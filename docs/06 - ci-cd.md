# CI/CD Setup Using GitHub Actions

The goal of this step is to automate the Docker image build and push process whenever code changes are pushed to the main branch. This ensures a consistent, repeatable, and production-ready container image is always available.

## 1. Workflow Overview

GitHub Actions Workflow is defined in: `.github/workflows/docker-build-push.yml`

### Workflow Purpose

*   Trigger automatically on push to the `main` branch
*   Build the Docker image from the repository code
*   Tag the Docker image with:
    *   `latest` → always points to most recent build
    *   Commit SHA → unique version for traceability
*   Push both tags to GitHub Container Registry (GHCR)
*   Automates CI/CD and ensures image consistency for Kubernetes deployment.

## 2. Workflow File Structure

```yaml
name: Build and Push Docker Image

on:
  push:
    branches:
      - main

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Log in to GHCR
        uses: docker/login-action@v3
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Build Docker image
        run: docker build -t $REGISTRY/${{ env.IMAGE_NAME }}:latest .

      - name: Tag image with SHA
        run: docker tag $REGISTRY/${{ env.IMAGE_NAME }}:latest $REGISTRY/${{ env.IMAGE_NAME }}:${{ github.sha }}

      - name: Push images
        run: |
          docker push $REGISTRY/${{ env.IMAGE_NAME }}:latest
          docker push $REGISTRY/${{ env.IMAGE_NAME }}:${{ github.sha }}
```

## 3. Explanation of Workflow Steps

### Trigger

```yaml
on:
  push:
    branches:
      - main
```

*   Triggered automatically on every push to `main` branch.
*   Ensures CI/CD happens without manual intervention.

### Environment Variables

```yaml
env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}
```

*   `REGISTRY`: GitHub Container Registry (GHCR) URL
*   `IMAGE_NAME`: Repository name, used as Docker image name
*   These variables are referenced throughout the workflow to avoid hardcoding.

### Steps

#### Checkout Repository

```yaml
- uses: actions/checkout@v4
```

*   Pulls the latest code from the repository
*   Ensures Docker has access to the latest source for building

#### Login to GHCR

```yaml
- uses: docker/login-action@v3
  with:
    registry: ${{ env.REGISTRY }}
    username: ${{ github.actor }}
    password: ${{ secrets.GITHUB_TOKEN }}
```

*   **Purpose**: Authenticate with GitHub Container Registry
*   Uses `GITHUB_TOKEN` secret provided automatically by GitHub Actions
*   **Why**: Required to push Docker images to GHCR

#### Build Docker Image

```bash
docker build -t $REGISTRY/${{ env.IMAGE_NAME }}:latest .
```

*   Builds image locally in GitHub Actions runner
*   Tagged as `latest` for easy reference

#### Tag Image with Commit SHA

```bash
docker tag $REGISTRY/${{ env.IMAGE_NAME }}:latest $REGISTRY/${{ env.IMAGE_NAME }}:${{ github.sha }}
```

*   Adds unique versioning using commit SHA
*   Helps trace which image corresponds to which commit

#### Push Images to GHCR

```bash
docker push $REGISTRY/${{ env.IMAGE_NAME }}:latest
docker push $REGISTRY/${{ env.IMAGE_NAME }}:${{ github.sha }}
```

*   Pushes both `latest` and commit-specific tags to GHCR
*   Ensures both continuous updates and reproducibility of older versions

## 4. Key Points & Best Practices

*   **Automatic CI/CD**: Developers don’t need to manually build or push Docker images
*   **Traceable versions**: Using commit SHA helps in debugging and rollbacks
*   **Reusable variables**: Environment variables prevent hardcoding, making the workflow portable
*   **Security**: Using GitHub Secrets (`GITHUB_TOKEN`) to authenticate safely
*   **Future scalability**: You can extend this workflow to build Kubernetes manifests, run tests, or deploy to Minikube/EKS/GKE

## 5. How it Integrates with Docker and Kubernetes

*   Docker image built here is identical to the locally tested image
*   Kubernetes deployment uses the image from GHCR:

    ```yaml
    image: ghcr.io/<username>/<repo-name>:latest
    ```

*   Every push ensures the latest image is ready for deployment, maintaining a true DevOps pipeline.

## Outcome of this Step

*   Docker image automatically built on each push
*   Pushed to GitHub Container Registry with `latest` and SHA tags
*   Ready for deployment to Minikube Kubernetes cluster

