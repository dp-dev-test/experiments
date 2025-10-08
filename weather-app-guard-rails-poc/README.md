# Weather App Guard Rails PoC - Deployment for AKS

This project contains Kubernetes manifests for deploying a weather application guard rails proof-of-concept to Azure Kubernetes Service (AKS) using Kustomize.

## Structure

```
weather-app-guard-rails-poc/
├── base/                           # Base Kustomization resources
│   ├── deployment.yaml            # Weather app deployment
│   ├── service.yaml               # ClusterIP service
│   ├── configmap.yaml             # Configuration
│   └── kustomization.yaml         # Base kustomization
└── overlays/
    ├── dev/                       # Development overlay
    │   ├── configs/               # Development-specific configs
    │   │   ├── dev-app.conf      # Development nginx config
    │   │   └── dev-weather.json  # Development app config
    │   ├── deployment-patch.yaml  # Development deployment patches
    │   ├── service-patch.yaml     # NodePort service patch
    │   ├── namespace.yaml         # Development namespace
    │   └── kustomization.yaml     # Development kustomization
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
- Weather app guard rails PoC deployment with 2 replicas
- ClusterIP service on port 80
- ConfigMap for application configuration
- Health and readiness probes
- Resource requests and limits
- Security context and non-root user
- AKS-specific node selectors and tolerations

### Development Overlay
- 1 replica for resource efficiency
- NodePort service (port 30080) for easy access
- Debug mode enabled with verbose logging
- Mock data and test endpoints enabled
- Guard rails PoC features enabled for testing
- Relaxed security settings for development
- Lower resource limits (200m CPU, 256Mi memory)
- CORS enabled for local development

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
   kubectl get pods -n weather-app-guard-rails-poc-prod
   kubectl get services -n weather-app-guard-rails-poc-prod
   ```

4. **Check application logs:**
   ```bash
   kubectl logs -l app=weather-app-guard-rails-poc -n weather-app-guard-rails-poc-prod
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