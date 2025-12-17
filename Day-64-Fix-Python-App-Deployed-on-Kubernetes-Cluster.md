# Day 64: Fix Python App Deployed on Kubernetes Cluster

## Challenge Description
One of the DevOps engineers was trying to deploy a python app on Kubernetes cluster. Unfortunately, due to some mis-configuration, the application is not coming up. Please take a look into it and fix the issues. Application should be accessible on the specified nodePort.

The deployment name is `python-deployment-xfusion`, its using `poroko/flask-demo-appimage`. The deployment and service of this app is already deployed.
`nodePort` should be `32345` and `targetPort` should be python flask app's default port.

## Key Concepts
- **Kubernetes Deployments:** Managing desired states for Pods.
- **Kubernetes Services:** Exposing applications using `NodePort`.
- **Debugging Pods:** Using `kubectl describe` and `kubectl logs` to identify issues like `ImagePullBackOff`.
- **Container Ports:** Understanding the relationship between `port`, `targetPort`, and `nodePort`.

## Solution

First, we check the status of the resources on the `jump_host`:

```bash
kubectl get all
```

We notice that the pod is in `ImagePullBackOff` or `ErrImagePull` state. We describe the pod to find the root cause:

```bash
kubectl describe pod <pod-name>
```

The events show that the image `poroko/flask-app-demo` failed to pull because it doesn't exist. The correct image is `poroko/flask-demo-appimage`.

### 1. Update the Deployment
We update the deployment with the correct image:

```bash
kubectl edit deployment python-deployment-xfusion
```

In the editor, we change:
- `image: poroko/flask-app-demo` to `image: poroko/flask-demo-appimage`

### 2. Update the Service
Next, we ensure the service is correctly configured to use `NodePort` 32345 and points to the correct `targetPort` (5000 for Flask):

```bash
kubectl edit service python-service-xfusion
```

We ensure the `spec` section contains:
```yaml
spec:
  type: NodePort
  ports:
  - nodePort: 32345
    port: 5000
    protocol: TCP
    targetPort: 5000
```

## Verification
We verify that the pod is running and the service is correctly mapped:

```bash
kubectl get pods
kubectl get svc python-service-xfusion
```

The output should show the pod as `Running` and the service exposing port `5000:32345/TCP`.
