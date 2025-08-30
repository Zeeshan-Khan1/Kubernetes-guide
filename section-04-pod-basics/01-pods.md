# 9. Pods

## The Smallest Deployable Unit in Kubernetes

A Pod is the basic execution unit of a Kubernetes application—the smallest and simplest unit in the Kubernetes object model that you create or deploy.

## What is a Pod?

- **Atomic unit:** The smallest deployable unit in Kubernetes
- **One or more containers:** A wrapper for one or more tightly coupled containers
- **Shared context:** Containers in a Pod share:
  - Network namespace (same IP address and port space)
  - Storage volumes
  - Linux namespace, cgroups, and other isolation aspects

## Why Pods Instead of Direct Containers?

Pods provide a higher-level abstraction that:

1. **Group co-located containers** that need to work together
2. **Share resources** between tightly coupled application components
3. **Simplify deployment** of multi-container applications
4. **Enable better resource management** for related processes

## Pod Specifications

### Basic Pod Manifest
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
  labels:
    app: my-app
    tier: frontend
spec:
  containers:
  - name: web-container
    image: nginx:1.21
    ports:
    - containerPort: 80
  - name: logger-container
    image: busybox
    command: ['sh', '-c', 'while true; do echo "$(date) Log message"; sleep 5; done']
Key Specification Fields
Metadata:

name: Unique identifier for the Pod

labels: Key-value pairs for identifying and selecting Pods

annotations: Non-identifying metadata for tools and libraries

Specification:

containers: List of containers to run in the Pod

volumes: Storage that can be accessed by containers in the Pod

restartPolicy: Always, OnFailure, or Never

nodeSelector: Constrain Pod to run on specific nodes

Pod Lifecycle
Phase States
Pending: Pod has been accepted, but containers are not running

Running: Pod is bound to a node, and all containers are created

Succeeded: All containers have terminated successfully

Failed: At least one container terminated in failure

Unknown: Pod state could not be obtained

Container States
Waiting: Container is performing setup operations

Running: Container is executing successfully

Terminated: Container has completed execution

Multi-Container Pod Patterns
1. Sidecar Pattern
Extends and enhances the main container (e.g., logging, monitoring, syncing)

2. Adapter Pattern
Standardizes and normalizes output (e.g., format conversion, metrics aggregation)

3. Ambassador Pattern
Proxy network connections to the main container (e.g., service discovery, connection pooling)

Pod Management
Creating Pods
bash
# From YAML file
kubectl apply -f pod.yaml

# Imperative command
kubectl run my-pod --image=nginx --port=80
Monitoring Pods
bash
# Get all pods
kubectl get pods

# Get detailed information
kubectl describe pod <pod-name>

# View logs
kubectl logs <pod-name> [-c <container-name>]
Deleting Pods
bash
kubectl delete pod <pod-name>
kubectl delete -f pod.yaml
Best Practices
One process per container: Keep containers focused on a single purpose

Use labels effectively: Implement consistent labeling strategy

Set resource limits: Always specify resource requests and limits

Implement health checks: Use liveness and readiness probes

Use Deployments: For production, use Deployments instead of bare Pods

Common Pod Issues
Image pull errors: Check image name and registry access

CrashLoopBackOff: Application crashing on startup

Pending state: Insufficient resources or scheduling constraints

ContainerCreating: Slow image pulls or volume mounting issues

Up Next: Demo: Pods

text

### 7. section-04-pod-basics/02-demo-pods.md
```markdown
# 10. Demo: Pods

**Estimated Duration:** 17 minutes

## Hands-on Pod Management

This practical demonstration will guide you through creating, managing, and troubleshooting Pods in a Kubernetes cluster.

## Prerequisites

- A running Kubernetes cluster (from Section 3)
- `kubectl` configured to communicate with your cluster
- Basic familiarity with command line operations

## Demo 1: Creating Your First Pod

### Step 1: Create a Pod Manifest
Create a file named `simple-pod.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
    environment: demo
spec:
  containers:
  - name: nginx-container
    image: nginx:1.21
    ports:
    - containerPort: 80
Step 2: Apply the Manifest
bash
kubectl apply -f simple-pod.yaml
Step 3: Verify Pod Creation
bash
kubectl get pods
kubectl get pods -o wide
kubectl describe pod nginx-pod
Demo 2: Multi-Container Pod
Step 1: Create Multi-Container Pod Manifest
Create multi-container-pod.yaml:

yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-container-pod
spec:
  containers:
  - name: web-server
    image: nginx:1.21
    ports:
    - containerPort: 80
  - name: log-tailer
    image: busybox
    command: ['sh', '-c', 'tail -n+1 -f /var/log/nginx/access.log']
    volumeMounts:
    - name: nginx-logs
      mountPath: /var/log/nginx
  volumes:
  - name: nginx-logs
    emptyDir: {}
Step 2: Create and Verify
bash
kubectl apply -f multi-container-pod.yaml
kubectl get pods
kubectl describe pod multi-container-pod
Step 3: Access Container Logs
bash
# View logs from specific container
kubectl logs multi-container-pod -c web-server
kubectl logs multi-container-pod -c log-tailer
Demo 3: Pod Interaction and Debugging
Step 1: Execute Commands in Running Pods
bash
# Get a shell in the container
kubectl exec -it nginx-pod -- /bin/bash

# Run a specific command
kubectl exec nginx-pod -- nginx -v
Step 2: Port Forwarding for Local Access
bash
# Forward local port 8080 to pod port 80
kubectl port-forward nginx-pod 8080:80

# In another terminal, test access
curl http://localhost:8080
Step 3: Resource Monitoring
bash
# Watch pod status in real-time
kubectl get pods --watch

# View resource usage
kubectl top pods
Demo 4: Troubleshooting Common Issues
Scenario 1: Image Pull Errors
bash
# Create a pod with invalid image
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: bad-image-pod
spec:
  containers:
  - name: test-container
    image: nonexistent-image:latest
EOF

# Check pod status and events
kubectl describe pod bad-image-pod
Scenario 2: CrashLoopBackOff
bash
# Create a pod that will crash
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: crashing-pod
spec:
  containers:
  - name: crash-container
    image: busybox
    command: ['sh', '-c', 'echo "Crashing immediately"; exit 1']
EOF

# Investigate the issue
kubectl describe pod crashing-pod
kubectl logs crashing-pod --previous
Demo 5: Cleanup and Management
Step 1: Delete Pods
bash
# Delete individual pods
kubectl delete pod nginx-pod
kubectl delete pod multi-container-pod

# Delete multiple pods by selector
kubectl delete pods -l environment=demo

# Delete all pods in namespace (use with caution!)
kubectl delete pods --all
Step 2: Verify Cleanup
bash
kubectl get pods
kubectl get all
Practical Exercises
Create a Pod that runs a Redis database with persistent storage

Build a Pod with a main application and a sidecar for logging

Troubleshoot a Pod that's stuck in "ContainerCreating" state

Implement resource limits for CPU and memory in your Pods

Key Takeaways
Pods are the fundamental building blocks in Kubernetes

Multi-container Pods enable powerful patterns like sidecars and adapters

kubectl provides comprehensive tools for Pod management

Understanding Pod states is crucial for troubleshooting

Always use Deployments for production workloads instead of bare Pods

Next Steps
Practice creating different types of Pods and experiment with various configurations. In the next section, we'll learn about Deployments for managing Pod lifecycles.

