# 7. Play-With-Kubernetes (PWK)

## Zero-Installation Kubernetes Learning

Play-With-Kubernetes (PWK) is a free, browser-based Kubernetes environment that provides temporary clusters for learning and experimentation.

## What is PWK?

- **Web-based interface:** No software installation required
- **Temporary clusters:** Sessions last for 4 hours
- **Real Kubernetes:** Uses real Docker containers running on real hardware
- **Multiple nodes:** Create multi-node clusters with a click
- **Pre-configured:** Comes with `kubectl` and necessary tools

## Getting Started with PWK

### Step 1: Access the Platform
1. Visit [labs.play-with-k8s.com](https://labs.play-with-k8s.com/)
2. Log in with your Docker Hub account (create one if needed)

### Step 2: Start a New Session
1. Click "Start" to begin a new session
2. Click "Add New Instance" to create nodes
3. Typically create 1 master and 2 worker nodes

### Step 3: Initialize Your Cluster
```bash
# On your master node
kubeadm init --apiserver-advertise-address $(hostname -i) --pod-network-cidr 10.5.0.0/16

# Set up kubeconfig
mkdir -p $HOME/.kube
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
chown $(id -u):$(id -g) $HOME/.kube/config

# Install a network plugin (Calico example)
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
Step 4: Join Worker Nodes
bash
# On each worker node, run the join command provided by kubeadm init
kubeadm join <master-ip>:6443 --token <token> --discovery-token-ca-cert-hash <hash>
PWK Interface Overview
Terminal access: Each node has its own terminal

Cluster visualization: See your node relationships

Session management: Access your cluster for 4 hours

Port exposure: Access services running in your cluster

Advantages of PWK
No setup required: Immediate access to real Kubernetes

Experiment freely: Try commands without affecting local systems

Learn cluster setup: Practice multi-node cluster configuration

Cost-free: No cloud provider costs

Limitations
Temporary: Clusters disappear after 4 hours

Resource limits: Limited CPU and memory per session

No persistence: All data is lost when session ends

Internet access required: Browser-based only

Best Practices for PWK
Document your work: Save important commands and outputs

Use multiple terminals: Open separate terminals for different nodes

Start fresh: Don't worry about mistakes - just create a new session

Experiment: Try different configurations and learn from errors

Up Next: Installing Kubernetes using Kubeadm
