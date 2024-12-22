# Kubernetes Rolling Updates Command Reference Guide

### Project Content Table
- [Section 1: Core Project Workflow](#section-1-core-project-workflow)
- [Section 2: Advanced Operations](#section-2-advanced-operations)
- [Section 3: Production Guide](#section-3-production-guide)

> **Author**: [Md Toriqul Islam](https://linkedin.com/in/thetoriqul)  
> **Description**: Comprehensive guide for managing Kubernetes rolling updates and rollbacks  
> **Learning Focus**: Zero-downtime deployments and version management  
> **Note**: Review commands carefully before execution in production environments

## Section 1: Core Project Workflow

### Step 1: Initialize Deployment
```bash
# Deploy NGINX version 1.21.3
kubectl apply -f deployment.yaml

# Verification command
kubectl get deployment nginx-deployment
kubectl get pods -l app=nginx

# Expected output:
# NAME               READY   UP-TO-DATE   AVAILABLE   AGE
# nginx-deployment   3/3     3            3           1m
```

### Step 2: Verify Initial State
```bash
# Check deployment details
kubectl describe deployment nginx-deployment

# Verify NGINX version
kubectl get pods -o wide

# Verification command
kubectl describe pod nginx-deployment-<pod-id> | grep Image:
# Should show nginx:1.21.3
```

### Step 3: Perform Rolling Update
```bash
# Update to latest NGINX version
kubectl set image deployment/nginx-deployment nginx=nginx:latest

# Monitor update progress
kubectl rollout status deployment/nginx-deployment

# Verification command
kubectl get pods -w
# Should show pods being updated one by one
```

### Step 4: Verify Update Success
```bash
# Check new version
kubectl describe deployment nginx-deployment | grep Image:

# View rollout history
kubectl rollout history deployment/nginx-deployment

# Verification command
kubectl get pods -o wide
# Should show all pods running latest version
```

### Final Step: Rollback (if needed)
```bash
# Rollback to previous version
kubectl rollout undo deployment nginx-deployment

# Verification command
kubectl rollout status deployment/nginx-deployment
```

## Section 2: Advanced Operations

### Advanced Deployment Management
```bash
# Pause rolling update
kubectl rollout pause deployment/nginx-deployment

# Resume update
kubectl rollout resume deployment/nginx-deployment

# View detailed rollout history
kubectl rollout history deployment/nginx-deployment --revision=2
```

### Advanced Monitoring
```bash
# Watch deployment progress
kubectl get deployments -w

# View pod logs
kubectl logs -l app=nginx

# Check events
kubectl get events --sort-by=.metadata.creationTimestamp
```

## Section 3: Production Guide

### Production Setup
```bash
# Set resource limits
kubectl set resources deployment nginx-deployment \
  --limits=cpu=200m,memory=512Mi \
  --requests=cpu=100m,memory=256Mi

# Configure update strategy
kubectl patch deployment nginx-deployment -p \
  '{"spec":{"strategy":{"rollingUpdate":{"maxSurge":1,"maxUnavailable":0}}}}'
```

### Monitoring
```bash
# Check deployment health
kubectl describe deployment nginx-deployment

# Monitor pod metrics
kubectl top pods -l app=nginx

# View system events
kubectl get events --field-selector type=Warning
```

### Backup Operations
```bash
# Export deployment configuration
kubectl get deployment nginx-deployment -o yaml > backup.yaml

# Save rollout history
kubectl rollout history deployment nginx-deployment > history.txt
```

## Learning Notes

1. Always verify deployment status after updates
2. Use specific version tags in production
3. Monitor resource usage during updates
4. Keep deployment backups before major changes
5. Test rollback procedures regularly

---

> 💡 **Best Practice**: Always use specific version tags instead of 'latest' in production environments

> ⚠️ **Warning**: Rolling updates may temporarily increase resource usage

> 📝 **Note**: Keep track of deployment history for quick rollbacks