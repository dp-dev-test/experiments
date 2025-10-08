# Weather App Deployment for AKS

This project contains Kubernetes manifests for deploying a weather application to Azure Kubernetes Service (AKS) using Kustomize.

## Structure

```
weather-app/
├── base/                           # Base Kustomization resources
│   ├── deployment.yaml            # Weather app deployment
│   ├── service.yaml               # ClusterIP service
│   ├── configmap.yaml             # Configuration
│   └── kustomization.yaml         # Base kustomization
└── overlays/
    └── prod/                      # Production overlay
        ├── configs/               # Production-specific configs
        │   ├── prod-app.conf     # Production nginx config
        │   └── prod-weather.json # Production app config
        ├── deployment-patch.yaml  # Production deployment patches
        ├── service-patch.yaml     # LoadBalancer service patch
        ├── resource-limits-patch.yaml # Resource limits JSON patch
        ├── namespace.yaml         # Production namespace
        └── kustomization.yaml     # Production kustomization
```

## Features

### Base Configuration
- Weather app deployment with 2 replicas
- ClusterIP service on port 80
- ConfigMap for application configuration
- Health and readiness probes
- Resource requests and limits
- Security context and non-root user
- AKS-specific node selectors and tolerations

### Production Overlay
- 5 replicas for high availability
- Pod anti-affinity for better distribution
- LoadBalancer service with Azure internal load balancer
- Production-specific resource limits (2 CPU, 2Gi memory)
- Production environment variables
- Enhanced security headers
- Monitoring and metrics endpoints

## Deployment Instructions

### Prerequisites
1. AKS cluster running and kubectl configured
2. Kustomize installed (or use `kubectl apply -k`)
3. Azure Container Registry (ACR) for custom images

### Deploy to Production

1. **Build and view the manifests:**
   ```bash
   kubectl kustomize overlays/prod
   ```

2. **Apply to AKS cluster:**
   ```bash
   kubectl apply -k overlays/prod
   ```

3. **Verify deployment:**
   ```bash
   kubectl get pods -n weather-app-prod
   kubectl get services -n weather-app-prod
   ```

4. **Check application logs:**
   ```bash
   kubectl logs -l app=weather-app -n weather-app-prod
   ```

### Update Configuration

1. **Update production configuration:**
   - Edit files in `overlays/prod/configs/`
   - Apply changes: `kubectl apply -k overlays/prod`

2. **Update image version:**
   - Edit `overlays/prod/kustomization.yaml`
   - Update the `newTag` field in the `images` section

### Monitoring

- Health check: `http://service-ip/health`
- Readiness check: `http://service-ip/ready`
- Metrics (production): `http://service-ip/metrics`

## AKS-Specific Features

- **Node Selection**: Targets Linux nodes with specific instance types
- **Spot Instance Support**: Tolerates spot instance scheduling
- **Azure Load Balancer**: Internal load balancer with subnet specification
- **Security**: Non-root container execution and read-only filesystem
- **High Availability**: Pod anti-affinity and multiple replicas

## Customization

To create additional environments (staging, dev), create new overlay directories:

```bash
mkdir -p overlays/staging
# Copy and modify prod files
```

Update the kustomization.yaml files to reference appropriate:
- Image tags
- Resource limits
- Replica counts
- Environment variables