---
title: "Kubernetes services"
date: 2022-11-05T19:10:37-07:00
draft: false
description : "Kubernetes services"
meta_image: tech/k8s_for_absolute_beginners/k8s.png
image: tech/k8s_for_absolute_beginners/k8s.png
weight : 30
---
{{< featuredImage >}}

This notes is for the course [Kuberenetes for absolute beginners](https://www.udemy.com/course/learn-kubernetes/)

Kubernetes service enable communication between various components within or ourside of the application. Kubernetes Services helps us connect applications with other applications or users.
Services enable connectivity between the group of pods. For example front-end to the users, connection between the frontend and backend processes and backend to to external data source.
Services enable **Loose coupling** between microservices in our application.

#### Node Port
One of the use case of Nodeport is to listen to a port on the Node and forward requests on that port to a port on the pod running the web application.
This type of service is know as NodePort service because the service listens to a port on the Node and forwards requests to Pods.
NodePorts can only be in a valid range which is from 30000 to 32767.


{{< img src=/tech/k8s_for_absolute_beginners/Clipboard_2022-10-30-11-36-10.png title="K8s Services" caption="https://www.udemy.com/course/learn-kubernetes/" alt="K8s services" width="700px" position="center" >}}


Below terms are from the viewpoint of the service. The service is like a virtual server inside of the node. It has its own ip address.

1) TargetPort - The port on the Pod where the actual web server is running - (port 80). This is where the service forwards requests to.
2) Port - Port on the service itself. (Simply referred to as the port).
3) NodePort - Port on the node, which can be used to access the web server externally.

```
apiVersion: v1
kind: Service
metadata:
  name: spring-boot-service
  namespace: spring-boot
spec:
  selector:
    app: spring-boot-deployment
  ports:
    - port: 8080
      targetPort: 8080
      nodePort: 30009
  type: NodePort
```

Here the only mandatory field is port. The target port would be the same as port if not specified and nodePort value will be between the 30000 and 32767 range (automatically allocated if not specified).

We can do multiple port mappings within a single service.

*Labels and selectors* are used to map the pods to the service. When a service is created it looks for matching pods with the labels and finds 3 of them. The service then auto selects all 3 pods to forward the external requests coming from the user. No additional configuration is needed to make this happen.

This service acts as a built in load balancer to distribute the load across different pods.

{{< img src=/tech/k8s_for_absolute_beginners/Clipboard_2022-10-30-11-52-59.png title="K8s Services" caption="https://www.udemy.com/course/learn-kubernetes/" alt="K8s services" width="700px" position="center" >}}

Kubernetes creates a service that can **_spans across all the nodes_** in the cluster and maps the target port to the same nodeport on all the nodes in the cluster, without having to do any additional configurations.
This way the application can be accessed using the IP of any node by using the same port number.

To summarize – in ANY case whether it be a single pod in a single node, multiple pods on a single node, multiple pods on multiple nodes, the service is created exactly the same without having to do any additional steps during the service creation.
When PODs are removed or added the service is automatically updated making it highly flexible and adaptive. Once created, you won’t typically have to make any additional configuration changes.

#### ClusterIP

ClusterIP provides a single interface to **access pods in a group**. A service created for backend pods will help group all the backend pods and provide a single interface for the other pods to access this service, allowing to easily and effectively deploy a microservices based application on a Kubernetes cluster. Similarly, creating additional services for database layer allows the backend pods to access the database layer through this sergice. 

Each service gets an IP and name assigned to it inside the cluster and **that is the name that should be used** by the other pods to acccess the service. This type of service is known as ClusterIP. ClusterIP is the default type in kubernetes configuration. If we did not specify the type, ClusterIP would be assigned by default.

{{< img src=/tech/k8s_for_absolute_beginners/Clipboard_2022-10-30-13-25-30.png title="K8s Services" caption="https://www.udemy.com/course/learn-kubernetes/" alt="K8s services" width="700px" position="center" >}}

```
apiVersion: v1
kind: Service
metadata:
  name: back-end
spec:
  type: ClusterIP
  ports:
    - targetPort: 80
      port: 80
  selector:
    app: myapp
    type: back-end
```

#### LoadBalancer
Exposes the Service externally using a cloud provider's load balancer.
On cloud providers which support external load balancers, setting the type field to LoadBalancer provisions a load balancer for your Service.


```
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app.kubernetes.io/name: MyApp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376
  clusterIP: 10.0.171.239
  type: LoadBalancer
status:
  loadBalancer:
    ingress:
    - ip: 192.0.2.127
```



#### Differrence between different services

https://stackoverflow.com/questions/41509439/whats-the-difference-between-clusterip-nodeport-and-loadbalancer-service-types

Excerpts from the above stack overflow link

{{< box >}}
You can access a service from your load balancer's IP address, which routes your request to a nodePort, which in turn routes the request to the clusterIP port. You can acess this service as you would a NodePort or a ClusterIP service as well.
{{< /box >}}


A ClusterIP Service is part of a NodePort Service. A NodePort Service is Part of a Load Balancer Service.

```kubectl get services``` displays the loadbalancer as well as cluster-ip
```minikube service redis-service --url``` displays the node-ports.

{{< img src=/tech/k8s_for_absolute_beginners/Clipboard_2022-10-30-14-18-30.png title="K8s Services" caption="https://www.udemy.com/course/learn-kubernetes/" alt="K8s services" width="700px" position="center" >}}


**ClusterIP** - Exposes a service which is only accessible from within the cluster.
**NodePort** - Exposes a service via a static port on each node’s IP.
**LoadBalancer** - Exposes the service via the cloud provider’s load balancer.


Pods within the cluster can talk to each other through **clusterIP**.
To make a pod accessible from outside the cluster, it will create **nodePort**. Node port will make use of the clusterIP to do this.
Load balancer puts a loadbalancer in front so that the inbound traffic is distributed between node ports.

If you want to acccess the service from outside a cluster only Nodeport will be accessible and not clusterIP.

Additional reference: https://stackoverflow.com/a/72988866