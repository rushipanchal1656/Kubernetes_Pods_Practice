# 🐳 Kubernetes Core Features & Components Practice

> A hands-on collection of Kubernetes YAML manifests for learning core concepts and best practices.

---

## 📋 Quick Start Guide

| Component | File | Purpose |
|-----------|------|---------|
| 🚀 **Deployments** | [deployment.yml](deployment.yml) | Run & scale applications |
| 📦 **ReplicaSets** | [replica-set-prod.yml](replica-set-prod.yml) | Maintain pod replicas |
| ⚙️ **ConfigMaps** | [configmap.yml](configmap.yml) | Store configurations |
| 🔐 **Secrets** | [generic-secret.yml](generic-secret.yml) | Secure sensitive data |
| 💾 **Persistent Volumes** | [persistent-volume.yml](persistent-volume.yml) | Manage storage |
| 🏠 **Host Volumes** | [host-volume.yml](host-volume.yml) | Local node storage |
| 🌐 **NFS Volumes** | [nfs-volume.yaml](nfs-volume.yaml) | Shared network storage |
| 💓 **Health Probes** | [liveness-prob.yml](liveness-prob.yml) | Monitor pod health |

---

## 🎯 Core Components

### 🚀 **Deployments** | [deployment.yml](deployment.yml)
Manage containerized applications with:
- ✨ Self-healing (restarts failed pods)
- 🔄 Rolling updates & rollbacks
- 📊 Automatic pod replication (4 replicas)
- 🏷️ Label-based pod selection

```bash
kubectl apply -f deployment.yml
kubectl get deployments
```

---

### 📦 **ReplicaSets** | [replica-set-prod.yml](replica-set-prod.yml) & [replica-set-test.yml](replica-set-test.yml)
Ensure desired pod count with advanced selectors:
- 🛡️ Self-healing capabilities
- 🔍 Advanced label matching (`matchExpressions`)
- ⚠️ No rolling updates (use Deployments instead)
- **Best for:** Fine-grained environment control (prod/test/dev)

**Key Difference from Deployments:** Less features but more control over pod selection.

---

### 🔴 **ReplicaController** (Legacy) | [replica-controller.yml](replica-controller.yml)
⚠️ **Deprecated** - Use **Deployments** instead.

---

## ⚙️ Configuration & Secrets

### 📝 **ConfigMaps** | [configmap.yml](configmap.yml)
Store non-sensitive configurations separately from code:
- 🔖 Environment variables
- 📋 Application settings
- 🎛️ Feature flags
- **Example:** Database hostname, port number

```bash
kubectl create configmap mysqlconfig --from-literal=MYSQL_ROOT_PASSWORD=root
```

---

### 🔐 **Secrets** | [generic-secret.yml](generic-secret.yml) & [docker-registry-secret.yml](docker-registry-secret.yml)
Secure sensitive data (passwords, API keys, tokens):
- 🔒 Base64-encoded (add encryption for production)
- 📦 Max 1 MB per secret
- 🚀 Pull private Docker images
- **Types:** Opaque, docker-registry, tls, ssh

```bash
kubectl create secret generic my-secret --from-literal=password=mypassword
```

**⚠️ Security Tips:**
- Enable encryption at rest
- Use external tools (Vault, AWS Secrets Manager)
- Rotate secrets regularly

---

## 💾 Storage Management

### 📦 **Persistent Volumes (PV) & Claims (PVC)**

#### PersistentVolume | [persistent-volume.yml](persistent-volume.yml)
Cluster-wide storage resource (10Gi capacity):
- 🔄 Access Modes: `ReadWriteOnce`, `ReadWriteMany`, `ReadOnlyMany`
- 💾 Backends: hostPath, NFS, AWS EBS, GCP Disk, Azure Disk
- 🔧 Admin-created resources

#### PersistentVolumeClaim | [persistent-volume-claim.yml](persistent-volume-claim.yml)
Request storage from PV (Pod-specific):
- 🎯 Auto-binding to matching PV
- 📋 Namespace-scoped
- 🧠 Kubernetes handles provisioning

**Workflow:** PV created → PVC requests → Auto-bound → Pod mounts

```bash
kubectl apply -f persistent-volume.yml
kubectl apply -f persistent-volume-claim.yml
kubectl get pv
kubectl get pvc
```

---

### 🏠 **Host Volume** | [host-volume.yml](host-volume.yml)
Direct access to node filesystem:
- ⚡ Fast (local storage)
- 🔗 Node-tied (pod affinity)
- 📁 Auto-create directories
- **Use:** Development, logging, node-local cache
- ⚠️ **Not for production**

---

### 🌐 **NFS Volume** | [nfs-volume.yaml](nfs-volume.yaml) & [NFS.yml](NFS.yml)
Share storage across multiple nodes:
- 👥 MultiRead/Write (many pods simultaneously)
- 🏗️ Requires NFS server
- 🌍 Production-ready with HA
- **Use:** Databases, file servers, multi-node apps

```yaml
nfs:
  server: 192.168.1.100
  path: /home/ubuntu/nfs-share
```

---

## 💓 Health Checks & Monitoring

### 🩹 **Liveness Probe** | [liveness-prob.yml](liveness-prob.yml)
**Detects dead pods → Restarts them**
- 🔍 Check if app is responding
- 🚨 Automatic restart on failure
- 📊 Default: 3 failures = restart

```yaml
livenessProbe:
  httpGet:
    path: /index.html
    port: 80
  initialDelaySeconds: 5   # Wait 5s before first check
  periodSeconds: 10        # Check every 10s
  failureThreshold: 3      # Restart after 3 fails
```

**Probe Types:** httpGet, tcpSocket, exec

---

### ✅ **Readiness Probe** | [readyness-prob.yml](readyness-prob.yml)
**Detects unready pods → Removes from traffic**
- 🎯 Check if app is ready to serve
- 🚦 Removes from load balancer
- 📊 Default: 3 failures = remove from service

```yaml
readinessProbe:
  httpGet:
    path: /login.html
    port: 80
```

| Aspect | Liveness | Readiness |
|--------|----------|-----------|
| **Action** | Restart pod | Remove from LB |
| **Purpose** | Detect deadlock | Wait for startup |
| **Impact** | Pod lifecycle | Traffic routing |

---

## 🚀 Quick Commands

```bash
# Deploy
kubectl apply -f deployment.yml          # Single file
kubectl apply -f .                       # All files

# Monitor
kubectl get pods
kubectl get deployments
kubectl describe pod <pod-name>

# Debug
kubectl logs <pod-name>
kubectl logs <pod-name> -f               # Follow
kubectl exec -it <pod-name> -- /bin/bash

# Cleanup
kubectl delete -f deployment.yml
kubectl delete pods --all
```

---

## ✅ Learning Outcomes

- ✨ Deploy & scale applications with Deployments
- 🔄 Manage replicas with ReplicaSets
- ⚙️ Configure apps with ConfigMaps
- 🔐 Secure data with Secrets
- 💾 Manage persistent data with PV/PVC
- 📁 Use various storage backends (host, NFS)
- 💓 Monitor health with Liveness & Readiness Probes

---

## 📚 Resources

- 🔗 [Kubernetes Docs](https://kubernetes.io/docs/)
- 📖 [kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
- 🎓 [API Reference](https://kubernetes.io/docs/reference/kubernetes-api/)

---

**Last Updated:** February 2026 | **Type:** 📚 Educational Practice Repository