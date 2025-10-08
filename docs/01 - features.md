# Key Features Implemented

This project incorporates several key features and best practices across its development and deployment lifecycle, ensuring efficiency, reliability, and maintainability.

## Docker Optimization

*   **Multi-stage builds**: Utilizes multi-stage Docker builds to separate build-time dependencies from runtime dependencies. This results in significantly smaller, more secure, and production-ready Docker images.
*   **Alpine base image**: Employs the `node:20-alpine` base image, known for its minimal footprint, further reducing image size and attack surface.
*   **Optimized dependency handling**: Installs `package.json` dependencies in a separate layer to leverage Docker's build cache, speeding up subsequent builds when dependencies haven't changed.
*   **Minimal file copying**: Only necessary production files (e.g., `.next` build output, `public` assets) are copied into the final image, avoiding inclusion of development-specific files.
*   **Exposed minimal ports**: Only the essential application port (3000) is exposed, adhering to security best practices by limiting network access.

## CI/CD Implementation

*   **Automated GitHub Actions workflow**: A `.github/workflows/docker-build-push.yml` workflow automates the entire CI/CD process.
*   **Triggered on push to main**: The workflow automatically triggers on every push to the `main` branch, ensuring continuous integration and deployment.
*   **GitHub Container Registry (GHCR) integration**: Automatically builds and pushes Docker images to GHCR.
*   **Dual tagging strategy**: Images are tagged with both `latest` (for continuous updates) and the commit SHA (for unique versioning and traceability), facilitating debugging and rollbacks.

## Kubernetes Configuration Quality

*   **Deployment with replicas**: The Kubernetes Deployment (`k8s/deployment.yaml`) is configured with 2 replicas, ensuring high availability and fault tolerance for the application.
*   **Liveness and readiness probes**: Implements both liveness and readiness probes to monitor the health of application pods. Liveness probes ensure unhealthy pods are restarted, while readiness probes ensure traffic is only sent to pods that are ready to serve requests.
*   **NodePort service for host access**: A Kubernetes Service (`k8s/service.yaml`) of type `NodePort` is used to expose the application, making it easily accessible from the host machine (e.g., via `http://<minikube-ip>:32000`).
*   **Optional Ingress for production-like access**: The project includes an optional `ingress.yaml` example, demonstrating how to configure an Ingress controller for more advanced, production-like access patterns (e.g., custom domains, SSL termination).

## Local Development Workflow

*   **Flexible execution environments**: The Next.js application can be run and tested in multiple environments:
    *   Locally on the Ubuntu VM (using `npm run dev` or `npm run start`).
    *   Inside a Docker container (using `docker run`).
    *   Deployed to a local Kubernetes cluster (Minikube).
*   **Multiple access methods**: Provides various ways to access the deployed application, catering to different testing and debugging needs:
    *   **NodePort**: Direct access via `http://<minikube-ip>:32000`.
    *   **Port-Forwarding**: Flexible access by forwarding a service port to a local port (e.g., `http://<VM-ip>:8080`).
    *   **Minikube Service Command**: A convenient `minikube service` command to automatically open the application in a browser.
    *   **Optional Ingress**: For advanced users, access via a custom domain (e.g., `http://nextjs.local`) after configuring Ingress and `/etc/hosts`.

