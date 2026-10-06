# K8s Demo

Simple ASP.NET Core project created to demonstrate Docker, CI/CD and Kubernetes basics.

The project contains a small REST API connected to Microsoft SQL Server.

## Technologies

- ASP.NET Core
- Entity Framework Core
- Microsoft SQL Server
- Docker
- Docker Compose
- Kubernetes
- kind
- GitHub Actions
- GitHub Container Registry

## Architecture

```text
Client
  |
Kubernetes Service
  |
ASP.NET Core API
  |
SQL Server
  |
Persistent Volume
```

The API is deployed using Kubernetes Deployment with multiple replicas.
SQL Server is deployed as a separate Kubernetes.

## Kubernetes resources

The `k8s` directory contains:

```text
k8s/
├── api-deployment.yaml
├── api-service.yaml
├── mssql-deployment.yaml
├── mssql-service.yaml
└── mssql-pvc.yaml
```

The project uses:

- Deployment for API and SQL Server
- Service for internal communication
- PersistentVolumeClaim for SQL Server data
- Kubernetes Secret for database credentials
- readiness probe
- liveness probe

## Docker

The application can be started locally using Docker Compose:

```bash
docker compose up --build
```

The API is available at:

```text
http://localhost:8080
```

Health endpoint:

```text
http://localhost:8080/health
```

## Kubernetes

A local Kubernetes cluster can be created using kind:

```bash
kind create cluster --name taskapi
```

Apply Kubernetes resources:

```bash
kubectl apply -f k8s/
```

Check running pods:

```bash
kubectl get pods
```

Check services:

```bash
kubectl get services
```

Check deployments:

```bash
kubectl get deployments
```

## Accessing the API

The API Service uses ClusterIP.

For local testing:

```bash
kubectl port-forward service/taskapi 8080:8080
```

The API can then be accessed at:

```text
http://localhost:8080
```

## Container Registry

Docker images are published to GitHub Container Registry.

Example:

```text
ghcr.io/kajetanbilinski/k8s-demo:sha-<commit-sha>
```

Each image can be associated with a specific Git commit.

## CI

GitHub Actions runs automatically for pull requests and changes to the main branch.

The CI pipeline performs:

```text
Restore
Build
Test
Docker build
Kubernetes smoke test
```

The Kubernetes smoke test creates a temporary kind cluster on the GitHub Actions runner.

The workflow then:

```text
Builds the Docker image
Creates a Kubernetes cluster
Loads the image into kind
Deploys SQL Server
Deploys the API
Waits for successful rollout
Checks the /health endpoint
```

The temporary cluster is removed after the workflow finishes.

## Image publishing

After changes are merged into the main branch, GitHub Actions builds and publishes a Docker image to GHCR.
Images are tagged using the Git commit SHA.

Example:

```text
sha-a1b2c3d4...
```

This allows a specific application version to be deployed and identified.

## Rolling updates

Kubernetes Deployment is used to perform rolling updates.
Deployment status can be checked using:

```bash
kubectl rollout status deployment/taskapi
```

Deployment history:

```bash
kubectl rollout history deployment/taskapi
```

Rollback:

```bash
kubectl rollout undo deployment/taskapi
```
