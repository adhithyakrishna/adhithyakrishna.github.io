---
title: "Introduction"
date: 2022-10-23T15:35:28-07:00
draft: false
description : "Notes for kubernetes course"
meta_image: notes/k8s_for_absolute_beginners/k8s.png
image: notes/k8s_for_absolute_beginners/k8s.png
weight : 5
---

{{< featuredImage >}}

This notes is for the course [Kuberenetes for absolute beginners](https://www.udemy.com/course/learn-kubernetes/)

## Introduction

**Container orchestration system = Container + Orchestration**

#### What are containers

1) Containers have their own processes, network and mounts
2) Multiple containers can share the underlying operating system kernel.

Docker (most popular container technology).

##### Problems before containers
1) Application component and services being incompatible with underlying OS.
2) Compatibility between services, libraries and dependencies on the OS.
3) Compatability checks had to be make during every component upgrade AKA matrix from hell.
4) Onboarding a new developer / setting up a local instance was difficult.

##### With docker
1) Each component can run in its own container with its own libraries and dependencies.
2) Docker is compatible with any operating systems. 
3) Onboarding a new developer / setting up a local instance is very easy.

#### Os components and responsibilities
All operating sytems consists of two important components
1) OS Kernel
2) Software

Os kernel is responsible for interacting with underlying hardware. Custom software differentiates operating systems from each other. **Docker container** shares the underlying kernel of docker host. Docker is not meant to virualize and run different operating systems on the same hardware. The main purpose is to containerize and ship them.


#### Docker vs Virtual machines

{{< img src=/notes/k8s_for_absolute_beginners/Clipboard_2022-10-23-12-05-47.png title="Containers vs Virtual Machine" caption="https://www.udemy.com/course/learn-kubernetes/" alt="container vs Vm" width="700px" position="center" >}}

**In case of docker we have**
1) Underlying hardware infrastructure.
2) Operating system
3) Docker installed on the OS (which is responsible for managing the containers that run with libraries and dependencies).

**In case of virual machine**
1) Underlying hardware infrastructure
2) Operating system
3) Hypervisor (ESX or virtualization)
4) Virtual machine
5) Virual machine has its own OS inside
6) Dependencies
7) Application

The overhead causes higher utilization of underlying resources because there are multiple operating systems and kernel running. The Virtual machine is heavy and consume high disk space (Gigabytes) whereas Docker containers a re light weight and are usually mega bytes in size. 

Docker containers boot up faster (within seconds). VM takes minutes to boot up as it needs to boot up the entire OS.

Docker has less isolation as more resources (like kernel) are shared between containers.
For VMs, there is complete isolation. Since VM does not directly rely on underlying OS or kernel, we can run different OS such as linux / windows based on same hypervisor.

{{< box >}}
"Hypervisors and containers are used for different purposes. Hypervisors are used to create and run virtual machines (VMs), which each have their own complete operating systems, securely isolated from the others. In contrast to VMs, containers package up just an app and its related services. This makes them more lightweight and portable than VMs, so they are often used for fast and flexible application development and movement." 
Reference : https://www.vmware.com/topics/glossary/content/hypervisor.html?resource=cat-1023790256#cat-1023790256
{{< /box >}}

#### Image vs Containers

An image is a package or a template that is used to create one or more containers.
Containers are running instances of that image that are isolated and have their own **environments and set of processes.**

#### Advantage of containers
Traditonally, developers developed applications and hand it over to Ops team to deploy and manage it in production environments along with some instructions. If they hit an issue, they would have to work with developers to resolve it.

With docker, major portion of this infrastructure setup is now in the hands of developers in form of Docker file. The instructions that were put to gether previously (handed off to the ops team) can now put together easily into a Dockerfile (to create an image for their application). The image can run on any container platform and is **guaranteed to run the same way everywhere**. Ops team can now use the image to deploy the application. Since OPS team are not modifying it, it continues to work the same when deployed in production.