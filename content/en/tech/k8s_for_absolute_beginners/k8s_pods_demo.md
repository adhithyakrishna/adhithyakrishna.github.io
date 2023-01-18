---
title: "Demo - Voting application 1"
date: 2022-11-11T11:08:38-08:00
draft: false
description : "Kubernetes services"
meta_image: tech/k8s_for_absolute_beginners/k8s.png
image: tech/k8s_for_absolute_beginners/k8s.png
weight : 35
---
{{< featuredImage >}}

This notes is for the course [Kuberenetes for absolute beginners](https://www.udemy.com/course/learn-kubernetes/)


#### Application flow
{{< img src=/tech/k8s_for_absolute_beginners/Clipboard_2022-10-30-15-48-51.png title="K8s Pods demo" caption="https://www.udemy.com/course/learn-kubernetes/" alt="K8s nodes" width="700px" position="center" >}}


#### Voting application

We create pods and expose the container port. Please note that all the docker images are pre-built and this demo is to understand how different microservices communicate with each other through k8s services.

Create a definition for pod ```voting-app-pod.yml```
```
apiVersion: v1
kind: Pod
metadata:
  name: voting-app-pod
  labels:
    name: voting-app-pod
    app: demo-voting-app
spec:
  containers:
    - name: voting-app
      image: dockersamples/examplevotingapp_vote
      imagePullPolicy: IfNotPresent
      ports:
        - containerPort: 80
  restartPolicy: Always
```

Create a Loadbalancer service to expose the application to external world ```voting-app-service.yml```

```
apiVersion: v1
kind: Service
metadata:
  name: voting-service
  labels:
    name: voting-service
    app: demo-voting-app
spec:
  ports:
    - port: 80
      targetPort: 80
  selector:
    name: voting-app-pod
    app: demo-voting-app
  type: LoadBalancer
```

#### Result application

Create a definition for a pod ```result-app-pod.yml```
```
apiVersion: v1
kind: Pod
metadata:
  name: result-app-pod
  labels:
    name: result-app-pod
    app: demo-voting-app
spec:
  containers:
    - name: result-app
      image: dockersamples/examplevotingapp_result
      imagePullPolicy: IfNotPresent
      ports:
        - containerPort: 80
  restartPolicy: Always
```

Create a Loadbalancer service to expose the application to external world ```result-app-service.yml```

```
apiVersion: v1
kind: Service
metadata:
  name: result-service
  labels:
    name: result-service
    app: demo-voting-app
spec:
  ports:
    - port: 80
      targetPort: 80
  selector:
    name: result-app-pod
    app: demo-voting-app
  type: LoadBalancer
```

#### Redis application

Create a definition for a pod ```redis-pod.yml```
```
apiVersion: v1
kind: Pod
metadata:
  name: redis-pod
  labels:
    name: redis-pod
    app: demo-voting-app
spec:
  containers:
    - name: redis
      image: redis
      imagePullPolicy: IfNotPresent
      ports:
        - containerPort: 6379
  restartPolicy: Always
```

Create a NodePort service for postgres ```redis-service.yml```
```
apiVersion: v1
kind: Service
metadata:
  name: redis
  labels:
    name: redis-service
    app: demo-voting-app
spec:
  ports:
    - port: 6379
      targetPort: 6379
  selector:
    name: redis-pod
    app: demo-voting-app
  type: NodePort
```

{{< boxmd >}}
Services are created so that the pods can communicate to each other.

Services should be named based on what other applications are looking for. The  ```name``` here is set to db because the application looks for service named ```db``` (you can see that in the code).
{{< /boxmd >}}

#### Postgres application

Create a definition for a pod ```postgres-pod.yml```
```
apiVersion: v1
kind: Pod
metadata:
  name: postgres-pod
  labels:
    name: postgres-pod
    app: demo-voting-app
spec:
  containers:
    - name: postgres
      image: postgres:9.4
      imagePullPolicy: IfNotPresent
      env:
        - name: POSTGRES_USER
          value: "postgres"
        - name: POSTGRES_PASSWORD
          value: "postgres"
        - name: POSTGRES_HOST_AUTH_METHOD
          value: trust
      ports:
        - containerPort: 5432
  restartPolicy: Always
```

Create a NodePort service for postgres ```postgres-service.yml```
```
apiVersion: v1
kind: Service
metadata:
  name: db
  labels:
    name: db-service
    app: demo-voting-app
spec:
  ports:
    - port: 5432
      targetPort: 5432
  selector:
    name: postgres-pod
    app: demo-voting-app
  type: NodePort
```


#### Worker application

Note that thess pod is needed just for internal communication.

Create a definition for a pod ```worker-app-pod.yml```
```
apiVersion: v1
kind: Pod
metadata:
  name: worker-app-pod
  labels:
    name: worker-app-pod
    app: demo-voting-app
spec:
  containers:
    - name: worker-app
      image: dockersamples/examplevotingapp_worker
      imagePullPolicy: IfNotPresent
  restartPolicy: Always
```

#### Output

{{< img src=/tech/k8s_for_absolute_beginners/Clipboard_2022-10-30-15-55-02.png title="K8s Pods demo" caption="https://www.udemy.com/course/learn-kubernetes/" alt="K8s nodes" width="700px" position="center" >}}

#### Application communication (in docker)

{{< boxmd >}}
Note that links is deprecated but this gives an idea of how the applications communicate internally.
{{< /boxmd >}}

{{< img src=/tech/k8s_for_absolute_beginners/Clipboard_2022-10-30-15-56-46.png title="K8s Pods demo" caption="https://www.udemy.com/course/learn-kubernetes/" alt="K8s nodes" width="700px" position="center" >}}