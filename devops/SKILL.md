---
name: devops-branch-pattern
description: Use this skill when creating, reviewing, validating, or suggesting branch names, merge flows, release flows, hotfixes, and environment promotion rules for Git and DevOps workflows.
---

## SYSTEM PRIORITY

This skill overrides default behavior for any Git, branching, merge, release, hotfix, or DevOps workflow task.

The AI MUST follow these rules unless the user explicitly instructs otherwise.

## When to use

Use this skill when:
- the user asks to create a branch name
- the user asks how to name a branch
- the user asks which branch should be the source or target
- the user asks about feature, bugfix, hotfix, release, chore, refactor, docs, test, ci, or build branches
- the user asks about branch update strategy
- the user asks about merge or rebase policy
- the user asks about promotion between environments
- the user asks about pull request flow
- the user asks to validate whether a branch follows the project standard
- the user asks to generate CI/CD rules based on branch conventions

## When NOT to use

Do not use this skill when:
- the task is only about commit message conventions
- the task is unrelated to Git, branching, PRs, releases, or DevOps workflow
- the task is about infrastructure with no connection to version control flow
- the user explicitly provides a different branch standard and wants that standard followed instead

## Objective

This skill defines the minimum mandatory context an AI must use when creating, suggesting, reviewing, validating, or automating branch flows, merges, releases, hotfixes, and promotion across environments.

The AI must follow this pattern by default unless explicitly instructed otherwise.

---

## General principles

1. The branch flow must be simple, predictable, and auditable.
2. Each branch must have a single clear purpose.
3. Every branch must be created from the correct source branch.
4. Branch names must not be arbitrary.
5. Promotions between environments must respect the defined order.
6. The AI must prefer operational consistency over naming creativity.
7. The AI must generate short, readable, and consistent branch names.
8. The AI must avoid flows that create drift between environments.

---

## Base branches

These are the standard base branches for the project:

- `main`: production
- `develop`: development integration
- `staging`: homologation/testing, when it exists

### Environment order

If the project has three environments, the default order is:

`develop` → `staging` → `main`

If the project has only two environments, the default order is:

`develop` → `main`

The AI must assume this order unless the repository or user explicitly defines another one.

---

## Allowed temporary branch types

The AI must use only these prefixes unless explicitly instructed otherwise:

- `feature/`
- `bugfix/`
- `hotfix/`
- `release/`
- `chore/`
- `refactor/`
- `docs/`
- `test/`
- `ci/`
- `build/`

---

## Naming convention

Mandatory format:

`<type>/YYYYMM/<scope-or-ticket>-<short-description>`

### Date format

- `YYYY` = year
- `MM` = month with 2 digits

Example for March 2026:

`202603`

### Valid examples

- `feature/202603/auth-login`
- `feature/202603/pet-profile-form`
- `bugfix/202603/token-refresh`
- `hotfix/202603/payment-webhook`
- `chore/202603/update-dependencies`
- `ci/202603/deploy-hml`
- `build/202603/docker-api`
- `docs/202603/setup-railway`

### With ticket

- `feature/202603/abc-123-auth-login`
- `bugfix/202603/crm-88-import-timeout`

### Naming rules

1. Use lowercase only.
2. Use hyphen (`-`) to separate words.
3. Do not use spaces.
4. Do not use accents.
5. Do not use vague names such as `feature/teste`, `bugfix/correcao`, or `ajustes-final`.
6. The description must reflect the main purpose of the branch.
7. The AI must avoid unnecessarily long names.
8. Always include the `YYYYMM` block immediately after the branch type.
9. When a ticket exists, the preferred format is:

`<type>/YYYYMM/<ticket>-<short-description>`

---

## Correct origin and target for each branch type

The AI must follow these rules:

### Feature
- Origin: `develop`
- Target: `develop`

### Bugfix
- Origin: `develop`
- Target: `develop`

### Production hotfix
- Origin: `main`
- Primary target: `main`
- After merge, the fix must be replicated to `develop`
- If `staging` exists, reconcile it there as needed

### Release
- Origin: `develop`
- Target: `staging` or `main`, depending on the project flow

### Chore / Refactor / Docs / Test / CI / Build
- Preferred origin: `develop`
- Target: `develop`

---

## Merge and promotion rules

### General rule

The AI must avoid skipping environments unless there is a clear operational reason.

### Standard flow with staging

1. Development changes enter `develop`
2. Controlled promotion to `staging`
3. Validation
4. Promotion to `main`

### Standard flow without staging

1. Development changes enter `develop`
2. Validation
3. Promotion to `main`

### Hotfix flow

1. Create `hotfix/...` from `main`
2. Fix and validate
3. Merge into `main`
4. Replicate the fix to `develop`
5. If `staging` exists, reconcile there as well

