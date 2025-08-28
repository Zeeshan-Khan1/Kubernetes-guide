# 4. What is Kubernetes

## Definition

Kubernetes (often abbreviated as K8s) is an open-source container orchestration platform that automates the deployment, scaling, and management of containerized applications.

## Origin

Kubernetes was originally developed by Google based on their experience running production workloads at scale using their internal system called Borg. It was open-sourced in 2014 and is now maintained by the Cloud Native Computing Foundation (CNCF).

## Core Concepts

### Cluster
A Kubernetes cluster is a set of nodes that run containerized applications. It consists of:
- **Control Plane**: The brain that manages the cluster
- **Nodes**: Worker machines that run your applications

### Pod
The smallest deployable unit in Kubernetes, which can contain one or more containers.

### Deployment
A declarative way to manage Pods and ReplicaSets, enabling updates and rollbacks.

### Service
An abstraction that defines a logical set of Pods and a policy to access them.

### Namespace
A virtual cluster within a physical cluster, used for organizing resources and providing scope.

## Kubernetes Architecture

### Control Plane Components
- **kube-apiserver**: Frontend for the Kubernetes control plane
- **etcd**: Consistent and highly-available key value store
- **kube-scheduler**: watches for newly created Pods with no assigned node, and selects a node for them to run on
- **kube-controller-manager**: Runs controller processes
- **cloud-controller-manager**: Embeds cloud-specific control logic

### Node Components
- **kubelet**: An agent that runs on each node in the cluster
- **kube-proxy**: Maintains network rules on nodes
- **Container Runtime**: Software responsible for running containers

## Key Features

### Automatic Bin Packing
Kubernetes automatically places containers based on resource requirements and constraints, while not sacrificing availability.

### Self-Healing
Kubernetes restarts containers that fail, replaces containers, kills containers that don't respond to your user-defined health check, and doesn't advertise them to clients until they are ready to serve.

### Horizontal Scaling
Scale your application up and down with a simple command, with a UI, or automatically based on CPU usage.

### Service Discovery and Load Balancing
Kubernetes can expose a container using the DNS name or using their own IP address. If traffic to a container is high, Kubernetes is able to load balance and distribute the network traffic.

### Automated Rollouts and Rollbacks
You can describe the desired state for your deployed containers using Kubernetes, and it can change the actual state to the desired state at a controlled rate.

### Secret and Configuration Management
Kubernetes lets you store and manage sensitive information, such as passwords, OAuth tokens, and SSH keys.

## Kubernetes Ecosystem

The Kubernetes ecosystem includes:
- **Helm**: Package manager for Kubernetes
- **Prometheus**: Monitoring and alerting toolkit
- **Istio**: Service mesh for microservices
- **Knative**: Kubernetes-based platform to build, deploy, and manage serverless workloads
- **Operators**: Method of packaging, deploying, and managing a Kubernetes application

## Getting Started

To begin working with Kubernetes, you can:
1. Use Minikube for local development
2. Set up a cluster on cloud providers (GKE, EKS, AKS)
3. Use kind (Kubernetes in Docker) for testing
4. Explore managed Kubernetes services

## Next Steps

Now that you understand what Kubernetes is, we'll proceed to setting up your setting up Kubernetes Environment in the next section.

---
**Up Next:** [Kubernetes Architecture](./04-kubernetes-Architecture.md)
