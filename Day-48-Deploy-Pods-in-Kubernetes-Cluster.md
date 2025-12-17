# Day 48: Deploy Pods in Kubernetes Cluster

## Challenge Description
The Nautilus DevOps team is diving into Kubernetes for application management. One team member has a task to create a pod according to the details below:

*   Create a pod named `pod-nginx` using the `nginx` image with the `latest` tag. Ensure to specify the tag as `nginx:latest`.
*   Set the app label to `nginx_app`, and name the container as `nginx-container`.

Note: The `kubectl` utility on `jump_host` is configured to operate with the Kubernetes cluster.

## Key Concepts
*   **Kubernetes Pod:** The smallest deployable unit in Kubernetes, representing a running process.
*   **`kubectl`:** The command-line tool for interacting with the Kubernetes API.
*   **Pod Manifest:** A YAML file describing the desired state of a Pod, including its metadata (name, labels) and spec (containers, images).
*   **Labels:** Key-value pairs used to organize and select subsets of objects.

## Solution

1.  **Login to Jump Host:**
    We accessed the jump host where `kubectl` is configured.
    ```bash
    ssh thor@jump_host
    ```

2.  **Create Pod Manifest:**
    We created a file named `nginx-pod.yaml` with the specific requirements:
    *   Pod Name: `pod-nginx`
    *   Image: `nginx:latest`
    *   Container Name: `nginx-container`
    *   Label: `app=nginx_app`

    ```bash
    vi nginx-pod.yaml
    ```

    Content of `nginx-pod.yaml`:
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: pod-nginx
      labels:
        app: nginx_app
    spec:
      containers:
      - name: nginx-container
        image: nginx:latest
        ports:
        - containerPort: 80
    ```

3.  **Apply the Manifest:**
    We used `kubectl apply` to create the pod from the YAML file.
    ```bash
    kubectl apply -f nginx-pod.yaml
    ```

## Verification
To verify the deployment, we checked the status of the pod and its details.

1.  **Check Pod Status:**
    ```bash
    kubectl get pods
    ```
    Expected output should show `pod-nginx` in `Running` state.

2.  **Verify Configuration:**
    We described the pod to ensure all settings were applied correctly (correct container name and label).
    ```bash
    kubectl describe pod pod-nginx
    ```
