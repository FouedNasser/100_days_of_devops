# Day 63: Deploy Iron Gallery App on Kubernetes

## Challenge Description
The Nautilus DevOps team is deploying the "Iron Gallery" application, a custom web app, to the Kubernetes cluster. The deployment involves setting up a specific namespace, a frontend deployment (Iron Gallery), a backend database deployment (MariaDB/Iron DB), and their corresponding services.

## Key Concepts
-   **Kubernetes Namespace:** Logical isolation within the cluster (`iron-namespace-datacenter`).
-   **Multi-Tier Deployment:** Deploying separate frontend and backend components.
-   **Resource Limits:** Constraining CPU and Memory usage for containers.
-   **Environment Variables:** Injecting configuration (database credentials) into the container.
-   **Volumes:** Using `emptyDir` for temporary storage mounts.
-   **Services:** Using `ClusterIP` for internal DB communication and `NodePort` for external frontend access.

## Solution

1.  **Create the Manifest File**
    We created a comprehensive YAML file named `iron-gallery-full.yaml` that defines all the required resources:

    *   **Namespace:** `iron-namespace-datacenter`
    *   **Frontend Deployment (`iron-gallery-deployment-datacenter`):**
        *   Image: `kodekloud/irongallery:2.0`
        *   Resource Limits: `memory: 100Mi`, `cpu: 50m`
        *   Volumes: Two `emptyDir` volumes mounted at `/usr/share/nginx/html/data` and `/usr/share/nginx/html/uploads`.
    *   **Backend Deployment (`iron-db-deployment-datacenter`):**
        *   Image: `kodekloud/irondb:2.0`
        *   Env Vars: `MYSQL_DATABASE`, `MYSQL_ROOT_PASSWORD`, `MYSQL_PASSWORD`, `MYSQL_USER`.
        *   Volume: One `emptyDir` volume mounted at `/var/lib/mysql`.
    *   **Backend Service (`iron-db-service-datacenter`):**
        *   Type: `ClusterIP` on port `3306`.
    *   **Frontend Service (`iron-gallery-service-datacenter`):**
        *   Type: `NodePort` on port `80`, exposed via NodePort `32678`.

    ```yaml
    apiVersion: v1
    kind: Namespace
    metadata:
      name: iron-namespace-datacenter
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: iron-gallery-deployment-datacenter
      namespace: iron-namespace-datacenter
      labels:
        run: iron-gallery
    spec:
      replicas: 1
      selector:
        matchLabels:
          run: iron-gallery
      template:
        metadata:
          labels:
            run: iron-gallery
        spec:
          containers:
          - name: iron-gallery-container-datacenter
            image: kodekloud/irongallery:2.0
            resources:
              limits:
                memory: "100Mi"
                cpu: "50m"
            volumeMounts:
            - name: config
              mountPath: /usr/share/nginx/html/data
            - name: images
              mountPath: /usr/share/nginx/html/uploads
          volumes:
          - name: config
            emptyDir: {}
          - name: images
            emptyDir: {}
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: iron-db-deployment-datacenter
      namespace: iron-namespace-datacenter
      labels:
        db: mariadb
    spec:
      replicas: 1
      selector:
        matchLabels:
          db: mariadb
      template:
        metadata:
          labels:
            db: mariadb
        spec:
          containers:
          - name: iron-db-container-datacenter
            image: kodekloud/irondb:2.0
            env:
            - name: MYSQL_DATABASE
              value: "database_web"
            - name: MYSQL_ROOT_PASSWORD
              value: "Sup3rS3cr3t!"
            - name: MYSQL_PASSWORD
              value: "IronUserPass123!"
            - name: MYSQL_USER
              value: "iron_user"
            volumeMounts:
            - name: db
              mountPath: /var/lib/mysql
          volumes:
          - name: db
            emptyDir: {}
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: iron-db-service-datacenter
      namespace: iron-namespace-datacenter
    spec:
      type: ClusterIP
      selector:
        db: mariadb
      ports:
      - protocol: TCP
        port: 3306
        targetPort: 3306
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: iron-gallery-service-datacenter
      namespace: iron-namespace-datacenter
    spec:
      type: NodePort
      selector:
        run: iron-gallery
      ports:
      - protocol: TCP
        port: 80
        targetPort: 80
        nodePort: 32678
    ```

2.  **Apply the Configuration**
    We applied the manifest to the cluster:
    ```bash
    kubectl apply -f iron-gallery-full.yaml
    ```

## Verification

1.  **Check Pod Status:**
    We verified that all pods in the namespace were running:
    ```bash
    kubectl get pods -n iron-namespace-datacenter
    ```

2.  **Check Services:**
    We confirmed the services were correctly created and exposed:
    ```bash
    kubectl get svc -n iron-namespace-datacenter
    ```

3.  **Access Application:**
    We validated the deployment by accessing the Iron Gallery installation page via the browser at `http://<Node-IP>:32678`.
