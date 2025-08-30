# 6. Kubernetes Installation Methods

## Choosing Your Kubernetes Installation Path

There are multiple ways to set up Kubernetes, each suited for different use cases:

## 1. Local Development Environments

**Purpose:** Learning, development, and testing on a local machine.

**Options:**
- **Minikube:** Runs a single-node Kubernetes cluster inside a VM (ideal for beginners)
- **kind (Kubernetes in Docker):** Runs Kubernetes clusters using Docker containers
- **Docker Desktop:** Includes a single-node Kubernetes cluster (macOS/Windows)

**Best for:** Developers who need a local Kubernetes environment for testing.

## 2. Managed Kubernetes Services

**Purpose:** Production workloads without managing the control plane.

**Options:**
- **Google Kubernetes Engine (GKE)** - Google Cloud
- **Amazon Elastic Kubernetes Service (EKS)** - AWS
- **Azure Kubernetes Service (AKS)** - Microsoft Azure
- **DigitalOcean Kubernetes** - DigitalOcean

**Best for:** Production applications where you want to focus on workloads, not cluster management.

## 3. Custom Deployments

**Purpose:** Full control over the Kubernetes environment.

**Options:**
- **kubeadm:** The standard way to bootstrap a Kubernetes cluster
- **kops:** Kubernetes Operations - production-grade cluster management
- **kubespray:** Kubernetes deployment using Ansible playbooks

**Best for:** Custom requirements, air-gapped environments, or specific compliance needs.

## 4. Learning Platforms

**Purpose:** Quick, temporary clusters for learning and experimentation.

**Options:**
- **Play-With-Kubernetes (PWK):** Free browser-based Kubernetes environment
- **Katacoda:** Interactive learning platform with Kubernetes scenarios

**Best for:** Trying Kubernetes without any local installation.

## Recommendation for This Course

We'll use **Play-With-Kubernetes** for quick experiments and **kubeadm** for a more permanent, production-like setup.

---

**Up Next:** [Play-With-Kubernetes (PWK)](./02-play-with-kubernetes-pwk.md)
