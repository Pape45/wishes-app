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

## Setup Instructions


## Pipeline Setup

### Prerequisites
- Jenkins server installed
- Docker installed
- Jenkins Pipeline plugin installed

### Jenkins Configuration
1. Create new Pipeline job
2. Configure Git SCM
3. Set build triggers
4. Point to Jenkinsfile in repository root

### Running the Pipeline
1. Open Jenkins dashboard
2. Select the pipeline job
3. Click "Build with Parameters"
4. Select month and enter username
5. Click "Build"
