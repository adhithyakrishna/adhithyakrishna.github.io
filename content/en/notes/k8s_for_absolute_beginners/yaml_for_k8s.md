---
title: "Yaml for kuberentes"
date: 2022-11-01T12:41:20-07:00
draft: false
description : "Yaml for kuberentes"
meta_image: notes/k8s_for_absolute_beginners/k8s.png
image: notes/k8s_for_absolute_beginners/k8s.png
weight : 20
---
{{< featuredImage >}}

This notes is for the course [Kuberenetes for absolute beginners](https://www.udemy.com/course/learn-kubernetes/)

#### Kubernetes definition file

A kuberentes definition file always contains 4 top level fields. These are required fields

```
apiVersion:
kind:
metadata:
.
.
.
spec:
```

1) apiVersion - Version of k8s API
2) kind - type of object (pod, replicaset, service, deployment)
3) metadata - data about the object (name and label), it is in the form of a dictionary. Here name is a string value and labels is a dictionary. Labels are useful to identify objects at a later point in time.
```
metadata:
  name: myapp-pod
  labels:
    app: myapp
```
4) spec - (specification) where we provide additional information to k8s pertaining to that object. Spec is a dictionary, we can add  properties to it, **containers** in this example, which is a list. The reason this property is a list is because the PODs can have multiple containers within them.

```
spec:
  containers:
    - name: nginx-container
      image: nginx
```


#### Few of the kubectl commands

```
kubectl create -f pod-definition.yml
kubectl get pods
kubectl describe pods
```