# ops-guides-ja

This repository is intended for creating, refining, and improving Japanese translations of various guidelines. It contains operations guides for development teams.

## Important Notes

- The guidelines included in this repository may not be related to each other in many cases.
- The convention rules of this repository (such as those defined in CONTRIBUTING.md) do not apply to the individual guidelines contained here.

## Documents

| Document | Description |
|----------|-------------|
| [Branching Strategy](./docs/branching-strategy.md) | Git-flow compliant branching rules |
| [Deployment Strategy](./docs/deployment-strategy.md) | Environment configuration and deployment/distribution rules |
| [Branching and Deployment Runbook](./docs/branching-and-deployment-runbook.md) | Step-by-step procedures for branching and deployment scenarios |

## Overview

This repository manages operations guides for development teams.

### Branching Strategy

- Git-flow compliant branch structure
- Semantic Versioning (Sem-Ver) based version management
- Feature-freeze, Hot-fix, and non-Hot-fix operations rules
- PR and review principles

### Deployment Strategy

- `develop` branch → DEV environment (auto/semi-auto/manual)
- `vX.Y.Z-rc.N` tag → STG environment (can be tagged on release/hotfix/master)
- `vX.Y.Z` tag → PRD environment (exists only on master)
- Mobile app distribution and review process flow

### Branching and Deployment Runbook

Step-by-step procedures for branching and deployment scenarios:

- Feature development
- Release branch creation
- RC tag creation and STG deployment
- Production release
- Hot-fix application
- Review rejection handling
- Post-release bug fixes

## Contributing

Please read [CONTRIBUTING.md](CONTRIBUTING.md) before contributing to this project.