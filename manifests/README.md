# DumbKV Kubernetes Deployment

This directory contains Kubernetes manifests to deploy the **DumbKV** application with either a **SQLite backend** (local storage) or a **Postgres backend** (database service).  
It is structured for **Kustomize overlays**, so you can easily switch between environments.

---

### Repository Structure

```bash
manifests/
├── base/ # Common manifests (deployment, service, ingress)
│ ├── deployment.yml
│ ├── service.yml
│ ├── ingress.yml
│ ├── pvc.yml
│ ├── pv.yml
│ ├── kustomization.yml
│
├── overlays/
│ ├── sqlite/ # Overlay for SQLite backend
│ │ ├── kustomization.yml
│ │ └── deployment-patch.yml
│ │
│ └── postgres/ # Overlay for Postgres backend
│ ├── kustomization.yml
│ ├── deployment-statefulset.yml
│ ├── patch-deployment-postgres.yml
│ └── secret.yml
```

- **base** → shared resources (app Deployment, Service, Ingress, PVC).  
- **sqlite overlay** → patches base to use SQLite with a `PersistentVolumeClaim`.  
- **postgres overlay** → deploys a Postgres instance, creates a Secret, and patches DumbKV Deployment to connect to it.  

---

### Deploying with SQLite

This runs DumbKV with an embedded SQLite database stored on a PVC.

```bash
kubectl apply -k overlays/sqlite  ## Considering you are in manifests directory

```

### Deploying with Postgres

This runs DumbKV with a dedicated Postgres backend inside the cluster.

> Deploy Postgres + Secret

The overlay contains:
    * Deployment for Postgres
    * A headless Service (so the hostname postgres resolves inside the cluster)
    * A Secret with username and password

```bash
kubectl apply -k overlays/postgres  ## Considering you are in manifests directory

```
### Monitoring & Debugging

1. View logs:

```bash
kubectl logs deploy/dumbkv
kubectl logs deploy/postgres
```

2. Describe resources:

```bash
kubectl describe pod <pod-name>
kubectl describe svc dumbkv
```

3. Port-forward for local debugging:

```bash
kubectl port-forward svc/dumbkv 8000:80
```


