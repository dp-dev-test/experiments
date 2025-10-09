# Guardrails System Documentation

This document describes the comprehensive guard rails system implemented for the `weather-app-guard-rails-poc` project to ensure safe and controlled deployments.

## Overview

The guard rails system enforces policies for pull request merges, particularly when changes affect base configurations that could impact production environments.

### Key Features
- 🛡️ **Automatic detection** of base configuration changes
- 🏷️ **Intelligent labeling** based on files modified
- 👥 **Mandatory approvals** from designated teams
- 🔒 **Security scanning** and compliance checks
- 📋 **Structured PR templates** with comprehensive checklists
- 🚨 **Branch protection** enforcement

## System Components

### 1. GitHub Actions Workflows

#### PR Guard Rails Workflow (`.github/workflows/pr-guard-rails.yml`)
- **Trigger**: Pull requests affecting `weather-app-guard-rails-poc/**`
- **Purpose**: Detect base config changes and enforce approval requirements
- **Actions**:
  - Detects changes in `base/` directory
  - Checks for production overlay existence
  - Automatically adds required labels
  - Posts detailed comments with requirements
  - Performs security scans

#### Auto Label Workflow (`.github/workflows/auto-label.yml`)
- **Trigger**: All pull requests
- **Purpose**: Automatically label PRs based on changed files
- **Actions**:
  - Adds contextual labels based on file patterns
  - Adds size labels based on PR complexity
  - Routes to appropriate teams

### 2. CODEOWNERS Configuration

The `.github/CODEOWNERS` file defines mandatory reviewers:

```
weather-app-guard-rails-poc/base/ @dp-dev-test/production-team @dp-dev-test/platform-team
weather-app-guard-rails-poc/overlays/prod/ @dp-dev-test/production-team
```

### 3. Branch Protection Rules

Required settings for the `main` branch:
- ✅ Require pull request reviews before merging
- ✅ Require review from CODEOWNERS
- ✅ Require status checks to pass
- ✅ Required status checks: `guard-rails-check`, `security-scan`, `auto-label`

### 4. PR Templates

#### Default Guard Rails Template (`pull_request_template.md`)
Comprehensive guard rails checklist automatically applied to all PRs, including:
- Base configuration impact assessment
- Production environment considerations
- Security and compliance checks
- Testing requirements
- Deployment planning
- Reviewer guidance

## Policy Enforcement

### Base Configuration Protection

**Policy**: If a PR modifies files in any `base/` directory AND the application has a `overlays/prod/` directory, then production team approval is mandatory.

**Implementation**:
1. GitHub Actions detects base config changes
2. Checks for production overlay existence
3. Adds `requires-prod-approval` label
4. CODEOWNERS enforces reviewer requirements
5. Branch protection prevents merge without approval

### Automatic Labeling

Labels are automatically applied based on file patterns:

| Pattern | Label | Purpose |
|---------|-------|---------|
| `**/base/**` | `base-config` | Flags base configuration changes |
| `**/overlays/dev/**` | `dev-environment` | Development environment changes |
| `**/overlays/prod/**` | `prod-environment` | Production environment changes |
| `**/README.md`, `**/*.md` | `documentation` | Documentation updates |

### Security Scanning

Every PR affecting applications triggers:
- ✅ Manifest validation
- ✅ Secret scanning with TruffleHog
- ✅ Policy compliance verification
- ✅ Context validation

## Team Structure

### Required Teams

| Team | Responsibility | Required For |
|------|----------------|--------------|
| `@dp-dev-test/production-team` | Production oversight | Base configs, prod overlays |
| `@dp-dev-test/platform-team` | Platform governance | All changes (fallback) |

## Usage Examples

### Scenario 1: Base Configuration Change

**Files Changed**: `weather-app-guard-rails-poc/base/deployment.yaml`

**Guard Rails Response**:
1. ✅ Detects base config change
2. ✅ Finds production overlay exists
3. ✅ Adds labels: `requires-prod-approval`, `base-config-change`, `needs-review`
4. ✅ Posts detailed comment with requirements
5. ✅ CODEOWNERS requests production team review
6. ❌ PR cannot merge until approved

### Scenario 2: Development Only Change

