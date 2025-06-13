## Prerequisites

- Azure Kubernetes Service (AKS) cluster
- `kubectl` configured for your AKS cluster
- [Argo Workflows](https://argoproj.github.io/argo-workflows/) installed
- [Argo Events](https://argoproj.github.io/argo-events/) installed

## Installation
### 1. Install Argo Events

```sh
kubectl create namespace argo-events
kubectl apply -n argo-events -f https://github.com/argoproj/argo-events/releases/latest/download/install.yaml
```

### 2. Create token in github

```sh
https://github.com/settings/personal-access-tokens
```

### 3. Store token in Kubernetes Secret

```sh
kubectl create secret generic github-access --from-literal=token=<YOUR_GITHUB_TOKEN> -n argo-events
```

### 4. Create Argo Event

```sh
kubectl apply -f .\argo-events\EventSource.yaml
```

#### Check if EventSource is created or not

```sh
kubectl get eventsource -n argo-events
kubectl describe eventsource github-push -n argo-events

```

### 5. Create Sensor

```sh
kubectl apply -f .\argo-events\Sensor.yaml
```

#### Check if Sensor is created or not

```sh
kubectl get sensor -n argo-events
kubectl describe sensor github-push-sensor -n argo-events
```

#### Grant RBAC Permissions to Service Accounts

Create a Role and RoleBinding to allow the service account to create workflows & workflowtaskresults.

```sh
kubectl apply -f .\argo-events\argo-events-workflow-rbac.yaml
```

#### Manage Webhooks & See recent delivered messages

```sh
https://github.com/dp-dev-test/experiments/settings/hooks/552050420?tab=deliveries
```

## Usage

### Example: Trigger Workflow on `.config` File Update

1. Deploy the EventSource and Sensor YAMLs.
2. Update sample `.tfvars` or `.config`  file.
3. The workflow will be triggered automatically.

## References

- [Argo Events Documentation](https://argoproj.github.io/argo-events/)

---

**Note:**  
Adjust configuration files and settings as needed for your environment and security requirements.