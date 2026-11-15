# GitHub Public Repository Setup — Group Leader Checklist

**Repository name:** `distributed-dbms-optimization`  
**Visibility:** Public  
**Target:** Six students collaborating on a locally hosted PostgreSQL/DBMS project. GitHub is for source code and documents, **not the deployed database**.

## Before inviting your group

1. Open <https://github.com/new> and create a **Public** repository in your personal account named `distributed-dbms-optimization`. Fill in the description: `A distributed DBMS project demonstrating PostgreSQL replication, load balancing, indexing, and performance optimization across six local laptops.`
2. Leave GitHub's automatic README, `.gitignore` and license selections **off**, because this package includes your own files.
3. Extract this ZIP locally. On the empty GitHub repository, click **uploading an existing file** or **Add file → Upload files** and select these **five extracted files**. Do not upload the ZIP itself:
   - `README.md`
   - `AGENTS.md`
   - `DBMS_PROJECT_SETUP_AND_ASSIGNMENTS.md`
   - `GITHUB_PUBLIC_SETUP.md`
   - `.gitignore` (a hidden file; ensure it was included)
4. Review the upload list carefully. Commit directly to `main` **for the initial upload only**, with the message `docs: initialize public DBMS project`.
5. On your repository home page, verify all five files appear in the root. Inspect README and AGENTS.md by clicking them.
6. Go to **Settings → General**. If needed, enable **Issues** under Features. Under **Pull Requests**, consider enabling squash merge and automatic deletion of head branches (available settings may vary).
7. **Protect main before inviting anyone**: open **Settings → Rules → Rulesets → New ruleset → New branch ruleset**. Name it `protect-main`; set enforcement to **Active**; target the default branch (`main`). Select **Require a pull request before merging** with **one approval** (if the group wants enforced peer review); select **Block force pushes** and **Restrict deletions**. Save. These rulesets are available for public repositories on GitHub Free. Ruleset UI labels may vary.
8. Make sure no sensitive data, personal IDs, real passwords, API keys, `.env` files, downloaded Instacart CSVs, or database dumps are in the repository. `.gitignore` helps with Git CLI but does **not** sanitize files deliberately uploaded in the GitHub browser.

## Invite the five other members

1. Open the repo **Settings → Collaborators → Add people** (in the Access section). Invite by **GitHub username**, not just a display name.
2. Invite Menard, Michell, Rei, Diane and Ralph if **JC is the account owner**. If a different member owns the account, invite whichever five members are not the owner. Have each member accept the invitation.
3. Everyone can see a public repository, but the invitation is needed for write/collaboration access.
4. Create six GitHub Issues assigning one owner per task:
   - **Lead / JC:** Architecture decisions, storefront/template selection, shared ERD/API review, pull-request review, monitoring plan.
   - **Menard — Windows 1 (Menard):** FastAPI `/health` prototype, API configuration, application deployment plan.
   - **Michell — Windows 3 (Michell):** ERD and SQL migration proposal, PostgreSQL 18 local setup (do not change shared schema without approval).
   - **Rei — Windows 4 (Rei):** PostgreSQL 18 backup/restore small-database exercise, future replica plan.
   - **Diane — MacBook M2 (Diane):** PostgreSQL macOS setup, HAProxy plan, network topology checklist.
   - **Ralph — Chromebook (Ralph):** Instacart data dictionary review, sample importer proposal, Locust basic test.

## What to send in the group chat

Repository: `https://github.com/USERNAME/distributed-dbms-optimization`

1. Create/sign in to GitHub and accept your collaborator invitation.
2. Install Git (if missing): <https://git-scm.com/downloads>.
3. Clone the repository onto your own laptop:

```bash
git clone https://github.com/USERNAME/distributed-dbms-optimization.git
cd distributed-dbms-optimization
git status
```

4. Read `README.md`, `AGENTS.md`, and `DBMS_PROJECT_SETUP_AND_ASSIGNMENTS.md` **before giving your agent a task**.
5. Check the software already on your laptop. Do **not** reinstall Python solely for one patch version. Follow the role-specific tool checklist in the group guide.
6. Find your assigned Issue. Do not begin large features or alter shared ERD/API contracts until the group has approved them.
7. For each new task, make a feature branch:

```bash
git switch main
git pull origin main
git switch -c feat/yourname-task
```

8. After your agent produces a small, reviewed and tested change, inspect the diff; stage **specific** files rather than `git add .`:

```bash
git status
git add path/to/your/file
git diff --cached
git commit -m "feat: describe your task"
git push -u origin feat/yourname-task
```

9. On GitHub, open a **Pull request** from the task branch to `main` and request group-lead review. At least one authorized person should approve if branch protection requires it. Merge only after tests and explanations are checked.

## AI session starter (everyone)

> Read `AGENTS.md`, `README.md`, and the shared setup guide. I am **[NAME]**, using **[LAPTOP WITH OWNER]**. Our current gate is **Gate 0**. I am assigned GitHub Issue **[NUMBER AND TITLE]**. Inspect the repository and my software versions first. Tell me what you'll do and what files you plan to change. Do not change shared schema, API contracts, or deployment without review. Work in `feat/[name]-[task]`, give exact OS-specific commands and tests, explain the DBMS concepts, and do not upload credentials or the full dataset.

## After this setup: first technical milestone

Review one ERD/schema, Instacart CSV-to-table mapping, API contract, and migration design **together**. Only after approval should different agents build dependent backend, importer and benchmarking modules. Router testing and replication are **not prerequisites** to starting this milestone.

## If you already have a private repository

In the repository: **Settings → General → Danger Zone → Change repository visibility → Make public**. Review source files and commit history for secrets **before** switching; public visibility publishes the repository's history.

## Official GitHub documentation

- Creating a repo: <https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-new-repository>
- Uploading files: <https://docs.github.com/en/repositories/working-with-files/managing-files/adding-a-file-to-a-repository>
- Inviting collaborators: <https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/repository-access-and-collaboration/inviting-collaborators-to-a-personal-repository>
- Rulesets: <https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets>
- Visibility changes: <https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/managing-repository-settings/setting-repository-visibility>
