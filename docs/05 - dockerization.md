# Dockerization

Dockerization ensures the Next.js application runs consistently across any environment, whether it’s local development, CI/CD pipelines, or Kubernetes clusters. In this step, we’ll go through the Dockerfile, best practices, and running the Docker image locally.

## 1. Dockerfile Overview

The project contains a multi-stage Dockerfile for optimized production builds.

### Dockerfile Structure

```dockerfile
# Stage 1: Build Stage
FROM node:20-alpine AS builder

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .
RUN npm run build

# Stage 2: Production Stage
FROM node:20-alpine

WORKDIR /app

COPY --from=builder /app/package*.json ./
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/.next ./.next
COPY --from=builder /app/public ./public

EXPOSE 3000

CMD ["npm", "run", "start"]
```

## 2. Explanation of Each Stage

### Stage 1: Build Stage

```dockerfile
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build
```

*   **Base image**: `node:20-alpine`
    *   Lightweight and optimized for production
    *   Reduces final image size
*   **`WORKDIR /app`**: Sets the working directory inside the container
*   **`COPY package*.json ./` & `RUN npm install`**:
    *   Installs dependencies separately to leverage Docker cache
    *   Speeds up future builds if dependencies don’t change
*   **`COPY . .` & `RUN npm run build`**:
    *   Copies application code
    *   Builds production-ready `.next` folder

### Stage 2: Production Stage

```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY --from=builder /app/package*.json ./
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/.next ./.next
COPY --from=builder /app/public ./public
EXPOSE 3000
CMD ["npm", "run", "start"]
```

*   **Copy artifacts from builder stage**:
    *   Only the production build and dependencies are copied
    *   Reduces final image size and security risks
*   **`EXPOSE 3000`**: Opens container port 3000
*   **`CMD ["npm", "run", "start"]`**:
    *   Runs the application in production mode
*   **Why multi-stage build**: Separates build dependencies from runtime environment → smaller, secure, production-ready image.

## 3. Build Docker Image Locally

From the project root:

```bash
docker build -t nextjs-app:latest .
```

*   `-t nextjs-app:latest` → tags the image for easy reference
*   Docker will execute both stages, producing a minimal production image

**Verify the image exists**:

```bash
docker images
```

You should see `nextjs-app` with the `latest` tag.

## 4. Run Docker Container Locally

```bash
docker run -p 3000:3000 nextjs-app:latest
```

*   `-p 3000:3000` → maps host port 3000 → container port 3000
*   **Access the app**: Open browser inside Ubuntu VM: `http://localhost:3000`
*   **Outcome**: Application runs exactly as in development but in a containerized environment

## 5. Test the Production Build

*   Confirm `.next` folder is being used
*   Ensure app behaves correctly
*   **Check logs**:

    ```bash
docker logs <container-id>
    ```

*   **Optional**: remove container after testing:

    ```bash
docker rm -f <container-id>
    ```

## 6. Best Practices Implemented

*   **Multi-stage builds** → smaller and secure images
*   **Alpine base image** → lightweight
*   **Leverage Docker cache** → efficient builds
*   **Copy only necessary files** → production-ready image
*   **Expose minimal ports** → security best practice

## Outcome of this Step

*   The Docker image is built and tested locally.
*   Ready for integration into the CI/CD pipeline and deployment to Kubernetes.

