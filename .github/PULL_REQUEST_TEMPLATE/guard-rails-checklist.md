## Pull Request Description

### Summary
Brief description of the changes and their purpose.

### Type of Change
- [ ] 🐛 Bug fix (non-breaking change which fixes an issue)
- [ ] ✨ New feature (non-breaking change which adds functionality)
- [ ] 💥 Breaking change (fix or feature that would cause existing functionality to not work as expected)
- [ ] 📚 Documentation update
- [ ] 🔧 Configuration change
- [ ] 🔒 Security update

### Applications Affected
- [ ] Weather App Guard Rails PoC
- [ ] Argo Events
- [ ] Argo Workflows
- [ ] Other: _____________

---

## 🛡️ Guard Rails Checklist

### Base Configuration Changes
If you've modified files in any `base/` directory:
- [ ] I understand this affects shared/base configurations
- [ ] I have tested these changes in a development environment
- [ ] I have considered the impact on production workloads
- [ ] I have a rollback plan if issues arise
- [ ] I have updated relevant documentation

### Production Environment Impact
If you've modified files in any `overlays/prod/` directory:
- [ ] I have assessed the production impact
- [ ] I have coordinated with the production team
- [ ] I have verified resource requirements and limits
- [ ] I have considered monitoring and alerting implications
- [ ] I have a deployment timeline/plan

### Kubernetes Configuration
If you've modified Kubernetes YAML files:
- [ ] I have validated the YAML syntax
- [ ] I have tested the configurations with `kubectl apply --dry-run`
- [ ] I have verified security contexts and permissions
- [ ] I have checked resource requests and limits
- [ ] I have ensured proper labeling and annotations

### Security Considerations
- [ ] No secrets or sensitive data are exposed in plain text
- [ ] RBAC permissions follow the principle of least privilege
- [ ] Network policies are appropriate for the application
- [ ] Container security contexts are properly configured
- [ ] Image references use specific tags (not `latest`)

---

## Testing

### Development Environment
- [ ] Changes tested in dev overlay
- [ ] No breaking changes to existing functionality
- [ ] Health checks and probes working correctly
- [ ] Application starts and responds as expected

### Integration Testing
- [ ] Kustomize build successful: `kubectl kustomize overlays/dev`
- [ ] Kustomize build successful: `kubectl kustomize overlays/prod`
- [ ] No syntax errors in Kubernetes manifests
- [ ] Resource validation passed

### Performance Impact
- [ ] Resource usage is within acceptable limits
- [ ] No significant performance degradation
- [ ] Scaling behavior verified (if applicable)

---

## Deployment Plan

### Pre-deployment
- [ ] Backup current configurations (if applicable)
- [ ] Notify relevant teams of planned changes
- [ ] Schedule deployment window (if required)

### Deployment Steps
1. 
2. 
3. 

### Post-deployment
- [ ] Monitor application health and metrics
- [ ] Verify all components are functioning correctly
- [ ] Update relevant documentation
- [ ] Communicate successful deployment to stakeholders

---

## Additional Notes

### Breaking Changes
If this is a breaking change, describe:
- What functionality is affected
- Migration steps required
- Timeline for transition

### Dependencies
List any dependencies on other PRs or external changes:
- 
- 

### Rollback Plan
Describe how to rollback these changes if needed:
- 
- 

---

## Checklist for Reviewers

### For Production Team (Base Config Changes)
- [ ] Impact assessment completed and documented
- [ ] Changes align with production standards
- [ ] Security implications reviewed
- [ ] Monitoring and alerting considerations addressed
- [ ] Deployment plan is sound

### For Infrastructure Team
- [ ] Kubernetes best practices followed
- [ ] Resource specifications are appropriate
- [ ] Security contexts properly configured
- [ ] Network policies reviewed (if applicable)

### For Security Team
- [ ] No security vulnerabilities introduced
- [ ] Secrets management properly implemented
- [ ] RBAC permissions are minimal and appropriate
- [ ] Container images are from trusted sources

---

*This PR will be automatically labeled and routed to appropriate reviewers based on the files changed.*