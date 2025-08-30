# 8. Installing Kubernetes using Kubeadm

## Production-Grade Cluster Setup

kubeadm is a tool built to provide `kubeadm init` and `kubeadm join` as best-practice "fast paths" for creating Kubernetes clusters.

## Prerequisites

### System Requirements
- Ubuntu 20.04+/CentOS 7+ (or other compatible Linux distribution)
- 2+ GB RAM per machine (recommended: 4GB+)
- 2+ CPUs per machine
- Full network connectivity between all machines
- Unique hostname, MAC address, and product_uuid for each node
- Certain ports open on all machines

### Required Ports
**Control Plane Node:**
- 6443 (Kubernetes API server)
- 2379-2380 (etcd server client API)
- 10250 (Kubelet API)
- 10259 (kube-scheduler)
- 10257 (kube-controller-manager)

**Worker Nodes:**
- 10250 (Kubelet API)
- 30000-32767 (NodePort Services)

## Installation Steps

### Step 1: Update System and Install Dependencies
```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg lsb-release
Step 2: Install Container Runtime (containerd)
bash
# Add Docker repository
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install containerd
sudo apt-get update
sudo apt-get install -y containerd.io

# Configure containerd
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml
sudo systemctl restart containerd
sudo systemctl enable containerd
Step 3: Install kubeadm, kubelet, and kubectl
bash
# Add Kubernetes repository
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

# Install Kubernetes components
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
Step 4: Initialize Control Plane Node
bash
# Initialize the cluster
sudo kubeadm init --pod-network-cidr=10.244.0.0/16

# Set up kubeconfig for regular user
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
Step 5: Install Pod Network Add-on (Flannel)
bash
kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
Step 6: Join Worker Nodes
bash
# On each worker node, run the join command from the init output
sudo kubeadm join <control-plane-host>:<port> --token <token> --discovery-token-ca-cert-hash <hash>
Step 7: Verify Cluster Status
bash
# Check nodes
kubectl get nodes

# Check all system components
kubectl get pods -n kube-system
Post-Installation Configuration
Remove Taints from Master Node (Optional)
bash
# Allow pods to run on control plane node (for single-node clusters)
kubectl taint nodes --all node-role.kubernetes.io/control-plane-
Install Kubernetes Dashboard (Optional)
bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
Troubleshooting Common Issues
Reset Failed Installation
bash
sudo kubeadm reset
sudo apt-get purge kubeadm kubectl kubelet kubernetes-cni
sudo rm -rf ~/.kube
Check System Configuration
bash
# Verify swap is disabled
sudo swapoff -a

# Check network connectivity
ping <other-node-ip>

# Verify container runtime
sudo systemctl status containerd
Best Practices
Use configuration files: Use kubeadm config for reproducible installations

Backup etcd: Regularly backup your cluster state

Keep systems updated: Regularly update Kubernetes and host OS

Monitor cluster health: Set up monitoring from the beginning

Secure your cluster: Apply security best practices and RBAC

Next Section: Section 4: Pod Basics
