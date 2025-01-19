# Make Your Wishes Come True - DevOps Project

## Project Overview
A DevOps implementation for a wish-tracking system that helps users maintain their yearly resolutions through automated processes and monitoring.

## Team
See [TEAM.md](TEAM.md) for team composition and roles.

## Repository Structure
- `/docs` - Project documentation
- `/src/pipelines` - Pipeline configurations
- `/.github/workflows` - GitHub Actions workflows

## Workflow
1. Create feature branch from `develop`
2. Create PR to merge into `develop`
3. Merge to `staging` for testing
4. Release to `main` for production

## Pipeline Setup

### Prerequisites
- Jenkins server installed
- Pipeline Utility Steps plugin installed
- Git plugin installed

### Jenkins Configuration
1. Create new Pipeline job
2. Configure Git SCM
3. Point to Jenkinsfile

### Running the Pipeline
1. Click "Build with Parameters"
2. Select month
3. Enter current day
4. Mark objectives completion
5. View HTML report in artifacts