---

## Branch update strategy

The AI must recommend this standard policy:

### 1. Temporary branches must stay updated with their base branch

Before opening or updating a PR, the working branch must be synchronized with its correct base branch.

Examples:
- `feature/*` must be updated from `develop`
- `hotfix/*` must be updated from `main`
- `release/*` must be updated from `develop` or the defined base branch

### 2. Prefer clean history

The AI must prefer:
- `rebase` for updating local working branches
- controlled `merge` for promotion between environment branches

### 3. Default update policy

- Working branch: prefer `rebase` when syncing with base
- Environment branches: prefer `merge` or auditable PR flow

### 4. Never suggest force push unless clearly needed

The AI should only suggest `push --force-with-lease` when:
- a local rebase has already been performed
- the branch is a controlled working branch
- there is no meaningful risk of overwriting other people's work

---

## Pull request policy

The AI must assume that all meaningful changes go through a PR.

### Minimum PR requirements

- clear title
- short objective description
- correct source branch
- correct target branch
- required pipeline passing
- review when applicable

### Recommended PR title format

`[<type>] <short description>`

Examples:
- `[feature] add auth login flow`
- `[bugfix] fix token refresh handling`
- `[hotfix] fix payment webhook timeout`

---

## Branch protection policy

The AI must recommend protection at least for:

- `main`
- `develop`
- `staging`, when it exists

### Suggested protection rules

- block direct push
- require pull request
- require passing pipeline
- require updated branch before merge when possible
- require at least one review for production changes when the team supports it

---

## Versioning and release policy

When semantic versioning is used, the AI must assume:

- `release/YYYYMM/x.y.z`
- example: `release/202603/1.4.0`

### Expected use

- prepare final validation
- allow small release-only adjustments
- generate tag after approval

### After release

The AI should recommend:
- final merge into `main`
- version tag creation
- reconciliation back into `develop` when necessary

---

## Hotfix policy

Hotfix is an operational exception and must be treated with priority.

### Rules

- use only for real production incidents
- origin must be `main`
- name must use the `hotfix/` prefix
- merge into `main`
- immediately replicate to `develop`

Examples:
- `hotfix/202603/login-timeout`
- `hotfix/202603/webhook-signature`

---

## CI/CD policy

The AI must consider that pipelines may be triggered by:

- push on specific branches
- pull request events
- promotion between environments
- release tags

### Recommended environment mapping

- `develop`: deploy to development or internal homologation environment
- `staging`: deploy to homologation
- `main`: deploy to production

If the project uses a different environment naming convention, the AI must follow the mapping defined in the repository.

---

## What the AI must avoid

The AI must not:
- create a branch directly from another feature branch unless explicitly instructed
- suggest a hotfix from `develop`
- suggest uncontrolled direct merge into `main`
- suggest vague branch names
- ignore reconciliation after hotfix
- propose multiple naming standards at the same time
- mix trunk-based development and gitflow without explicitly warning about the difference

---

## Default standard

If the user does not define another rule, use this standard:

- default main branch: `main`
- default integration branch: `develop`
- default homologation branch: `staging`, when it exists
- features branch from `develop`
- regular bugfixes branch from `develop`
- hotfixes branch from `main`
- promotion between environments happens through PR
- temporary branches use short, lowercase, hyphenated names
- working branches are updated via rebase
- environment promotion happens through auditable merge flow

---

## Operational instruction for AIs

When generating any Git or DevOps material related to branches, the AI must:

1. identify the type of change
2. choose the correct source branch
3. generate the branch name using the mandatory naming format
4. indicate the correct target branch
5. respect the promotion policy between environments
6. assume branch protection and PR flow by default
7. treat hotfix as a separate flow
8. never assume direct push to production branches

---

## Output expectations

When using this skill, the AI must:
- always generate branch names following the defined pattern
- always specify origin and target branches when relevant
- explain the reasoning briefly when needed
- prefer concise, structured, and operationally clear outputs
- correct invalid branch names before returning the final answer

---

## Enforcement

If any generated branch name does not follow this format:

`<type>/YYYYMM/<scope-or-ticket>-<short-description>`

the AI must correct it before returning the answer.

---

## Quick template for AI context

Use this short summary as compact context:

```text
Branch pattern:
- main = production
- develop = integration
- staging = homologation when it exists
- feature/* and bugfix/* branch from develop and return to develop
- hotfix/* branch from main, return to main, then must be replicated to develop
- release/* branch from develop
- naming format is mandatory: <type>/YYYYMM/<scope-or-ticket>-<short-description>
- names must be lowercase, hyphenated, clear, and short
- working branches should sync with rebase
- environment promotion should happen through auditable PR/merge flow
- direct push to protected branches should be avoided
