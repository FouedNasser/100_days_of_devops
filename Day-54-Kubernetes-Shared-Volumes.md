# Day 54: Kubernetes Shared Volumes

## Challenge Description
We need to create a Kubernetes Pod named `volume-share-devops` containing two containers that share a temporary storage volume.
- **Container 1**: `volume-container-devops-1` (Image: `fedora:latest`), mounts volume at `/tmp/media`.
- **Container 2**: `volume-container-devops-2` (Image: `fedora:latest`), mounts volume at `/tmp/demo`.
- **Volume**: Named `volume-share` of type `emptyDir`.
- **Verification**: Create a file in one container and verify its existence in the other.

## Key Concepts
- **Multi-Container Pods**: Running multiple containers in a single Pod that share the same network and storage lifecycle.
- **emptyDir Volume**: A transient volume type that is created when a Pod is assigned to a node and exists as long as that Pod is running. It is perfect for sharing temporary data between containers.
- **VolumeMounts**: Mounting the same volume at different paths in different containers.

## Solution

1.  **Create the Pod Manifest**
    We created a YAML file named `volume-share-pod.yaml` with the following content:

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: volume-share-devops
    spec:
      volumes:
      - name: volume-share
        emptyDir: {}
      containers:
      - name: volume-container-devops-1
        image: fedora:latest
        command: ["sleep", "infinity"]
        volumeMounts:
        - name: volume-share
          mountPath: /tmp/media
      - name: volume-container-devops-2
        image: fedora:latest
        command: ["sleep", "infinity"]
        volumeMounts:
        - name: volume-share
          mountPath: /tmp/demo
    ```

2.  **Deploy the Pod**
    We applied the manifest to the cluster:
    ```bash
    kubectl apply -f volume-share-pod.yaml
    ```

3.  **Verify Pod Status**
    We waited for the pod to reach the `Running` state:
    ```bash
    kubectl get pods volume-share-devops
    ```

## Verification

1.  **Create a Test File in Container 1**
    We executed into the first container and created a file named `media.txt` in the shared mount path:
    ```bash
    kubectl exec -it volume-share-devops -c volume-container-devops-1 -- touch /tmp/media/media.txt
    ```

2.  **Verify File Existence in Container 2**
    We executed into the second container to confirm the file exists in its mounted path:
    ```bash
    kubectl exec -it volume-share-devops -c volume-container-devops-2 -- ls -l /tmp/demo/media.txt
    ```
    The command should list the `media.txt` file, confirming the volume is successfully shared.
