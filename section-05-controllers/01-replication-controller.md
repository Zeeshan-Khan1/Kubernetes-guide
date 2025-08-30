# 11. Replication Controller

## The Original Pod Manager

ReplicationController is a legacy Kubernetes object that ensures a specified number of pod replicas are running at any given time.

## What is a ReplicationController?

A ReplicationController (RC) is a controller that:
- **Maintains pod availability:** Ensures exactly N replicas of a pod are running
- **Provides self-healing:** Automatically replaces failed or terminated pods
- **Enables scaling:** Allows easy scaling of pod replicas up or down
- **Handles node failures:** Reschedules pods from failed nodes to healthy ones

## Why ReplicationController Matters

While largely replaced by ReplicaSet and Deployment, understanding ReplicationController is important because:
- It demonstrates the fundamental Kubernetes controller pattern
- Many existing systems still use ReplicationControllers
- It helps understand the evolution to more advanced controllers

## ReplicationController Manifest

### Basic Example
```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: nginx-rc
spec:
  replicas: 3
  selector:
    app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.21
        ports:
        - containerPort: 80
Key Components
spec.replicas: The desired number of pod replicas (default: 1)

spec.selector: Label selector that identifies pods managed by this RC

spec.template: Pod template used to create new replicas (same as Pod spec)

Working with ReplicationControllers
Create a ReplicationController
bash
kubectl apply -f rc.yaml
Check Status
bash
kubectl get replicationcontrollers
kubectl describe rc nginx-rc
Scale Manually
bash
kubectl scale rc nginx-rc --replicas=5
Delete ReplicationController (with or without pods)
bash
# Delete RC but leave pods running
kubectl delete rc nginx-rc --cascade=orphan

# Delete RC and all managed pods (default)
kubectl delete rc nginx-rc
How ReplicationController Works
Reconciliation Loop
Observe: Check current number of pod replicas with matching labels

Compare: Compare actual state with desired state (spec.replicas)

Act:

If too few pods → create new pods using template

If too many pods → delete excess pods

If correct number → do nothing

Pod Selection
ReplicationController uses equality-based selector requirements:

Must match ALL specified label key-value pairs exactly

Does not support set-based selectors (like ReplicaSet)

Limitations of ReplicationController
Basic selector functionality: Only equality-based selection

No rolling update capability: Cannot perform gradual updates

Legacy status: Being phased out in favor of ReplicaSet

Limited rollout features: No version tracking or rollback support

Migration to ReplicaSet
While ReplicationController still works, it's recommended to use ReplicaSet for new deployments:

yaml
# ReplicaSet equivalent
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.21
        ports:
        - containerPort: 80
Practical Example: Self-Healing Demo
Step 1: Create ReplicationController
bash
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: ReplicationController
metadata:
  name: webapp-rc
spec:
  replicas: 3
  selector:
    app: webapp
  template:
    metadata:
      labels:
        app: webapp
    spec:
      containers:
      - name: webapp
        image: nginx:1.21
        ports:
        - containerPort: 80
EOF
Step 2: Verify Pods are Running
bash
kubectl get pods -l app=webapp
Step 3: Simulate Pod Failure
bash
# Delete one pod manually
kubectl delete pod <pod-name>

# Watch ReplicationController create replacement
kubectl get pods -l app=webapp --watch
Best Practices
Use for simple cases: Only if you need basic replication without updates

Consistent labeling: Ensure template labels match selector labels

Resource limits: Always set resource requests and limits in pod template

Plan migration: Consider migrating to ReplicaSet for future compatibility

Up Next: ReplicaSet
