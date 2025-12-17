# Day 53: Resolve VolumeMounts Issue in Kubernetes

## Challenge Description
We encountered an issue with our Nginx and PHP-FPM setup on the Kubernetes cluster where the functionality was halted. The goal was to investigate the pod `nginx-phpfpm` and its configmap `nginx-config`, identify the misconfiguration regarding volume mounts, and fix it. Finally, we needed to copy a provided `index.php` file to the web root and verify accessibility.

## Key Concepts
- **Kubernetes Pods**: Managing multi-container pods (Nginx and PHP-FPM sidecar pattern).
- **VolumeMounts**: Sharing data between containers in a pod using `emptyDir` volumes.
- **ConfigMaps**: Injecting configuration files into containers.
- **Troubleshooting**: Identifying path mismatches between application configuration (Nginx root) and container filesystem mounts.

## Solution

1.  **Analyze the Configuration**
    We inspected the running pod and configmap to understand the setup.
    ```bash
    kubectl get po nginx-phpfpm -o yaml
    kubectl get cm nginx-config -o yaml
    ```
    We observed that the `nginx-config` ConfigMap defined the root directory as `/var/www/html`. However, the `nginx-container` in the pod had the shared volume mounted at `/usr/share/nginx/html`. This mismatch caused Nginx to look in the wrong place for files.

2.  **Export and Modify Pod Configuration**
    We exported the current pod configuration to a file for editing.
    ```bash
    kubectl get po nginx-phpfpm -o yaml > nginx-phpfpm.yaml
    ```
    We edited `nginx-phpfpm.yaml` to update the `mountPath` for the `nginx-container` to match the configuration and the PHP-FPM container.

    **Change:**
    From:
    ```yaml
    - mountPath: /usr/share/nginx/html
      name: shared-files
    ```
    To:
    ```yaml
    - mountPath: /var/www/html
      name: shared-files
    ```

3.  **Apply the Fix**
    We replaced the running pod with the updated configuration.
    ```bash
    kubectl replace -f nginx-phpfpm.yaml --force
    ```

4.  **Deploy Application Code**
    Once the pod was running, we copied the `index.php` file from the jump host to the shared document root within the container.
    ```bash
    kubectl cp /home/thor/index.php nginx-phpfpm:/var/www/html -c nginx-container
    ```

## Verification
We verified the solution by accessing the "Website" button on the top bar, which successfully loaded the PHP application served by Nginx.
