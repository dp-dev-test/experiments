# Branch Protection Configuration for Guard Rails

This document outlines the required branch protection settings for the `main` branch to ensure the guard rails system works effectively.

## Required Branch Protection Rules

### Basic Protection
- **Require pull request reviews before merging**: ✅ Enabled
- **Require review from CODEOWNERS**: ✅ Enabled  
- **Required number of reviewers**: 1 (minimum)
- **Dismiss stale reviews when new commits are pushed**: ✅ Enabled
- **Require review from CODEOWNERS**: ✅ Enabled

### Status Checks
- **Require status checks to pass before merging**: ✅ Enabled
- **Require branches to be up to date before merging**: ✅ Enabled

#### Required Status Checks:
- `guard-rails-check` (from PR Guard Rails workflow)
- `security-scan` (from PR Guard Rails workflow) 
- `auto-label` (from Auto Label workflow)

### Additional Protection
- **Restrict pushes that create files**: ❌ Disabled (allow normal development)
- **Require signed commits**: ⚠️ Recommended but optional
- **Include administrators**: ✅ Enabled (admins follow same rules)
- **Allow force pushes**: ❌ Disabled
- **Allow deletions**: ❌ Disabled

## Label-Based Protection

The guard rails system uses labels to trigger additional protection:

### `requires-prod-approval` Label
When this label is present on a PR:
- **Additional required reviewers**: 2 (instead of 1)
- **Production team approval**: Required via CODEOWNERS
- **Platform team approval**: Required via CODEOWNERS

### Implementation Options

#### Option 1: GitHub API Configuration
Use the GitHub API or repository settings UI to configure branch protection:

```bash
# Example using GitHub CLI
gh api repos/:owner/:repo/branches/main/protection \
  --method PUT \
  --field required_status_checks='{"strict":true,"contexts":["guard-rails-check","security-scan","auto-label"]}' \
  --field enforce_admins=true \
  --field required_pull_request_reviews='{"required_approving_review_count":1,"dismiss_stale_reviews":true,"require_code_owner_reviews":true}' \
  --field restrictions=null
```

#### Option 2: Repository Settings UI
1. Go to repository **Settings** → **Branches**
2. Click **Add rule** for `main` branch
3. Configure the settings as outlined above

#### Option 3: Terraform Configuration
```hcl
resource "github_branch_protection" "main" {
  repository_id = "experiments"
  pattern       = "main"

  required_status_checks {
    strict   = true
    contexts = [
      "guard-rails-check",
      "security-scan", 
      "auto-label"
    ]
  }

  required_pull_request_reviews {
    required_approving_review_count = 1
    dismiss_stale_reviews          = true
    require_code_owner_reviews     = true
  }

  enforce_admins = true
}
```

## Team Configuration

Ensure the following GitHub teams exist and have appropriate members:

### Required Teams
- `@dp-dev-test/production-team` - Production environment stakeholders
- `@dp-dev-test/platform-team` - Platform/infrastructure owners
- `@dp-dev-test/infrastructure-team` - Infrastructure specialists
- `@dp-dev-test/development-team` - Development team members
- `@dp-dev-test/devops-team` - DevOps engineers
- `@dp-dev-test/security-team` - Security specialists
- `@dp-dev-test/admin-team` - Repository administrators
- `@dp-dev-test/documentation-team` - Documentation maintainers
- `@dp-dev-test/network-team` - Network/connectivity specialists
- `@dp-dev-test/application-team` - Application developers

### Team Permissions
- **Production Team**: Write access, required for base config changes
- **Platform Team**: Admin access, overall system oversight
- **Other Teams**: Write access for their respective areas

## Workflow Integration

The guard rails system integrates with branch protection through:

1. **Status Checks**: GitHub Actions workflows report success/failure
2. **CODEOWNERS**: Automatic reviewer assignment based on changed files
3. **Labels**: Additional requirements triggered by specific labels
4. **Comments**: Automated guidance and requirements documentation

## Testing Branch Protection

To test the guard rails system:

1. Create a test PR that modifies files in `weather-app-guard-rails-poc/base/`
2. Verify that the `requires-prod-approval` label is automatically added
3. Confirm that production team members are automatically requested as reviewers
4. Ensure the PR cannot be merged without required approvals

## Troubleshooting

### Common Issues
- **Status checks not appearing**: Check workflow triggers and permissions
- **CODEOWNERS not working**: Verify team names and member permissions
- **Labels not applying**: Check GitHub Actions permissions for PR writes

### Debug Commands
```bash
# Check branch protection status
gh api repos/:owner/:repo/branches/main/protection

# List required status checks
gh api repos/:owner/:repo/branches/main/protection/required_status_checks

# Check team membership
gh api orgs/:org/teams/:team/members
```