**Files Changed**: `weather-app-guard-rails-poc/overlays/dev/deployment-patch.yaml`

**Guard Rails Response**:
1. ✅ Detects dev environment change
2. ✅ Adds labels: `dev-environment`, `weather-app`
3. ✅ CODEOWNERS requests development team review
4. ✅ No additional restrictions (dev-only change)

### Scenario 3: Documentation Update

**Files Changed**: `weather-app-guard-rails-poc/README.md`

**Guard Rails Response**:
1. ✅ Adds labels: `documentation`, `weather-app`
2. ✅ Minimal review requirements
3. ✅ Fast-track approval process

## Setup Instructions

### 1. Configure Branch Protection

```bash
gh api repos/dp-dev-test/experiments/branches/main/protection \
  --method PUT \
  --field required_status_checks='{"strict":true,"contexts":["guard-rails-check","security-scan","auto-label"]}' \
  --field enforce_admins=true \
  --field required_pull_request_reviews='{"required_approving_review_count":1,"dismiss_stale_reviews":true,"require_code_owner_reviews":true}'
```

### 2. Create GitHub Teams

Ensure all teams referenced in CODEOWNERS exist:
- production-team
- platform-team  
- infrastructure-team
- security-team
- devops-team
- development-team
- admin-team
- documentation-team
- network-team
- application-team

### 3. Configure Team Permissions

Give teams appropriate repository access:
- **Admin teams**: Admin access
- **Production/Platform teams**: Write access
- **Other teams**: Write access for their areas

### 4. Test the System

1. Create test PR modifying `weather-app-guard-rails-poc/base/deployment.yaml`
2. Verify automatic labeling
3. Confirm reviewer assignment
4. Test merge prevention without approval

## Troubleshooting

### Common Issues

#### Labels Not Applied
- Check GitHub Actions permissions
- Verify workflow triggers
- Review GitHub token scopes

#### CODEOWNERS Not Working
- Verify team names match exactly
- Check team member permissions
- Ensure CODEOWNERS syntax is correct

#### Status Checks Failing
- Review workflow logs
- Check required contexts in branch protection
- Verify GitHub Actions are enabled

### Debug Commands

```bash
# Check current branch protection
gh api repos/dp-dev-test/experiments/branches/main/protection

# List repository teams
gh api orgs/dp-dev-test/teams

# Check team membership
gh api orgs/dp-dev-test/teams/production-team/members

# View recent workflow runs
gh run list --repo dp-dev-test/experiments
```

## Customization

### Adding New Applications

To extend guard rails to new applications:

1. **Update workflows**: Add new paths to workflow triggers
2. **Update CODEOWNERS**: Add patterns for new application paths
3. **Update labeler**: Add labeling rules for new patterns
4. **Update documentation**: Document new policies

Example for new app `my-new-app`:

```yaml
# In pr-guard-rails.yml
paths:
  - 'weather-app-guard-rails-poc/**'
  - 'my-new-app/**'  # Add this

# In CODEOWNERS
my-new-app/base/ @dp-dev-test/production-team

# In labeler.yml
my-new-app:
  - my-new-app/**
```

### Modifying Policies

To change approval requirements:

1. **Update CODEOWNERS**: Modify team assignments
2. **Update workflows**: Change label logic or requirements
3. **Update branch protection**: Modify required reviewers count
4. **Test changes**: Verify new policies work as expected

## Best Practices

### For Developers
- 🔍 **Review labels** automatically applied to your PRs
- 📋 **Use appropriate PR templates** for complex changes
- 🧪 **Test in development** before modifying base configs
- 📚 **Update documentation** when changing configurations

### For Reviewers
- 🛡️ **Understand guard rails context** from automated comments
- 🔒 **Review security implications** of configuration changes
- 📊 **Assess production impact** before approving base config changes
- ✅ **Verify testing** has been completed in dev environments

### For Administrators
- 🔧 **Monitor workflow success rates** and adjust as needed
- 👥 **Keep team memberships** current and accurate
- 📈 **Review guard rails effectiveness** regularly
- 🚀 **Update policies** as the system evolves

---

*This guard rails system provides comprehensive protection while maintaining development velocity. For questions or issues, contact the platform team.*