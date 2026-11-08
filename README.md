# Kubernetes React: Multi-Service App on Kubernetes

A multi-service application (React client, Node.js API, background worker, PostgreSQL and Redis) containerized with Docker and deployed to Kubernetes using declarative manifests. Each component runs as its own Deployment, and internal traffic is routed through ClusterIP Services.

## Architecture

| Component | Kubernetes objects | Purpose |
|---|---|---|
| `client` | Deployment + ClusterIP Service | React front end |
| `server` | Deployment + ClusterIP Service | Node.js API; talks to PostgreSQL and Redis |
| `worker` | Deployment | Background process connected to Redis (no Service needed, nothing calls it directly) |
| `postgres` | Deployment + ClusterIP Service + PersistentVolumeClaim | Relational database with persistent storage |
| `redis` | Deployment + ClusterIP Service | In-memory data store |

Each service has its own Dockerfile (`client`, `server`, `worker`), and every Pod is reachable inside the cluster by its Service name.

## Kubernetes manifests

```
k8s/
├── client-deployment.yaml
├── client-cluster-ip-service.yaml
├── server-deployment.yaml
├── server-cluster-ip-service.yaml
├── worker-deployment.yaml
├── postgres-deployment.yaml
├── postgres-cluster-ip-service.yaml
├── database-pvc.yaml
├── redis-deployment.yaml
└── redis-cluster-ip-service.yaml
```

Key concepts used:
- **Deployments** to run and self-heal each component
- **ClusterIP Services** for stable internal networking and service discovery between Pods
- **PersistentVolumeClaim** so PostgreSQL data survives Pod restarts

## Run it

Requirements: Docker, `kubectl`, and a Kubernetes cluster (for example Minikube or Docker Desktop).

```bash
# Apply all manifests
kubectl apply -f k8s/

# Check the result
kubectl get pods
kubectl get services
kubectl get pvc
```

Remove everything:

```bash
kubectl delete -f k8s/
```

## Roadmap

- [ ] Add an Ingress to expose the client and API through a single entry point
- [ ] Move database credentials into a Kubernetes Secret
- [ ] Add a CI/CD pipeline to build images and deploy automatically

## Tech stack

Docker, Kubernetes, React, Node.js, PostgreSQL, Redis
