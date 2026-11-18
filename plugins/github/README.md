# GitHub Plugin for Antigravity

Create and manage GitHub repositories, branches, commits, pull requests, and issues directly from Antigravity.

## Included Components

- **Skill (`github-manager`)**: Complete workflows for installing and authenticating Git & GitHub CLI (`gh`), creating public/private repositories, pushing local code, and managing pull requests and issues.
- **Rules (`rules/AGENTS.md`)**: Safety and authentication rules ensuring credentials are never committed and Windows binary paths are resolved automatically.

## Quick Setup

1. Install **Git** and **GitHub CLI (`gh`)** (ask the agent to install them for you via `winget`).
2. Authenticate your GitHub account via browser sign-in (`gh auth login`) or a Personal Access Token.
3. Ask the agent to create repositories, push code, or open pull requests!
