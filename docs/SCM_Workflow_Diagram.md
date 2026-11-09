# ForgeLine Engineering: SCM Workflow & State Transitions
*Project: DevOps Metrics Dashboard | Authority: Mateo Rossi, Engineering Manager*

## Why This Matters
ForgeLine builds DevOps tooling for enterprise customers, so our internal engineering practices must meet the same standard we promise them. Without formal Software Configuration Management (SCM) rules, developers apply baseline hygiene inconsistently. The result is broken builds, slow review cycles and regressions that cannot be traced. Understanding how a Configuration Item (CI) moves from a working copy to the upstream repository keeps the DevOps Metrics Dashboard maintainable and keeps the team productive.

## Core Idea
The principle of our SCM strategy is **Strict State Transitioning and Isolation**. A change must never reach the upstream baseline (`main`) without passing through defined checkpoints. Feature branch isolation keeps new work separate from `main` until the work is complete, verified locally with linters and debuggers, and approved through peer review on a GitHub Pull Request.

## Key Terms
* **Version Control:** A system that records changes to files over time so that any earlier version can be recovered and every change can be attributed.
* **Repository (Repo):** The database that stores a project's files and the full history of recorded snapshots (commits). The *Local Repo* is on the developer's machine. The *Upstream Repo* is the shared source of truth on GitHub.
* **Software Configuration Management (SCM):** The administrative and technical process of tracking and controlling changes to software.
* **Configuration Item (CI):** A logically related set of files or code changes that forms one trackable modification to the system.
* **Working Copy (Working Directory):** The local folder where files are edited. Active development and debugging happen here.
* **Staging Area (Index):** A holding zone before a commit. It lets a developer choose exactly which changes go into the next snapshot and leave unfinished work out.
* **Commit:** A permanent snapshot of the staged changes, stored in the local repository with a unique hash.
* **Upstream:** The shared repository on GitHub that local work is pushed to and updated from.
* **Baseline (`main`):** The stable branch that represents approved, working code.
* **Feature Branch:** A temporary, parallel line of development created from `main` so unstable work stays isolated.
* **Pull Request (PR):** A GitHub request to merge a feature branch into `main`. It enforces peer review.

## Application

### Part 1: Feature Branch Isolation Strategy
All work must be done on an isolated feature branch.

```text
[Upstream: main]  (Commit A) -------------------------> (Commit D: Merge PR)
                         \                                   /
                          \                                 /
[Local: feature/refresh-interval]   +--> (Commit B) ---> (Commit C)-+
                                 ^               ^
                            (CI Staged)     (CI Staged)

Annotations:
  Commit A  = latest stable baseline on main; the branch point.
  Commit B/C = commits made only on the feature branch; main is untouched.
  Commit D  = merge into main, allowed only through a reviewed Pull Request.
```

**Strategy Outline:**
1. Branch from the latest, stable `main`.
2. Develop iteratively on the isolated `feature/*` branch.
3. Verify changes locally with linters and debuggers. Never push a broken state.
4. Reintegrate into `main` only through a GitHub Pull Request, which enforces peer review.

**Keeping the feature branch current with upstream `main`**

While you work, others may merge into `main`. Before opening a Pull Request, fetch the upstream changes and merge `main` into your feature branch.

```text
[Upstream: main]  (A) ----------> (E: Dana's change) ---------------> (Merge PR)
                    \                    \                              /
                     \              git fetch + git merge origin/main  /
                      \                    \                          /
[Local: feature/refresh-interval]  +--> (B) ---> (M: merge commit) ---+

Annotations:
  E = a commit added to main by another developer while you worked.
  M = merge commit on the feature branch. If E and B changed the same lines,
      Git reports a conflict; resolve it first (see Command_Sequences.txt,
      Sequence 4).
```

### Part 2: Configuration Item (CI) State Transitions
During standard development, a CI moves in one direction through four states.

```text
+---------------------+      +---------------------+      +---------------------+      +---------------------+
|    WORKING COPY     |      |    STAGING AREA     |      |   LOCAL REPOSITORY  |      | UPSTREAM REPOSITORY |
|  (Unmodified Files) |      |       (Index)       |      |     (.git / HEAD)   |      |      (GitHub)       |
+---------------------+      +---------------------+      +---------------------+      +---------------------+
           |                            |                            |                            |
[Edit / Modify Files]                   |                            |                            |
           |                            |                            |                            |
           v                            |                            |                            |
+---------------------+                 |                            |                            |
|    WORKING COPY     |                 |                            |                            |
|  (Modified Files)   |======(git add)=====>                         |                            |
+---------------------+                 |                            |                            |
                                        v                            |                            |
                             +---------------------+                 |                            |
                             |    STAGING AREA     |                 |                            |
                             |  (Tracked Changes)  |====(git commit)===>                          |
                             +---------------------+                 |                            |
                                                                     v                            |
                                                          +---------------------+                 |
                                                          |   LOCAL REPOSITORY  |                 |
                                                          |  (Committed State)  |===(git push)====>
                                                          +---------------------+                 |
                                                                                                  v
                                                                                       +---------------------+
                                                                                       | UPSTREAM REPOSITORY |
                                                                                       |   (Maintained CI)   |
                                                                                       +---------------------+
```

**Transition Definitions:**
1. **Working Copy to Staging Area (`git add`):** The developer selects which modifications belong to one cohesive CI. Debug statements and temporary files are left out.
2. **Staging Area to Local Repository (`git commit`):** The staged CI is recorded permanently in the local history with a hash for traceability.
3. **Local Repository to Upstream Repository (`git push`):** The local history is sent to ForgeLine's GitHub repository, where a Pull Request enables peer review before the change reaches `main`.
