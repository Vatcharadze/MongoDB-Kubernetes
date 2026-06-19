# MongoDB + Mongo Express on Kubernetes

A simple Kubernetes setup that deploys MongoDB along with Mongo Express (a web-based MongoDB admin UI). This project demonstrates core Kubernetes concepts: Deployments, Services, Secrets, and ConfigMaps.

## Architecture

- **MongoDB** runs as a single-replica Deployment, exposed internally via a ClusterIP Service (`mongodb-service`) on port `27017`.
- **Mongo Express** connects to MongoDB and is exposed externally via a `LoadBalancer` Service on port `8081` (NodePort `30000`).
- Credentials are stored in a **Secret** (`mongodb-secret`) and the MongoDB connection address is stored in a **ConfigMap** (`mongodb-configmap`).

## Files

| File | Purpose |
|---|---|
| `mongodb-secret.yaml` | Stores base64-encoded MongoDB root username/password |
| `mongodb-deployment.yaml` | MongoDB Deployment + internal Service |
| `mongoexpress-configmap.yaml` | Stores the MongoDB service URL used by Mongo Express |
| `mongoexpress-deployment.yaml` | Mongo Express Deployment + external (LoadBalancer) Service |

## Prerequisites

- A running Kubernetes cluster (e.g. Minikube, Kind, or a cloud cluster)
- `kubectl` configured to talk to your cluster

## Setup

### 1. Set your credentials

Edit `mongodb-secret.yaml` and replace the `data` values with your own base64-encoded username/password:

```bash
echo -n "yourusername" | base64
echo -n "yourpassword" | base64
```

### 2. Apply the manifests

Order matters — the Secret and ConfigMap must exist before the Deployments that reference them:

```bash
kubectl apply -f mongodb-secret.yaml
kubectl apply -f mongodb-deployment.yaml
kubectl apply -f mongoexpress-configmap.yaml
kubectl apply -f mongoexpress-deployment.yaml
```

### 3. Verify everything is running

```bash
kubectl get pods
kubectl get svc
```

## Accessing Mongo Express

If you're using **Minikube**:

```bash
minikube service mongo-express-service
```

Otherwise, access it via your cluster's external IP (or `localhost:30000` if using NodePort with Docker Desktop/Kind).


