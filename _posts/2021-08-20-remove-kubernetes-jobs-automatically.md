---
layout: post
title:  "How to remove Completed Kubernetes Jobs or CronJobs?"
date:   2021-08-20 00:11 +0100
categories: shorts
tags:
- kubernetes
- documentation
---

This is possible from version 1.12 Alpha with `ttlSecondsAfterFinished`. An example from [Clean Up Finished Jobs Automatically][ref]:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: pi-with-ttl
spec:
  ttlSecondsAfterFinished: 100
  template:
    spec:
      containers:
      - name: pi
        image: perl
        command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
      restartPolicy: Never
```

[ref]: https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/#clean-up-finished-jobs-automatically
