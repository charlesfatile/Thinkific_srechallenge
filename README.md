
## Application Overview

This comprehensive guide provides step-by-step instructions to complete the Thinkific SRE Challenge for the DumbKV application. Follow these steps sequentially to demonstrate enterprise-grade SRE practices.

---

## Prerequisites

- GitHub account
- Access to GitHub Codespaces or local development environment
- Basic understanding of Docker, Kubernetes, and CI/CD concepts

---

## Containerizing the Application

- [**Dockerfile**](./Dockerfile) for containerizing the application
- In order to do the local testing, go through [Running container](#local-testing)

## CI/CD 
[![Docker Build & Push](https://github.com/babfat2010/Thinkific_srechallenge/actions/workflows/build.yaml/badge.svg)](https://github.com/babfat2010/Thinkific_srechallenge/actions/workflows/build.yaml)

[![Tests with Postgres](https://github.com/babfat2010/Thinkific_srechallenge/actions/workflows/tests-postgres.yml/badge.svg)](https://github.com/babfat2010/Thinkific_srechallenge/actions/workflows/tests-postgres.yml)

[![Unit Tests](https://github.com/babfat2010/Thinkific_srechallenge/actions/workflows/unit-test.yml/badge.svg)](https://github.com/babfat2010/Thinkific_srechallenge/actions/workflows/unit-test.yml)

Below are the worklows created

1- **Docker build & Bush**

- Workflow helps in building the docker file on `feature branch`
- Workflows helps in testing the dockerfile getting build successfully on every PR to `main branch`
- Whenever any PR merged to `main branch`, workflow will push the image to GHRC - [Container-Registry](https://github.com/babfat2010/Thinkific_srechallenge/pkgs/container/thinkific_srechallenge)

2- **Tests with Postgres**

- Workflow check the application unit test case w.r.t postgres as a backend
- On every PR this workflow will be triggered

3- **Unit Test**

- Workflow check the application unit test case w.r.t sqlite in-memory db as a backend
- On every PR this workflow will be triggered

## K8s deployment

The application is completely containerized can be easily deployed to the k8s cluster.
> **For more informtion go through** - [K8s-deployment-doc](./manifests/README.md)

## Service Level Objectives

SLOs are defined under - [SLOs documentation](./SLOs.md)

## Validation Checklist

Before submitting, ensure:

- [x] Dockerfile builds successfully
- [x] GitHub Actions workflows pass
- [x] All Kubernetes manifests are valid YAML
- [x] Documentation is comprehensive and clear
- [x] SLOs are realistic and measurable
- [x] Security best practices are implemented
- [x] Health checks are properly configured
- [x] Monitoring is integrated

## Testing Your Solution

### Local Testing
1. Build Docker image: `docker build -t dumbkv:latest .`
2. Run container: `docker run -p 8000:8000 dumbkv:latest`
3. Test health endpoint: `curl http://localhost:8000/health`
4. Test metrics endpoint: `curl http://localhost:8000/metrics`

### GitHub Actions Testing
1. Push changes to trigger workflows
2. Verify build workflow completes successfully
3. Verify test workflow passes for both SQLite and PostgreSQL
4. Check that Docker image is built without errors

## Troubleshooting

### Common Issues
1. **Docker build fails**: Check UV installation and Python version
2. **Tests fail**: Ensure PostgreSQL service is running for integration tests
3. **Kubernetes deployment fails**: Verify storage class and ingress controller
4. **Metrics not scraped**: Confirm ServiceMonitor and Prometheus Operator

### Debug Commands
```bash
# Docker debugging
docker logs <container-id>
docker exec -it <container-id> /bin/bash

# Kubernetes debugging
kubectl describe pod <pod-name> -n dumbkv
kubectl logs <pod-name> -n dumbkv
kubectl get events -n dumbkv
```
