# Kubernetes Deployment & Application Access

This step demonstrates how to deploy the Dockerized Next.js application to a local Kubernetes cluster (Minikube) and all possible ways to access it, from inside the VM or from your Windows host machine.

## 1. Kubernetes Manifests

All Kubernetes manifests are located in the `k8s/` folder.

### Deployment (`k8s/deployment.yaml`)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nextjs-deployment
  labels:
    app: nextjs-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nextjs-app
  template:
    metadata:
      labels:
        app: nextjs-app
    spec:
      containers:
      - name: nextjs-container
        image: ghcr.io/<username>/<repo-name>:latest
        ports:
        - containerPort: 3000
        livenessProbe:
          httpGet:
            path: /
            port: 3000
          initialDelaySeconds: 5
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /
            port: 3000
          initialDelaySeconds: 5
          periodSeconds: 10
```

*   **Replicas**: 2 pods for high availability
*   **Health checks**: Liveness and readiness probes ensure Kubernetes restarts unhealthy pods

### Service (`k8s/service.yaml`)

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nextjs-service
  labels:
    app: nextjs-app
spec:
  type: NodePort
  selector:
    app: nextjs-app
  ports:
    - port: 80
      targetPort: 3000
      nodePort: 32000
```

*   **NodePort**: Exposes service on host-accessible port (32000)
*   **Selector**: Matches pods created by deployment
*   **TargetPort**: Maps service port 80 to container port 3000
*   Using NodePort is simple for local Minikube testing without an Ingress controller.

## 2. Deploying to Minikube

Apply Deployment and Service:

```bash
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
```

**Verify pods are running**:

```bash
kubectl get pods
```

**Verify service**:

```bash
kubectl get svc
```

You should see `nextjs-service` with NodePort assigned (32000)

## 3. Accessing the Application

Here are all possible ways to access the app running in Minikube:

### Option 1: Using NodePort (Host Machine Browser)

NodePort exposes the service on the Minikube node IP:

```bash
minikube ip
```

Example output: `192.168.99.100`

Access in browser (from Ubuntu VM or Windows host if network allows):

`http://<minikube-ip>:32000`

*   **Explanation**:
    *   NodePort forwards requests to the target port (3000) of your pod
    *   Works across VM and host if networking allows

### Option 2: Using Port-Forwarding

Port-forward the service to a local port:

```bash
kubectl port-forward svc/nextjs-service 8080:80 --address 0.0.0.0
```

Access in browser (Windows host or Ubuntu VM):

`http://<VM-ip>:8080`

*   **Explanation**:
    *   Maps service port 80 to host port 8080
    *   `--address 0.0.0.0` allows access from any machine on the network
*   **Verification**:

    ```bash
kubectl get svc
kubectl get pods
curl http://localhost:8080
    ```

*   **Tip**: Use this when NodePort access doesn’t work due to network restrictions.

### Option 3: Using Minikube Service Command

Minikube provides a shortcut to open service in browser:

```bash
minikube service nextjs-service
```

*   Automatically opens the default browser with the service URL
*   **Explanation**: Minikube sets up a temporary tunnel to the NodePort

### Option 4: Using Ingress (Optional / Advanced)

If you set up an Ingress controller:

```yaml
# k8s/ingress.yaml (example)
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nextjs-ingress
spec:
  rules:
  - host: nextjs.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nextjs-service
            port:
              number: 80
```

Add entry to `/etc/hosts` on your host machine:

`<minikube-ip> nextjs.local`

Access via:

`http://nextjs.local`

This mimics real-world production deployments. Optional for local testing.

## 4. Summary Table of Access Methods

| Method            | Command                                                               | Access URL                          | Notes                                       |
| :---------------- | :-------------------------------------------------------------------- | :---------------------------------- | :------------------------------------------ |
| **NodePort**      | `kubectl apply -f k8s/service.yaml`                                   | `http://<minikube-ip>:32000`        | Simple, works across VM and host            |
| **Port-Forward**  | `kubectl port-forward svc/nextjs-service 8080:80 --address 0.0.0.0` | `http://<VM-ip>:8080`               | Flexible, network-independent               |
| **Minikube Service** | `minikube service nextjs-service`                                     | Opens browser automatically         | Easiest for local development               |
| **Ingress**       | Configure `ingress.yaml` + `/etc/hosts`                               | `http://nextjs.local`               | Production-like setup, optional             |

## Outcome of this Step

*   Application is deployed on Minikube with replicas and health checks
*   Exposed using NodePort or port-forwarding for testing
*   Accessible from Ubuntu VM and Windows host via multiple methods
*   Fully mirrors real-world Kubernetes deployment workflow

