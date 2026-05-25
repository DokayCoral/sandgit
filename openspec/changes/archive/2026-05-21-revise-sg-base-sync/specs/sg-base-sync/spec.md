## ADDED Requirements

### Requirement: Switch to main branch from any branch
The skill SHALL switch the working directory to the `main` branch. If the current branch has uncommitted changes, the skill SHALL ask the user whether to save (commit) them before switching. If all changes are already committed, the skill SHALL switch to main directly.

#### Scenario: Non-main branch with uncommitted changes
- **WHEN** the user is on a non-main branch with uncommitted changes (modified, staged, or untracked files)
- **THEN** the skill SHALL list all affected files and ask the user whether to commit them before switching to main

#### Scenario: User confirms saving changes
- **WHEN** the user confirms they want to save the uncommitted changes
- **THEN** the skill SHALL execute `git add -A && git commit -m "sg-base-sync: auto save before switch"` and then switch to main

#### Scenario: User declines saving changes
- **WHEN** the user declines to save uncommitted changes
- **THEN** the skill SHALL abort and remain on the current branch

#### Scenario: Non-main branch with all changes committed
- **WHEN** the user is on a non-main branch with a clean working directory
- **THEN** the skill SHALL directly switch to `main` without prompting

#### Scenario: Already on main branch
- **WHEN** the user is already on the `main` branch
- **THEN** the skill SHALL skip branch switching and proceed

### Requirement: Optional remote sync
The skill SHALL treat local `main` as the authoritative baseline by default. If a remote `origin` exists, the skill SHALL ask the user whether to sync with the remote before proceeding.

#### Scenario: No remote configured
- **WHEN** the repository has no remote `origin`
- **THEN** the skill SHALL confirm local `main` as the baseline without any remote check

#### Scenario: Remote exists and user chooses to sync
- **WHEN** the user confirms they want to sync with `origin/main`
- **THEN** the skill SHALL execute `git fetch origin main` and `git pull --ff-only origin main`

#### Scenario: Remote exists and user declines to sync
- **WHEN** the user declines remote sync
- **THEN** the skill SHALL use local `main` as the baseline and proceed

#### Scenario: Remote sync fails with divergence
- **WHEN** `git pull --ff-only` fails due to branch divergence
- **THEN** the skill SHALL abort and instruct the user to resolve the divergence manually

### Requirement: Confirm environment ready
The skill SHALL output a summary confirming the environment is ready for subsequent sandbox operations.

#### Scenario: Sync completed successfully
- **WHEN** all steps complete without error
- **THEN** the skill SHALL report the current branch name, HEAD commit hash, and confirm readiness
