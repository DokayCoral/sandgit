## ADDED Requirements

### Requirement: Detect current branch and uncommitted changes
The skill SHALL inspect the current Git branch and detect any uncommitted changes (modified, staged, or untracked files) before performing sync operations.

#### Scenario: Clean working directory on main branch
- **WHEN** the user is on the `main` branch with no uncommitted changes
- **THEN** the skill proceeds directly to pulling the latest code

#### Scenario: Uncommitted changes detected
- **WHEN** the user has modified, staged, or untracked files in the working directory
- **THEN** the skill SHALL list all affected files and prompt the user to confirm before stashing

#### Scenario: User on a non-main branch
- **WHEN** the user is on a branch other than `main`
- **THEN** the skill SHALL notify the user and switch to `main` before syncing

### Requirement: Sync main branch to latest remote
The skill SHALL pull the latest commits from `origin/main` and update the local `main` branch. If no remote is configured, local main is treated as the authoritative baseline.

#### Scenario: No remote repository configured
- **WHEN** the repository has no remote named `origin` or no remote at all
- **THEN** the skill SHALL report that no remote exists and confirm local `main` as the current baseline

#### Scenario: Successful fast-forward sync
- **WHEN** the local `main` branch is behind `origin/main`
- **THEN** the skill SHALL execute `git pull --ff-only origin main` and report the updated commit range

#### Scenario: Local commits not yet pushed
- **WHEN** the local `main` branch has commits ahead of `origin/main`
- **THEN** the skill SHALL warn the user about unpushed commits and pause for confirmation before continuing

#### Scenario: Divergent branches
- **WHEN** local `main` and `origin/main` have diverged
- **THEN** the skill SHALL abort and instruct the user to resolve the divergence manually

### Requirement: Confirm clean environment ready for sandbox
The skill SHALL output a summary confirming the environment is ready for subsequent sandbox operations.

#### Scenario: Sync completed successfully
- **WHEN** all sync steps complete without error
- **THEN** the skill SHALL report the current main branch HEAD commit hash and confirm readiness
