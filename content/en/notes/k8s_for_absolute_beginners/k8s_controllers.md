---
title: "Kubernetes controllers"
date: 2022-11-03T23:45:59-07:00
draft: false
description : "Kuberentes controllers"
meta_image: notes/k8s_for_absolute_beginners/k8s.png
image: notes/k8s_for_absolute_beginners/k8s.png
weight : 25
---
{{< featuredImage >}}

This notes is for the course [Kuberenetes for absolute beginners](https://www.udemy.com/course/learn-kubernetes/)

#### Replication controller
1) Replication controller helps us run multiple instance of a pod in kubernetes cluster, thus providing high availability.
2) Replication controller is capable of ensuring that the specified number of pods are running at all times.
3) It is also capable of load balancing betweening multiple pods to share the loads. It can load balance  pods and can do it when pods are in different nodes as well. 
4) It allows us to scale the application when demand increases.

```
apiVersion: v1
kind: ReplicationController
metadata:
  name: myapp-rc
  labels:
    app: myapp
    type: front-end
spec:
  template:
    metadata:
      name: myapp-pod
      labels:
        app: myapp
        type: frontend
    spec:
      containers:
        - name: nginx-container
          image: nginx
  replicas: 3
```

```
> kubectl get replicationcontroller
```


#### Replica Set
Replication controller that we saw previously is being replaced by replica set. Replica Set is the recommended way to setup replication. 
One difference between Replication Controller and Replica Set is, Replica Set requires a selector definition to identify what pods fall under it. Replica Set will take those pods that are already deployed that match the selector definition when creating the replicas. 
Replica Set can also be used to monitor the existing pods and redploy if one of them fails.
Labeling our pods is essential because there may be hundreds of pods running in our cluster and we can use **labels and selectors** to identify the pods.
If one of the pods fails, Replica Set will use the configuration under the __spec__ property to re-deploy the pod.

 

```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: myapp-replicaset
  labels:
      app: myapp
      type: front-end
spec:
  template:
    metadata:
      name: myapp-pod
      labels:
        app: myapp
        type: front-end
    spec:
      containers:
        - name: nginx-container
          image: nginx
  replicas: 3
  selector:
    matchLabels:
      type: front-end
``` 

```
kubectl create -f replicaset-definition.yml

kubectl get replicaset

kubectl delete replicaset myapp-replicaset
```

##### Scaling the replicaset

One of the following commands can be used to scale up the replicaset

```
kubectl replace -f replicaset-definition.yml
kubectl scale --replicas=6 -f replicaset-definition.yml


kubectl scale --replicas=6 replicaset myapp-replicaset
```



#### Deployments
1) Deployment provides declarative updates for Pods and RelicaSets
2) When you describe a desired state in a deployment, the deployment controller is capable of changing the actual state to the desired state at a controlled rate.
3) You can define Deployments to create a new ReplicaSets or to remove existing Deployments and adopt all their resources with new Deployments.

Deployments have several interesting use cases which are listed in this page. https://kubernetes.io/docs/concepts/workloads/controllers/deployment/


##### Deployment lifecycle
First the deployment is created.
Deployment in turn creates the replicaset.
Replica Set has in turn creates pods.


**Kubectl describe deployment** command can be used to find more information about the deployment status.



```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-replicaset
  labels:
      app: myapp
      type: front-end
spec:
  template:
    metadata:
      name: myapp-pod
      labels:
        app: myapp
        type: front-end
    spec:
      containers:
        - name: nginx-container
          image: nginx
  replicas: 3
  selector:
    matchLabels:
      type: front-end
``` 

```
kubectl get all
```


##### Deployment Rollout and Versioning

Whenever a new deployment or upgrade an image to the existing deployment, a rollout is triggered. A rollout is the process of **gradually** deploying or upgrading your application containers. 
When a new deployment is created, it triggers a rollout. A new rollout creates a new Deployment revision (Version 1). When the application is upgraded later, a new deployment revision is created (Version 2).
This will help us keep track of the changes made to our deployment and enable us to rollback to the previous version of the deployment if necessary.

```
kubectl rollout status 
kubectl rollout history
```

##### Deployment Strategies
The default deployment strategy is **Rollout**. There is another strategy called **Recreate** which as the name implies, deletes all the pods and recreates them bringing the entire service down while doing it. **Rollout strategy** on the other hand, takes down the older version and brings back the newer version one by one. This way, the application never goes down and the upgrade is seamless.


To update the image of the running deployment ```kubectl set image deployment/myapp-deployment nginx-container=nginx:1.12-perl``` command can be used.


##### Deployment Upgrades
When a new deployment is created, it creates a ReplicaSet which inturn creates the number of Pods required to meet the number of replicas. When you upgrade your application, the kubernetes deployment creates a **NEW** replicaset under the hood and starts deploying the containers there. Taking down the pods in old Replica Set also follow the Rolling Update strategy.

##### Deployment rollbacks
```kubectl rollout undo``` followed by the name of the deployment will allow you to rollback to a previous version. The deployment will destroy the pods in new replicaset and bring back the older ones in the old replicaset.

```kubectl run nginx --image=nginx``` the kubectl run command actually creates a deployment. A replicaset and pods are automatically created in the backend.


##### List of useful commands
```
> kubectl create -f deployment definition.yml
> kubectl create -f deployment definition.yml --record
> kubectl get deployments
> kubectl apply -f deployment definition.yml
> kubectl set image deployment/myapp-deployment nginx=nginx:1.9.1
> kubectl rollout status deployment/myapp-deployment
> kubectl rollout history deployment/myapp-deployment
> kubectl rollout undo deployment/myapp deployment
```


