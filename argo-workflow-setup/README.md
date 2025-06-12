# Prerequisites

- Kubernetes cluster
- Argo CD installed and configured
- ArgoCD and Argo CLI installed

# Installing Argo Workflows on AKS

Follow these steps to install Argo Workflows in your Azure Kubernetes Service (AKS) cluster:

## 1. Create a Namespace for Argo Workflows

```sh
kubectl create namespace argo
```

## 2. Install Argo Workflows Using Kubectl

For local installation add following line to install.yaml

```sh
        - --auth-mode=server
```

```sh
kubectl apply -n argo -f .\argo-workflow-setup\install.yaml
```

## 3. Apply the Role and RoleBinding to create workflowtaskresults resources

```sh
kubectl apply -f .\argo-workflow-setup\argo-workflow-role.yaml
kubectl apply -f .\argo-workflow-setup\argo-workflow-rolebinding.yaml
```

## 4. Verify installation:

```sh
kubectl get pods -n argo
```

## 5. (Optional) Access the Argo Workflows UI

To access the Argo Workflows UI locally, run:

```sh
kubectl -n argo port-forward deployment/argo-server 2746:2746
```

Then open [https://localhost:2746](https://localhost:2746/workflows/?&limit=50) in your browser.

## 6. (Optional) Submit sample workflow

### Submit sample workflow from local yaml

```sh
argo submit .\argo-workflows\helloworld-workflow.yaml --watch
```

### Submit sample workflow from remote yaml

```sh
argo submit https://raw.githubusercontent.com/argoproj/argo-workflows/master/examples/hello-world.yaml --watch
```

---

**Note:**  
- For production, consider securing the UI and configuring authentication.