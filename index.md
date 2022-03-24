---
title: Overview, intial setup
---

# Overview

This hands-on lab is based on the [Docker Getting Started Tutorial](https://github.com/docker/getting-started). 

In this Docker tutorial, a Node.js based ToDo app is packaged as a Container image and then run on Docker, first stand-alone using a built-in database, and then as a multi-container app with docker-compose, running MySQL as database in a second container.

![Agenda](docs/agenda.png)

In this lab, we will use the information in the Docker commands of the tutorial to create equivalent Kubernetes configurations. 

At one point in the Docker Getting Started Tutorial you publish the ToDo app container image to Docker Hub into your own repository. In this Kubernetes hands-on you are using my version of the getting-started image from my Docker Hub repository, but of course you can use your own.

The instructions in this lab are based on **Minikube**. They should work -- with some modifications -- with the Kubernetes that is part of Docker Desktop, with K3d, Kind, or any other Kubernetes distribution. Your mileage may vary ...

## Prerequisites

* [Docker](https://docs.docker.com/desktop/)
* [Minikube](https://minikube.sigs.k8s.io/docs/start/)
* [git](https://git-scm.com/downloads)
* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
* [stern](https://github.com/wercker/stern/releases) (rename stern_*_amd64[.exe] to stern[.exe])

All executables must be copied to a location in your PATH (e.g. for Windows copy to "C:\Windows\System32").

You may not know `stern`: it allows you to tail (follow) the logs of multiple pods on Kubernetes and multiple containers within the pod. It keeps displaying the logs when a pod is stopped and restarted. Each result is color coded for quicker debugging. 

## Get the code

```
$ git clone https://github.com/Harald-U/kubernetes-handson.git
$ cd kubernetes-handson
```

## Prepare Minikube

This is the minimum setup:

```
$ minikube start --cpus 2 --memory 4096 --driver docker
```

This starts a very small Minikube instance with 2 CPUs and a mere 4 GB of RAM. On Linux and MacOS, Docker is the recommended driver for Minikube. 

## NOTE: bwLehrpool 

There may be a "leftover" (and damaged) Minikube instance that was present when the VMware image for the Linux environment was built.  This may cause problems. Enter the following command before you start this workshop:

```
$ minikube delete
```

Output will be most likely something like this:

```
🔥  minikube" in docker wird gelöscht...
🔥  /home/student/.minikube/machines/minikube wird entfernt...
💀  Removed all traces of the "minikube" cluster.
```

Please be aware that this command will delete any existing Minikube cluster!

**bwLehrpool has sufficient RAM to increase memory for Minikube**, you can use this command instead:

```
$ minikube start --cpus 2 --memory 6144 --driver docker
```

which will assign 6 GB of RAM.

## Labs

The ToDo app we are going to deploy is based on Node.js. It can run "stand-alone" and will use a built in SQLite database then. Or it can be used together with MySQL. During the labs we add more and more Kubernetes features.

- [Lab 1](docs/lab1.md) - Deploy ToDo stand-alone
- [Lab 2](docs/lab2.md) - Deploy MySQL
- [Lab 3](docs/lab3.md) - Connect ToDo with MySQL using Environment Variables
- [Lab 4](docs/lab4.md) - MySQL with Persistent Volumes
- [Lab 5](docs/lab5.md) - Connect ToDo with MySQL using ConfigMap
- [Lab 6](docs/lab6.md) - Connect ToDo with MySQL using ConfigMap and Secret