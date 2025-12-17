# Day 65: Deploy Redis Deployment on Kubernetes

## Challenge Description
The Nautilus application development team observed some performance issues with one of the application that is deployed in Kubernetes cluster. They decided to use Redis as an in-memory caching utility. The task is to deploy Redis on the Kubernetes cluster with specific configurations.

**Requirements:**
1.  **ConfigMap:** Create `my-redis-config` with `maxmemory 2mb` in `redis-config`.
2.  **Deployment:** Create `redis-deployment` using `redis:alpine` image.
    *   Container name: `redis-container`.
    *   Replicas: 1.
    *   Requests: 1 CPU.
    *   Port: 6379.
3.  **Volumes:**
    *   Empty directory volume `data` mounted at `/redis-master-data`.
    *   ConfigMap volume `redis-config` mounted at `/redis-master`.

## Key Concepts
-   **ConfigMaps:** Decoupling configuration artifacts from image content.
-   **Deployments:** Managing stateless applications in Kubernetes.
-   **Volumes:** Managing storage and configuration injection (EmptyDir, ConfigMap).
-   **Resource Requests:** Specifying the minimum amount of CPU/Memory a container needs.

## Solution

### 1. Create the ConfigMap
We create a ConfigMap to hold the Redis configuration.

```bash
cat <<EOF > redis-config.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-redis-config
data:
  redis-config: |
    maxmemory 2mb
EOF

kubectl apply -f redis-config.yaml
```

### 2. Create the Deployment
We create the deployment manifest ensuring all constraints (volumes, resources, ports) are met.

```bash
cat <<EOF > redis-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - name: redis-container
        image: redis:alpine
        resources:
          requests:
            cpu: "1"
        ports:
        - containerPort: 6379
        volumeMounts:
        - name: data
          mountPath: /redis-master-data
        - name: redis-config
          mountPath: /redis-master
      volumes:
      - name: data
        emptyDir: {}
      - name: redis-config
        configMap:
          name: my-redis-config
EOF

kubectl apply -f redis-deployment.yaml
```

## Verification
We verify that the ConfigMap exists and the Pod is running with the correct configuration.

```bash
kubectl get configmap my-redis-config
kubectl get deployment redis-deployment
kubectl get pods
```

To verify the volume mounts and resources, we can describe the pod:
```bash
kubectl describe pod <pod-name>
```
We should see the volumes `data` and `redis-config` mounted correctly and the CPU request set to 1.
