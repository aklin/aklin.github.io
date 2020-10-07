---
layout: post
title:  "Installing Apache Superset on IBM Kubernetes Cluster"
date:   2020-10-05
categories: guides kubernetes
tags:
- kubernetes
- ibm
- helm
draft: true
---

## Before you begin

The following sections assume that all necessary CLI tools are installed, and
there is an IBM Kubernetes cluster already running. The following CLI tools
are also required:

1. `kubectl`
1. `ibmtools`
1. `helm`

The IBM [Clusters tutorial][1] provides instructions for creating a cluster and
installing `kubectl` and `ibmtools` on your machine.

The official [Helm documentation][2] will guide you through installing `helm`
on your machine (scroll down to find your operating system).

You will also need to configure `helm` with a Chart repository, so that you can
download the charts we will be using in this guide.

```bash
# Add the official Helm repository and update
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
helm repo update
```


## Install

We are ready to install Superset. We will create a custom StorageClass to
make Superset work with with IBM Cloud. We will also create a Persistent
Volume Claim (PVC) to be used by the Superset pod.

In the following sections we will first create the PVC and then move on to
the Helm chart.

### Create StorageClass and PersistentVolumeClaim

We will copy the default class `ibmc-file-gold` to make it use a specified
Group ID (GID), so that the pod can write to the volume after mounting.

In the same file we will define the PersistentVolumeClaim (PVC) which the
Superset pod will mount.

Save the following as `superset-sc-pvc.yaml`:

```yaml
# Copy of ibmc-file-gold, with a fixed GID
apiVersion: storage.k8s.io/v1beta1
kind: StorageClass
metadata:
  name: ibmc-file-gold-superset
  labels:
    kubernetes.io/cluster-service: "true"
provisioner: ibm.io/ibmc-file
reclaimPolicy: Delete
volumeBindingMode: Immediate
parameters:
  billingType: hourly
  classVersion: "2"
  iopsPerGB: "10"
  sizeRange: '[20-4000]Gi'
  type: Endurance
# Note: `superset` group ID is 1001
  gidAllocate: "true"
  gidFixed: "1001"

---

apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  annotations:
    volume.beta.kubernetes.io/storage-provisioner: ibm.io/ibmc-file
  finalizers:
    - kubernetes.io/pvc-protection
  labels:
    app: superset
    region: eu-gb
    zone: lon02
  name: superset-pvc
  namespace: default
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 8Gi
  storageClassName: ibmc-file-gold
  volumeMode: Filesystem
```

And create the resource:

```bash
kubectl apply -f superset-sc-pvc.yaml
```

Verify that the resources were created correctly by using `describe`:

```bash
kubectl describe storageclass ibmc-file-gold-superset
kubectl describe pvc superset-pvc
```

>Note: Some errors might not show up until the pod has claimed the PVC. Keep
>this in mind in case you need to troubleshoot later.

Wait a few minutes until the cluster has provisioned the Persistent Volume.

We now have a Persistent Volume ready to be used by Superset. In the next
section we will download and modify the official Helm chart to get our
deployment running.

### Modifying the Helm Chart

Run the following command to download the Superset Chart locally:

```bash
helm pull stable/superset --untar
```

You should now have a local directory named `superset` containing the
Superset chart definition.

```bash
# Change to the Chart directory
cd superset
helm install \
  --set persistence.enabled=true \
  --set persistence.existingClaim=superset-pvc \
  superset \
  ./
```


Your output should look like this:

```
NAME: superset
LAST DEPLOYED: Wed Oct  7 15:19:07 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
Superset can be accessed via port 9000 on the following DNS name from within your cluster:
superset.default.svc.cluster.local

Initially you can login with username/password: admin/admin.
```

Initialization will take a few minutes. You can monitor progress by watching the pod's logs:

```bash
kubectl get pods
# Get the pod name. In this example it is `superset-798c9c7fbd-k9lsr`
kubectl logs -f superset-798c9c7fbd-k9lsr
```

You have now finished setup.

### Set admin credentials

Start a shell inside the Superset container

```bash
kubectl exec --stdin --tty superset-798c9c7fbd-k9lsr /bin/bash
```

In the container shell, type the following to reset the admin credentials:

```bash
export FLASK_APP=superset
flask fab create-admin superset
```

You will need to enter the new credentials interactively. When finished, the output
should look like this:

```
Recognized Database Authentications.
Admin User admin created.
```

That means your admin credentials have been reset. You're ready to connect to
the web UI.

### Connect to Web UI

Finally, start a port-forwarding session via kubectl to connect to the login page.

```bash
kubectl port-forward svc/superset-demo 8080:9000
```

Visit [`localhost:8080`](http://localhost:8080) and use the credentials from the
previous step.


Congratulations, you have set up Superset on IBM Cloud!


[1]: https://cloud.ibm.com/docs/containers?topic=containers-cs_cluster_tutorial
[2]: https://helm.sh/docs/intro/install/
