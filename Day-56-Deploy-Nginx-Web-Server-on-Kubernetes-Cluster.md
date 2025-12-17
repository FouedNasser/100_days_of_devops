# Day 56: Deploy Nginx Web Server on Kubernetes Cluster

## Challenge Description
The Nautilus team needs a scalable and highly available static website deployment on their Kubernetes cluster. The requirements are:
1.  **Deployment**: Create a Deployment named `nginx-deployment` using the `nginx:latest` image with 3 replicas. The container should be named `nginx-container`.
2.  **Service**: Create a `NodePort` Service named `nginx-service` that exposes the application on port `30011`.

## Key Concepts
- **Kubernetes Deployment**: A resource object that provides declarative updates for Pods and ReplicaSets, allowing for scaling and management of stateless applications.
- **Kubernetes Service**: An abstraction which defines a logical set of Pods and a policy by which to access them.
- **NodePort**: A type of Service that exposes the Service on each Node's IP at a static port (the NodePort).

## Solution

1.  **Create the Manifest File**
    We created a YAML file named `nginx-deployment-service.yaml` containing both the Deployment and Service definitions:

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx-deployment
    spec:
      replicas: 3
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx-container
            image: nginx:latest
            ports:
            - containerPort: 80
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx-service
    spec:
      type: NodePort
      selector:
        app: nginx
      ports:
      - port: 80
        targetPort: 80
        nodePort: 30011
    ```

2.  **Apply the Configuration**
    We applied the manifest to the Kubernetes cluster:
    ```bash
    kubectl apply -f nginx-deployment-service.yaml
    ```

3.  **Verify Resources**
    We checked the status of our deployment and service:
    ```bash
    kubectl get deployments
    kubectl get services
    ```
    This confirmed that `nginx-deployment` was up with 3 available replicas and `nginx-service` was listening on port 30011.

## Verification
We verified the deployment was accessible by using `curl` or a web browser to access the Node IP on the specified port:
```bash
curl http://<Node-IP>:30011
```
The standard "Welcome to nginx!" HTML page confirmed the service is running correctly.
