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

The following sections assume that `kubectl` and `helm` are installed on your 
machine, and that there is an IBM Kubernetes cluster already running. 

The IBM [Clusters tutorial][1] provides instructions for creating a cluster and
installing `kubectl` and `ibmtools` on your machine.

Follow the official [Helm documentation][2] to install `helm` on your machine
(scroll down to find your operating system).
 
You will also need to configure `helm` with a Chart repository, so that you can
download the charts we will be using in this guide.

```shell script
# Add the official Helm repository and update
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
helm repo update
```

At the end of this section, you should have

1.An IBM Kubernetes cluster
1. `kubectl` installed and pointing to your cluster
1. `helm` installed

Next, we will install Superset with default credentials and the necessary configuration.

## Install

We are ready to install Superset. The following command will create a 
Superset deployment, and mount a new Persistent Volume to it. You can disable
persistence by omitting the `persistence.enabled` flag, but then you might
have trouble getting the default login credentials to work.

```shell script
helm install --set persistence.enabled=true stable/superset --name SupersetDemo
# alternatively, use --generate-name to generate a random name 
```


Your output should look like this:

```
NAME: superset-demo
LAST DEPLOYED: Mon Oct  5 02:33:17 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
Superset can be accessed via port 9000 on the following DNS name from within your cluster:
superset-demo.default.svc.cluster.local

Initially you can login with username/password: admin/admin.
```

Initialization will take a few minutes. You can monitor the progress by watching the pod's logs:

```shell script
kubectl get pods
# Get the pod name. It will be prefixed by the deployment name, in this example this is 'superset-demo'
kubectl logs -f superset-demo-aaabbbccc
```

At this point we cannot connect to the Superset deployment from outside the cluster,
but we can start do port forwarding via kubectl:

```shell script
kubectl port-forward svc/superset-demo 8080:9000
```

This forwards all traffic from local port 8080 to the `superset-demo` service at port 9000. Go to [localhost:8080](localhost:8080)


### Problems
SQLite database is not created, keeps throwing errors on the logs


[1]: https://cloud.ibm.com/docs/containers?topic=containers-cs_cluster_tutorial
[2]: https://helm.sh/docs/intro/install/
