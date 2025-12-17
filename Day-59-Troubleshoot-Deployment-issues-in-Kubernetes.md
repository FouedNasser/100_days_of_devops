# Day 59: Troubleshoot Deployment issues in Kubernetes

## Challenge Description
The Nautilus DevOps team's `redis-deployment` on the Kubernetes cluster is currently down due to errors introduced during recent changes. We need to identify and fix the issues preventing the pods from running.

## Key Concepts
-   **Kubernetes Deployments:** Managing and scaling sets of identical Pods.
-   **Image Pull Errors:** Occur when Kubernetes cannot pull the specified container image from a registry, often due to incorrect image names or tags.
-   **ConfigMap Errors:** Problems arising from incorrect ConfigMap names or missing ConfigMaps, leading to volume mount failures or application configuration issues.
-   **`kubectl describe pod`:** A crucial command for inspecting events and status of a Pod, which often reveals the root cause of failures.
-   **`kubectl logs`:** Used to view the output of a container, which can provide application-level error messages.

## Solution

Upon investigating the `redis-deployment` and the associated pod events, we identified two main issues:

1.  **Incorrect Image Name:**
    -   **Problem:** The deployment specified the Redis image as `redis:alpin`.
    -   **Diagnosis:** The pod events showed `ImagePullBackOff` or `ErrImagePull`, indicating that Kubernetes could not find an image with that exact tag. The correct tag for the Alpine-based Redis image is `alpine`.
    -   **Fix:** We edited the `redis-deployment` to change the image name from `redis:alpin` to `redis:alpine`.
        ```bash
        kubectl edit deployment redis-deployment
        # Change image: redis:alpin to image: redis:alpine
        ```

2.  **Incorrect ConfigMap Name:**
    -   **Problem:** The deployment was attempting to mount a ConfigMap named `redis-conig` as a volume.
    -   **Diagnosis:** Pod events indicated that the specified ConfigMap (`redis-conig`) was not found, leading to the pod failing to start or having issues mounting its configuration volume. Listing the available ConfigMaps revealed that the correct name was `redis-config`.
    -   **Fix:** We edited the `redis-deployment` to correct the ConfigMap name in the volume definition from `redis-conig` to `redis-config`.
        ```bash
        kubectl edit deployment redis-deployment
        # Change name: redis-conig to name: redis-config in the volumes section
        ```

After applying these fixes to the `redis-deployment` using `kubectl edit deployment redis-deployment`, Kubernetes automatically recreated the pods with the corrected configuration.

## Verification
We verified the fix by checking the status of the `redis-deployment` and its pods:
```bash
kubectl get deployment redis-deployment
kubectl get pods -l app=redis
```
The pods transitioned to a `Running` state, confirming that the deployment issues were resolved. We also checked the logs of the running Redis pod to ensure the application started without errors.
