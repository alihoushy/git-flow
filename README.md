# Git Flow Tutorial

This repository is a personal learning resource for understanding and practicing Git Flow, a branching model for Git introduced by Vincent Driessen. Git Flow provides a structured way to manage feature development, releases, and hotfixes in a versioned software project. It uses a set of Git extensions to simplify high-level repository operations.

While Git Flow has been popular for projects with explicit versioning and multiple supported releases, note that for modern continuous delivery (e.g., web apps), simpler trunk-based workflows are often recommended as best practices. This tutorial focuses on Git Flow for educational purposes, covering all possible commands, workflows, and details.

## What is Git Flow?

Git Flow is a branching strategy that defines specific branch types and rules for their creation, merging, and deletion. It separates ongoing development from stable production code.

### Key Branches
- **master**: Represents production-ready code. Every commit is a deployable release, tagged with a version number.
- **develop**: Integration branch for the next release. Features are merged here.
- **Feature branches**: For new features, branched from develop and merged back into develop.
- **Release branches**: For preparing releases, branched from develop, with minor fixes only.
- **Hotfix branches**: For urgent production fixes, branched from master.

### Branch Lifecycle Summary

| Branch Type | Created From | Merged Into | Lifetime | Purpose |
|-------------|--------------|-------------|----------|---------|
| master     | —            | —           | Infinite | Production releases |
| develop    | —            | —           | Infinite | Next release integration |
| Feature    | develop      | develop     | Temporary | New features |
| Release    | develop      | master, develop | Temporary | Release preparation (bug fixes, metadata) |
| Hotfix     | master (or tag) | master, develop | Temporary | Critical production fixes |

## Installation

Git Flow requires a working Git installation. Install the `git-flow` extensions on your platform:

### macOS
- **Homebrew**: `brew install git-flow-avh`
- **Macports**: `port install git-flow-avh`

### Linux
- `apt-get install git-flow`

### Windows (Cygwin)
- Download and run the installer: `wget -q -O - --no-check-certificate https://raw.github.com/petervanderdoes/gitflow-avh/develop/contrib/gitflow-installer.sh install stable | bash`

You need wget and util-linux for installation.

## Initialization

Initialize Git Flow in an existing Git repository to set up branch naming conventions (defaults are recommended):

```bash
git flow init
```

This configures branches like `master`, `develop`, `feature/`, `release/`, `hotfix/`, and `support/`. Answer prompts or accept defaults.

## Feature Branches

Feature branches isolate new development work. They exist mainly in local repos.

### Start a New Feature
Creates a branch from `develop` and switches to it:

```bash
git flow feature start MYFEATURE
```

Equivalent plain Git:
```bash
git checkout -b feature/MYFEATURE develop
```

### Finish a Feature
Merges into `develop`, deletes the branch, and switches back:

```bash
git flow feature finish MYFEATURE
```

Equivalent plain Git:
```bash
git checkout develop
git merge --no-ff feature/MYFEATURE
git branch -d feature/MYFEATURE
git push origin develop
```

Use `--no-ff` to create a merge commit for history preservation.

### Publish a Feature (for Collaboration)
Push to remote:

```bash
git flow feature publish MYFEATURE
```

Equivalent: `git push origin feature/MYFEATURE`

### Pull a Published Feature
Fetch from remote:

```bash
git flow feature pull origin MYFEATURE
```

### Track a Remote Feature
```bash
git flow feature track MYFEATURE
```

All features must be merged into `develop` only — no direct merges to `master`.

## Release Branches

Release branches prepare a new production version, allowing bug fixes and metadata updates (e.g., version bumps) without new features.

### Start a Release
Branches from `develop` (optionally from a specific commit BASE):

```bash
git flow release start RELEASE [BASE]
```

Equivalent plain Git:
```bash
git checkout -b release/RELEASE develop
# Bump version (e.g., via script)
./bump-version.sh RELEASE
git commit -a -m "Bumped version to RELEASE"
```

Publish for team fixes:
```bash
git flow release publish RELEASE
```

Track a remote release:
```bash
git flow release track RELEASE
```

### Finish a Release
Merges to `master` (tagged), back to `develop`, and deletes the branch:

```bash
git flow release finish RELEASE
git push origin --tags  # Push tags
```

Equivalent plain Git:
```bash
git checkout master
git merge --no-ff release/RELEASE
git tag -a RELEASE -m "Release RELEASE"  # Use -s for signed tags
git checkout develop
git merge --no-ff release/RELEASE
git branch -d release/RELEASE
```

Resolve any merge conflicts (e.g., version numbers).

## Hotfix Branches

Hotfixes address critical issues in production quickly.

### Start a Hotfix
Branches from `master` (or tag, optionally from BASENAME):

```bash
git flow hotfix start VERSION [BASENAME]
```

Equivalent plain Git:
```bash
git checkout -b hotfix/VERSION master
./bump-version.sh VERSION
git commit -a -m "Bumped version to VERSION"
```

### Finish a Hotfix
Merges to `master` (tagged), `develop`, and deletes the branch:

```bash
git flow hotfix finish VERSION
git push origin --tags
```

Equivalent plain Git:
```bash
git checkout master
git merge --no-ff hotfix/VERSION
git tag -a VERSION -m "Hotfix VERSION"
git checkout develop
git merge --no-ff hotfix/VERSION
git branch -d hotfix/VERSION
```

If a release branch exists, merge hotfix into it instead of `develop`.

## Best Practices

- **Commit Often, Push Once**: Make small, focused commits per issue, then push when ready.
- **Descriptive Commit Messages**: Use clear, concise messages (e.g., "Fix bug in login form").
- **Keep Branches Up-to-Date**: Regularly pull/rebase from `develop` to avoid conflicts.
- **Rebase vs. Merge**: Rebase private branches for clean history; never rebase shared branches. Use merges for integration.
- **Implement CI/CD**: Branch from trunk, use PRs, and automate tests/builds on all branches.
- **No New Features in Release/Hotfix**: Only bug fixes and metadata.
- **Use --no-ff for Merges**: Preserves history and allows easy reversion.
- **Tag Releases**: Always tag `master` merges for traceability.
- **Automate Deployments**: Use Git hooks on `master` for builds/deploys.
- **Adapt for Team Size**: For small teams, consider lighter variants; for large, enforce PR reviews.
- **Feature Branches in Local Repos**: Push only if collaborating.
- **Handle Merge Conflicts Early**: Especially version bumps in releases/hotfixes.

Git Flow works alongside plain Git commands. Avoid the beta 'support' branches.

## Resources
- [Git Flow Cheatsheet](https://danielkummer.github.io/git-flow-cheatsheet/)
- [Original Git Branching Model](https://nvie.com/posts/a-successful-git-branching-model/)
- [Atlassian Git Flow Tutorial](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)
- [AWS Git Flow Guidance](https://docs.aws.amazon.com/prescriptive-guidance/latest/choosing-git-branch-approach/gitflow-branching-strategy.html)

To practice, clone this repo and experiment with the commands!
