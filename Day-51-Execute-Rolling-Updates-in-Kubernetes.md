# Day 51: Execute Rolling Updates in Kubernetes

## Challenge Description
An application currently running on the Kubernetes cluster employs the nginx web server. The Nautilus application development team has introduced some recent changes that need deployment. They've crafted an image `nginx:1.17` with the latest updates. We need to execute a rolling update for this application, integrating the `nginx:1.17` image. The deployment is named `nginx-deployment`. We must ensure all pods are operational post-update.

## Key Concepts
*   **Kubernetes Deployment:** A controller that provides declarative updates for Pods and ReplicaSets, designed for stateless applications.
*   **Rolling Update:** A deployment strategy where old versions of pods are gradually replaced with new versions without downtime. Kubernetes gracefully moves traffic from old pods to new pods.
*   **`kubectl set image`:** A `kubectl` command used to update the image of a container within a deployment. This command triggers a rolling update.
*   **Container Name:** When a deployment has multiple containers or when the container name is explicitly defined, it's crucial to specify the correct container name along with the new image.

## Solution

1.  **SSH into the `jump_host`:**
    We accessed the jump host where the `kubectl` utility is configured to operate with the Kubernetes cluster.
    ```bash
    ssh thor@jump_host
    ```

2.  **Execute the Rolling Update:**
    We used the `kubectl set image` command to update the image for the `nginx-deployment`. Importantly, we specified the container name (`nginx-container`) within the deployment to ensure the correct container's image was updated.
    ```bash
    kubectl set image deployment/nginx-deployment nginx-container=nginx:1.17
    ```
    *   `deployment/nginx-deployment`: Specifies that we are targeting the deployment named `nginx-deployment`.
    *   `nginx-container=nginx:1.17`: This part indicates that within the `nginx-deployment`, the container named `nginx-container` should have its image updated to `nginx:1.17`.

## Verification

1.  **Monitor the Rollout Status:**
    To ensure the rolling update completed successfully, we monitored its status:
    ```bash
    kubectl rollout status deployment/nginx-deployment
    ```
    This command confirmed when the rollout was finished and all new pods were ready.

2.  **Check Pods After Update:**
    After the rollout was complete, we verified that the pods were running with the updated image by inspecting the pods associated with the deployment.
    ```bash
    kubectl get pods -l app=nginx -o wide # Assuming 'app=nginx' is a label used by the deployment
    ```
    We checked the `IMAGE` column in the output to confirm that all pods were now using the `nginx:1.17` image.
