# 13. Deployments

## The Modern Application Deployment Controller

Deployment is a higher-level abstraction that manages ReplicaSets and provides declarative updates, rolling deployments, and rollback capabilities for Pods.

## What is a Deployment?

A Deployment controller provides:
- **Declarative updates:** Describe desired state, and Deployment makes it happen
- **Rolling updates:** Update Pods with zero downtime
- **Rollback capabilities:** Revert to previous version if something goes wrong
- **Version tracking:** Maintain history of revisions for rollbacks
- **Scalability:** Easily scale your application up or down

## Deployment vs. ReplicaSet

| Feature | ReplicaSet | Deployment |
|---------|------------|------------|
| Updates | Manual pod replacement | Automated rolling updates |
| Rollbacks | Not supported | Built-in rollback capability |
| Version History | No | Yes (revision history) |
| Update Strategies | N/A | RollingUpdate or Recreate |

## Deployment Manifest

### Basic Example
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
  revisionHistoryLimit: 10
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
        image: nginx:1.21.0
        ports:
        - containerPort: 80
        livenessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 15
          periodSeconds: 20
        readinessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 5
          periodSeconds: 10
Key Components
spec.strategy: Update strategy (RollingUpdate or Recreate)

spec.revisionHistoryLimit: Number of old ReplicaSets to retain for rollbacks

spec.selector: Label selector for pods (same as ReplicaSet)

spec.template: Pod template for creating new replicas

Update Strategies
1. RollingUpdate (Default)
Gradually replaces old pods with new ones

Zero downtime during updates

Configurable surge and unavailable parameters

yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxSurge: 1        # Can be number or percentage
    maxUnavailable: 0  # Can be number or percentage
2. Recreate
Kills all old pods before creating new ones

Results in downtime during update

Useful for non-cluster-aware applications

yaml
strategy:
  type: Recreate
Working with Deployments
Create a Deployment
bash
kubectl apply -f deployment.yaml
Check Status
bash
kubectl get deployments
kubectl describe deployment nginx-deployment

# Watch rollout status
kubectl rollout status deployment nginx-deployment
Update Deployment
bash
# Update image version
kubectl set image deployment/nginx-deployment nginx=nginx:1.21.1

# Or edit deployment directly
kubectl edit deployment nginx-deployment
Scale Deployment
bash
kubectl scale deployment nginx-deployment --replicas=5
Rollback Deployment
bash
# Check revision history
kubectl rollout history deployment nginx-deployment

# Rollback to previous version
kubectl rollout undo deployment nginx-deployment

# Rollback to specific revision
kubectl rollout undo deployment nginx-deployment --to-revision=1
Deployment Lifecycle
Rollout Process
New ReplicaSet created with updated template

Gradual scale-up of new ReplicaSet while scaling down old one

Health checks ensure new pods are ready before continuing

Completion when all old pods are replaced

Rollback Process
Previous ReplicaSet scaled up

Current ReplicaSet scaled down

Revision history maintained for future rollbacks

Practical Example: Rolling Update
Step 1: Create Initial Deployment
bash
cat <<EOF | kubectl apply -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: webapp
  template:
    metadata:
      labels:
        app: webapp
    spec:
      containers:
      - name: webapp
        image: nginx:1.21.0
        ports:
        - containerPort: 80
EOF
Step 2: Perform Rolling Update
bash
# Update to new version
kubectl set image deployment/webapp-deployment webapp=nginx:1.21.1

# Watch the rollout progress
kubectl rollout status deployment/webapp-deployment

# Verify new pods are created
kubectl get pods -l app=webapp
Step 3: Test Rollback
bash
# Introduce a bad update
kubectl set image deployment/webapp-deployment webapp=nginx:invalid-tag

# Check rollout status (will show failure)
kubectl rollout status deployment/webapp-deployment

# Rollback to previous working version
kubectl rollout undo deployment/webapp-deployment

# Verify recovery
kubectl get pods -l app=webapp
Advanced Deployment Features
Pausing and Resuming Rollouts
bash
# Pause rollout for multiple changes
kubectl rollout pause deployment/webapp-deployment

# Make multiple updates
kubectl set image deployment/webapp-deployment webapp=nginx:1.21.2
kubectl set resources deployment/webapp-deployment -c webapp --limits=cpu=500m,memory=256Mi

# Resume rollout
kubectl rollout resume deployment/webapp-deployment
Canary Deployments
bash
# Create canary deployment with subset of traffic
cat <<EOF | kubectl apply -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp-canary
spec:
  replicas: 1  # Smaller number for canary
  selector:
    matchLabels:
      app: webapp
      track: canary
  template:
    metadata:
      labels:
        app: webapp
        track: canary
    spec:
      containers:
      - name: webapp
        image: nginx:1.21.2  # New version
        ports:
        - containerPort: 80
EOF
Best Practices
Use RollingUpdate strategy for production workloads

Set appropriate resource limits in pod templates

Implement health checks (liveness and readiness probes)

Maintain revision history for rollback capabilities

Use meaningful labels for better management

Monitor rollout progress during updates

Test rollback procedures regularly

Troubleshooting Common Issues
Deployment Stuck
bash
# Describe deployment to see events
kubectl describe deployment <deployment-name>

# Check replica sets
kubectl get replicasets

# Check pod issues
kubectl get pods
kubectl describe pod <pod-name>
Image Pull Errors
bash
# Check image name and accessibility
kubectl describe pod <pod-name> | grep -i image
Resource Quota Issues
bash
# Check resource quotas
kubectl describe resourcequota
