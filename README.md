# ops-guides-ja

This repository contains operations guides for development teams.

## Documents

| Document | Description |
|----------|-------------|
| [Branching Strategy](./docs/branching-strategy.md) | Git-flow compliant branching rules |
| [Deployment Strategy](./docs/deployment-strategy.md) | Environment configuration and deployment/distribution rules |
| [Runbook](./docs/runbook.md) | Step-by-step procedures for common scenarios |

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

### Runbook

Step-by-step procedures for common operational scenarios:

- Feature development
- Release branch creation
- RC tag creation and STG deployment
- Production release
- Hot-fix application
- Review rejection handling
- Post-release bug fixes

## Contributing

Please read [CONTRIBUTING.md](CONTRIBUTING.md) before contributing to this project.