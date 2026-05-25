## ADDED Requirements

### Requirement: Create test sandbox branch from main
The skill SHALL create a new `sgtest/<name>` branch from `main` as the test sandbox workspace. The branch name suffix SHALL be provided by the user.

#### Scenario: User provides a valid branch name
- **WHEN** the user provides a short descriptive name for the test sandbox
- **THEN** the skill SHALL create a new branch `sgtest/<name>` from `main` and switch to it

#### Scenario: Branch name already exists
- **WHEN** the target `sgtest/<name>` branch already exists
- **THEN** the skill SHALL warn the user and ask whether to reuse the existing branch or choose a new name

#### Scenario: Not on main branch
- **WHEN** the current branch is not `main`
- **THEN** the skill SHALL prompt the user to run `sg-base-sync` first to switch to main

### Requirement: Assist with test writing in sandbox
The skill SHALL guide the user through writing test code within the sandbox branch. Within the sandbox, the user SHALL have full read/write permissions to code files in the development environment, including creating, modifying, or deleting test files.

#### Scenario: User writes test files
- **WHEN** the user creates or edits test files in the sandbox
- **THEN** the skill SHALL assist with test code generation and review without restricting file operations

#### Scenario: User wants to iterate on tests
- **WHEN** the user modifies test files multiple times
- **THEN** the skill SHALL support iterative editing and debugging within the sandbox

### Requirement: Review item 1 — Test coverage description
After test code is written, the skill SHALL present a description of what functionality the tests cover. The user SHALL confirm before proceeding to the next review item.

#### Scenario: User confirms coverage
- **WHEN** the test coverage description is presented and the user confirms it is complete
- **THEN** the skill SHALL proceed to the next review item

#### Scenario: User requests coverage revision
- **WHEN** the user finds missing or incorrect items in the coverage description
- **THEN** the skill SHALL allow revision and re-presentation

### Requirement: Review item 2 — Test methodology description
The skill SHALL present how each test is structured and how verification is performed. The user SHALL confirm before proceeding to the next review item.

#### Scenario: User confirms methodology
- **WHEN** the test methodology description is presented and the user confirms it is correct
- **THEN** the skill SHALL proceed to the next review item

#### Scenario: User requests methodology revision
- **WHEN** the user finds issues with the test methodology
- **THEN** the skill SHALL allow revision and re-presentation

### Requirement: Review item 3 — Static case verification
The skill SHALL run the test code against static mock data to prove the test logic itself is correct. The user SHALL confirm the results before the sandbox is marked compliant.

#### Scenario: Static cases pass and user confirms
- **WHEN** static test cases execute successfully and the user confirms the results
- **THEN** the skill SHALL mark the test sandbox as compliant and output one test document summarizing coverage, methodology, and verification results

#### Scenario: Static cases fail
- **WHEN** static test cases fail
- **THEN** the skill SHALL report the failures and allow the user to fix the test code and re-run

#### Scenario: User wants to discard the sandbox
- **WHEN** the user decides the test approach is not viable at any review stage
- **THEN** the skill SHALL suggest calling `sg-branch-abort` to discard the sandbox
