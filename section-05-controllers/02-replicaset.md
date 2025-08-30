# 12. ReplicaSet

## The Modern Replication Controller

ReplicaSet is the next-generation ReplicationController with enhanced selector capabilities and better integration with higher-level controllers like Deployments.

## What is a ReplicaSet?

A ReplicaSet (RS) is a Kubernetes object that:
- **Maintains pod stability:** Ensures a specified number of identical pods are running
- **Uses advanced selectors:** Supports set-based label selection (more powerful than RC)
- **Works with Deployments:** Serves as the building block for Deployment objects
- **Provides self-healing:** Automatically replaces failed pods

## ReplicaSet vs. ReplicationController

| Feature | ReplicationController | ReplicaSet |
|---------|---------------------|------------|
| API Version | v1 | apps/v1 |
| Selector Type | Equality-based | Set-based |
| Rolling Updates | Not supported | Through Deployments |
| Recommended Use | Legacy systems | New deployments |

## ReplicaSet Manifest

### Basic Example
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: frontend-rs
  labels:
    app: frontend
    tier: webserver
spec:
  replicas: 3
  selector:
    matchLabels:
      app: frontend
    matchExpressions:
      - {key: tier, operator: In, values: [webserver]}
  template:
    metadata:
      labels:
        app: frontend
        tier: webserver
    spec:
      containers:
      - name: nginx
        image: nginx:1.21
        ports:
        - containerPort: 80
        resources:
          requests:
            memory: "64Mi"
            cpu: "250m"
          limits:
            memory: "128Mi"
            cpu: "500m"
Key Components
apiVersion: apps/v1 (different from ReplicationController's v1)

spec.selector: Supports both matchLabels and matchExpressions for advanced selection

spec.template: Pod template (same as ReplicationController)

Set-Based Selectors
ReplicaSet introduces powerful set-based selection requirements:

matchLabels (equality-based)
yaml
selector:
  matchLabels:
    app: frontend
    environment: production
matchExpressions (set-based)
yaml
selector:
  matchExpressions:
    - {key: environment, operator: In, values: [production, staging]}
    - {key: tier, operator: NotIn, values: [backend]}
    - {key: version, operator: Exists}
Supported Operators
In: Label's value must match one of the specified values

NotIn: Label's value must not match any specified values

Exists: Label must exist (regardless of value)

DoesNotExist: Label must not exist

Working with ReplicaSets
Create a ReplicaSet
bash
kubectl apply -f replicaset.yaml
Check Status
bash
kubectl get replicasets
kubectl describe rs frontend-rs
Scale ReplicaSet
bash
# Imperative scaling
kubectl scale rs frontend-rs --replicas=5

# Declarative scaling (edit and apply)
kubectl edit rs frontend-rs
Delete ReplicaSet
bash
# Delete ReplicaSet but leave pods
kubectl delete rs frontend-rs --cascade=orphan

# Delete ReplicaSet and all pods (default)
kubectl delete rs frontend-rs
How ReplicaSet Works
Owner References
ReplicaSet uses owner references to track pod ownership:

bash
# View pod owner references
kubectl get pod <pod-name> -o jsonpath='{.metadata.ownerReferences[0].name}'
Pod Management
Creates pods using the provided template

Continuously monitors pod count and health

Deletes excess pods if replica count is reduced

Recreates pods if they fail or are deleted

Practical Example: Advanced Selection
Step 1: Create ReplicaSet with Complex Selector
yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: complex-selector-rs
spec:
  replicas: 2
  selector:
    matchLabels:
      app: demo
    matchExpressions:
      - {key: environment, operator: In, values: [dev, test]}
      - {key: ready, operator: NotIn, values: [false]}
  template:
    metadata:
      labels:
        app: demo
        environment: dev
        ready: "true"
    spec:
      containers:
      - name: busybox
        image: busybox
        command: ['sh', '-c', 'echo "Running"; sleep 3600']
Step 2: Test Selector Behavior
bash
# Create pods with different labels to see which get managed
kubectl run test-pod --image=busybox --labels="app=demo,environment=prod" --command -- sleep 3600
Best Practices
Use with Deployments: Prefer Deployments over bare ReplicaSets for update capabilities

Meaningful labels: Use consistent and descriptive labeling strategy

Resource management: Always specify resource requests and limits

Health checks: Implement liveness and readiness probes in pod template

Namespace awareness: Use appropriate namespaces for better organization

Common Use Cases
Stable microservices: Services that don't need frequent updates

Background workers: Worker pools that process queues

Stateful applications: When combined with other controllers (though StatefulSet is better)

Learning foundation: Understanding the basis for more complex controllers

Up Next: Deployments
