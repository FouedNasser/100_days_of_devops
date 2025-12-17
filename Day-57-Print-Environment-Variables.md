# Day 57: Print Environment Variables

## Challenge Description
The Nautilus DevOps team needs to test a sample deployment that handles environment variables. The requirements are to create a Pod named `print-envars-greeting` with the following specifications:
-   **Container Name:** `print-env-container`
-   **Image:** `bash`
-   **Environment Variables:**
    -   `GREETING`: "Welcome to"
    -   `COMPANY`: "DevOps"
    -   `GROUP`: "Industries"
-   **Command:** Execute `/bin/sh -c 'echo "$(GREETING) $(COMPANY) $(GROUP)"'` to print the concatenated environment variables.
-   **Restart Policy:** `Never` (to prevent a crash loop after the command completes).

## Key Concepts
-   **Kubernetes Pods:** The smallest deployable units of computing that you can create and manage in Kubernetes.
-   **Environment Variables:** Injecting configuration data into containers at runtime.
-   **Pod Restart Policy:** Controlling how Kubernetes handles container termination (e.g., `Always`, `OnFailure`, `Never`).

## Solution

1.  **Create the Pod Manifest**
    We created a YAML file named `print-envars-greeting.yaml` to define the Pod resource:

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: print-envars-greeting
    spec:
      restartPolicy: Never
      containers:
      - name: print-env-container
        image: bash
        env:
        - name: GREETING
          value: "Welcome to"
        - name: COMPANY
          value: "DevOps"
        - name: GROUP
          value: "Industries"
        command: ["/bin/sh", "-c", "echo \"$(GREETING) $(COMPANY) $(GROUP)\""]
    ```

2.  **Deploy the Pod**
    We applied the manifest to the Kubernetes cluster:
    ```bash
    kubectl apply -f print-envars-greeting.yaml
    ```

3.  **Verify the Output**
    After the Pod started and completed its execution, we checked the logs to verify the output:
    ```bash
    kubectl logs -f print-envars-greeting
    ```
    **Expected Output:**
    ```
    Welcome to DevOps Industries
    ```
    This confirmed that the environment variables were correctly set and accessed by the container command.
