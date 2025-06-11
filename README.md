# Weather Forecast Service - Argo CD Application

This project contains the necessary configurations to deploy a weather forecast service using Argo CD. It includes Kubernetes manifests for the application deployment, service, and ingress, as well as an Argo CD application resource.

## Prerequisites

- Kubernetes cluster
- Argo CD installed and configured
- Access to a Git repository containing the application code

## Project Structure

```
weather-forecast-argocd
├── manifests
│   ├── application.yaml       # Argo CD application resource
│   ├── deployment.yaml        # Kubernetes deployment for the service
│   ├── service.yaml           # Kubernetes service definition
│   └── ingress.yaml           # Ingress configuration for routing
└── README.md                  # Project documentation
```

## Deployment Instructions

1. **Clone the Repository**: Clone this repository to your local machine or a server that has access to your Kubernetes cluster.

2. **Update the Manifests**: Modify the `manifests/deployment.yaml` file to specify the correct container image and any required environment variables.

3. **Deploy the Application**:
   - Ensure that your Argo CD is running and you have access to it.
   - Use the following command to add your Git repository to Argo CD:
     ```
     argocd repo add <your-repo-url> --username <your-username> --password <your-password>
     ```
   - Apply the Argo CD application manifest:
     ```
     kubectl apply -f manifests/application.yaml
     ```

4. **Access the Application**: Once the application is deployed, you can access it via the configured ingress. Make sure to check the ingress rules defined in `manifests/ingress.yaml`.

## Notes

- Ensure that your Kubernetes cluster has the necessary resources to run the weather forecast service.
- Monitor the application status using Argo CD's web interface or CLI commands.

# Argo CD Installation and Access Guide

## 1. Create the Argo CD Namespace

```sh
kubectl create namespace argocd
```

## 2. Install Argo CD

```sh
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

## 3. Expose the Argo CD API Server

To expose the Argo CD UI externally, patch the service to use a LoadBalancer:

```sh
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```

Alternatively, for local access, you can port-forward:

```sh
kubectl port-forward svc/argocd-server -n argocd 8088:443
```

## 4. Get the Argo CD Admin Password

```sh
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d
```

## 5. Download the Latest Argo CD CLI for Windows

```powershell
Invoke-WebRequest -Uri https://github.com/argoproj/argo-cd/releases/latest/download/argocd-windows-amd64.exe -OutFile argocd.exe
```

(Optional) Move the CLI to a directory in your PATH:

```powershell
Move-Item .\argocd.exe C:\Windows\System32\
```

---

**You can now access the Argo CD UI using the LoadBalancer's external IP or via [https://localhost:8088](https://localhost:8088) if using port-forwarding.**

## Login to argo cd from CLI
argocd login localhost:8088 --username admin --password {yourpassword}

## Add Your GitHub Repository to Argo CD
argocd repo add https://github.com/dp-dev-test/experiments.git --username gitusername --password yourpattoken