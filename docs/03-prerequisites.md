# Prerequisites & Environment Setup

Before running, building, or deploying this Next.js DevOps project, your environment must be properly configured. This section covers all software, tools, and configurations required on your Ubuntu VM and how they interact in the DevOps workflow.

## 1. Ubuntu VM (Base Environment)

*   **Requirement**: Ubuntu 22.04 LTS (or similar Linux VM)
*   **Reason**: Provides a stable environment to run:
    *   Node.js for Next.js application
    *   Docker for containerization
    *   Minikube for local Kubernetes cluster
    *   kubectl to manage deployments
*   **Recommended**: Allocate at least 2 CPUs, 4GB RAM for smooth Minikube and Docker operation.

## 2. Node.js and npm

*   **Purpose**: Run Next.js application locally and build production code
*   **Installation**:

    ```bash
    # Update package index
    sudo apt update

    # Install Node.js (LTS version)
    sudo apt install -y nodejs npm

    # Verify installation
    node -v
    npm -v
    ```

*   **Why**: Node.js executes the JavaScript server-side code, while npm handles project dependencies.
*   **Usage in this project**:
    *   `npm install` → installs Next.js dependencies
    *   `npm run dev` → starts local development server
    *   `npm run build` → prepares production build

## 3. Docker

*   **Purpose**: Containerize Next.js application for consistent environments and deployment
*   **Installation**:

    ```bash
    # Update package index
    sudo apt update

    # Install Docker
    sudo apt install -y docker.io

    # Start Docker service
    sudo systemctl start docker
    sudo systemctl enable docker

    # Verify
    docker --version
    docker-compose --version
    ```

*   **Why**:
    *   Ensures application runs identically across development, CI/CD, and production
    *   Required for building images and running containers locally or pushing to GHCR
*   **Usage in this project**:
    *   `docker build` → build optimized multi-stage Next.js image
    *   `docker run` → test container locally before deployment

## 4. Git & GitHub

*   **Purpose**: Version control and CI/CD integration
*   **Installation**:

    ```bash
    sudo apt install -y git

    # Verify
    git --version
    ```

*   **GitHub Account Requirements**:
    *   Repository for source code
    *   GitHub Container Registry (GHCR) for Docker images
    *   GitHub Actions enabled for automated workflow
*   **Usage in this project**:
    *   Push code → triggers CI/CD workflow
    *   Version control for Docker images using commit SHA

## 5. Kubernetes CLI (kubectl)

*   **Purpose**: Manage deployments, services, and pods in Minikube cluster
*   **Installation**:

    ```bash
    sudo apt update
    sudo apt install -y kubectl

    # Verify
    kubectl version --client
    ```

*   **Usage in this project**:
    *   Apply deployment/service manifests (`kubectl apply -f k8s/`)
    *   Verify pods and services (`kubectl get pods`, `kubectl get svc`)
    *   Port-forwarding or NodePort access for local testing

## 6. Minikube

*   **Purpose**: Local Kubernetes cluster for testing deployment
*   **Installation**:

    ```bash
    # Download latest Minikube
    curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64

    # Install
    sudo install minikube-linux-amd64 /usr/local/bin/minikube

    # Verify
    minikube version
    ```

*   **Start Minikube**:

    ```bash
    minikube start --driver=virtualbox
    ```

*   **Why**: Provides a local Kubernetes environment to test real-world deployment scenarios
*   **Usage in this project**:
    *   Deploy containerized Next.js application
    *   Expose service via NodePort or port-forward
*   **Tip**: Ensure VM has sufficient CPU & memory. If using VirtualBox, set networking to Bridged Adapter to allow host access from Windows machine.

## 7. Optional / Additional Tools

*   **Browser (Chrome / Firefox)** → Access application from host machine
*   **curl / wget** → Test endpoints
*   **Port Forwarding / SSH config** → Access VM services externally if needed

## Summary Table of Prerequisites

| Tool / Requirement | Purpose                     | Verification                 |
| :----------------- | :-------------------------- | :--------------------------- |
| Ubuntu VM          | Base environment            | `lsb_release -a`             |
| Node.js & npm      | Run Next.js                 | `node -v`, `npm -v`          |
| Docker             | Containerize app            | `docker --version`           |
| Git & GitHub       | Version control & CI/CD     | `git --version`              |
| kubectl            | Manage Kubernetes           | `kubectl version --client`   |
| Minikube           | Local Kubernetes cluster    | `minikube version`           |
| Browser / curl     | Access & test app           | `curl http://localhost:3000` |

After this setup, your environment is fully ready for:

*   Running Next.js locally
*   Building Docker images
*   Configuring GitHub Actions workflow
*   Deploying to Minikube

