# Branch Strategy

## Branches
- `main`: Production-ready code
- `staging`: Pre-production testing
- `develop`: Development integration
- `feature/*`: Individual features
- `hotfix/*`: Emergency fixes

## Workflow Rules
1. Feature Development
   - Create from: `develop`
   - Merge to: `develop`
   - Naming: `feature/US-[MONTH]-[NUM]`

2. Staging Deployment
   - Create from: `develop`
   - Merge to: `staging`
   - Requires: Pipeline success

3. Production Release
   - Create from: `staging`
   - Merge to: `main`
   - Requires: Full approval

4. Hotfixes
   - Create from: `main`
   - Merge to: `main` and `develop`
   - Naming: `hotfix/[ISSUE-NUM]`