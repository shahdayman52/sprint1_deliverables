# ForgeLine DevOps Metrics Dashboard

This repository contains the source code for the ForgeLine DevOps Metrics Dashboard, an internal tool that helps engineering managers track team velocity and deployment metrics. This file is the primary contribution guideline for the project.

## Why This Matters
ForgeLine provides DevOps tooling to enterprise customers, so our internal practices must meet the same standard. Consistent Software Configuration Management (SCM) keeps every change traceable, keeps the `main` branch stable and avoids broken builds and slow reviews.

## Core Idea
Work is isolated on a feature branch, verified locally, and merged into `main` only through a peer-reviewed GitHub Pull Request. Every change is tracked by a GitHub issue.

## Key Terms
* **Version Control:** A system that records changes to files over time.
* **Repository (Repo):** The database of the project's files and history. The local repo is on your machine. The upstream repo is on GitHub.
* **Configuration Item (CI):** A related set of file changes forming one trackable modification.
* **Working Copy:** The local files you edit.
* **Staging Area:** The holding zone that selects which changes go into the next commit.
* **Upstream:** The shared repository on GitHub (`origin`).
* **Feature Branch:** A temporary branch created from `main` to isolate work.
* **Pull Request (PR):** A request to merge a branch into `main`, subject to peer review.
* **Breakpoint:** A marker that pauses program execution at a chosen line in the IDE.

## Getting Started
1. Clone the repository to your local machine.
2. Run `npm install` to download dependencies.
3. Start the local development server with `npm run start:dev`.

## SCM Rules
1. Never commit directly to `main`. Branch from the latest `main` using the name format `feature/<short-description>`.
2. A Configuration Item moves from working copy, to staging area (`git add`), to local repository (`git commit`), to upstream (`git push`).
3. Review your changes with `git status`, `git diff` and `git diff --staged` before committing or pushing, so no unintended changes are included.
4. Keep your feature branch up to date with `main` by fetching and merging (`git fetch origin`, `git merge origin/main`). Resolve conflicts as shown in `docs/Command_Sequences.txt`.
5. Use basic Git operations only. Do not use rebase, bisect, cherry-pick or filter-branch.

Details: `docs/SCM_Workflow_Diagram.md` and `docs/Command_Sequences.txt`.

## GitHub Issue Workflow
Every piece of work is tracked by an issue.
1. Open the repository on GitHub and select the **Issues** tab.
2. Click **New issue**.
3. Enter a short, specific **title** and a **description** stating the problem or task and what "done" means.
4. Under **Labels**, select the labels that apply (for example `bug`, `enhancement`, `documentation`).
5. Under **Assignees**, select the engineer responsible for the work.
6. Click **Submit new issue**.
7. Create your feature branch for the issue and reference the issue number (for example `#12`) in your commits and Pull Request.

## Pre-Submission Checks
Before opening a Pull Request:
1. Run the linter locally and fix every warning (`flake8 .` for Python, `npm run lint:check` for JavaScript).
2. Re-run the linter until it reports no problems.
3. Use IDE breakpoints to debug, not `print` statements or `console.log()` calls. Do not commit them.

Details: `docs/Debugging_and_Linting_Summary.md`.

## Pull Request Workflow
A Pull Request protects the upstream `main` branch: changes cannot reach it until reviewers have examined and approved them.
1. Push your feature branch: `git push -u origin feature/<short-description>`.
2. On GitHub, click **Compare & pull request** for your branch (or **Pull requests** then **New pull request**).
3. Set the base branch to `main` and the compare branch to your feature branch.
4. Write a title and a description of what changed and why. Link the issue (for example `Closes #12`).
5. Assign at least two reviewers from the core engineering team and apply labels.
6. Click **Create pull request**.
7. Peer reviewers comment and either request changes or approve. Address requested changes by committing to the same branch and pushing again.
8. Merge into `main` only after at least two approvals from the core engineering team have been given and all automated checks pass. A PR with fewer than two approvals must not be merged.
