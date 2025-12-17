# Day 62: Manage Secrets in Kubernetes

## Challenge Description
The Nautilus DevOps team needs to securely store license information for tools deployed in the Kubernetes cluster. The requirements are:
1.  **Create a Secret:** Create a generic secret named `news` from an existing file `/opt/news.txt` on the jump host.
2.  **Create a Pod:** Deploy a Pod named `secret-xfusion` with a container `secret-container-xfusion` using the `debian:latest` image.
3.  **Mount the Secret:** Mount the created secret `news` to the path `/opt/apps` inside the container.
4.  **Keep Pod Running:** Use a sleep command to keep the container running for verification.

## Key Concepts
-   **Kubernetes Secrets:** Objects that let you store and manage sensitive information, such as passwords, OAuth tokens, and ssh keys, separately from Pod definitions and container images.
-   **Mounting Secrets:** Secrets can be mounted as data volumes or exposed as environment variables to be used by a container.

## Solution

1.  **Create the Secret**
    We used the `kubectl create secret` command to generate the secret directly from the provided file:
    ```bash
    kubectl create secret generic news --from-file=/opt/news.txt
    ```

2.  **Create the Pod Manifest**
    We created a YAML file named `secret-xfusion.yaml` to define the Pod and the volume mount for the secret:

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: secret-xfusion
    spec:
      containers:
      - name: secret-container-xfusion
        image: debian:latest
        command: ["sleep", "infinity"]
        volumeMounts:
        - name: secret-volume
          mountPath: /opt/apps
      volumes:
      - name: secret-volume
        secret:
          secretName: news
    ```

3.  **Deploy the Pod**
    We applied the manifest to the Kubernetes cluster:
    ```bash
    kubectl apply -f secret-xfusion.yaml
    ```

## Verification

1.  **Check Pod Status:**
    We verified that the pod was up and running:
    ```bash
    kubectl get pod secret-xfusion
    ```

2.  **Verify Secret Content:**
    We executed into the container to confirm that the secret file was correctly mounted and accessible:
    ```bash
    kubectl exec -it secret-xfusion -- ls /opt/apps
    kubectl exec -it secret-xfusion -- cat /opt/apps/news.txt
    ```
    This confirmed that the file `news.txt` was present in the `/opt/apps` directory and contained the expected secret data.
