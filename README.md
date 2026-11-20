# Distributed Coffee Shop DBMS Project

University Database Management Systems project: a **locally hosted** coffee shop and bakehouse application based on PostgreSQL, using reproducible, clearly labeled synthetic customer and transaction data. We will measure real bottlenecks, optimize SQL, and later demonstrate local-network logical replication and read load balancing.

**Repository visibility: Public.** Anyone can read the source; only authorized collaborators should edit it. Never upload secrets, private records, large generated data archives, or large database dumps.

**Documented stage:** Repository/tool preparation and storefront ideation. Actual member setup progress has not been verified. No PostgreSQL service, bulk data load, distributed node, or benchmark result is claimed operational.

## Current direction update — 20 November 2026

The coffee-shop domain supersedes previous grocery requirements. Branding is not approved yet. Ralph is responsible for the storefront and will hand it off to Menard. Read [the website design brief](WEBSITE_DESIGN_BRIEF.md) for the mandatory visual approval process. The database team can prepare domain requirements in parallel.

## Start here

1. Read [`AGENTS.md`](AGENTS.md) — common constraints for **all** coding agents.
2. Read [`DBMS_PROJECT_SETUP_AND_ASSIGNMENTS.md`](DBMS_PROJECT_SETUP_AND_ASSIGNMENTS.md) — member roles, software links, commands, milestone gates, and agent prompts.
3. Check which software is already installed. Do **not** reinstall Python solely to match a specific patch version. Prefer compatible Python 3.11+ and use per-project virtual environments.
4. Work in a **feature branch**; never ask an agent to push directly to `main`.
5. Submit a **pull request** with evidence of actual tests and ask the project lead to review. Do not merge without review.

## Six laptops and deployment responsibilities

| Laptop | Owner | Target role |
| --- | --- | --- |
| Windows 1 (Menard) | Menard | FastAPI integration and storefront hosting |
| Windows 2 (JC) | JC | Query performance, optimization and monitoring dashboard |
| Windows 3 (Michell) | Michell | Primary PostgreSQL instance; deploy reviewed migrations |
| Windows 4 (Rei) | Rei | Replica B; backup and recovery |
| MacBook M2 (Diane) | Diane | Replica A; HAProxy and local networking |
| Chromebook (Ralph) | Ralph | Storefront development, synthetic data generator and Locust load tests |

Ralph owns storefront design and development; the group project lead coordinates shared ERD/API and architecture approvals. Confirm formal leadership/repository ownership with the team. Michell runs the primary server but **does not unilaterally define** the database schema.

## Planned project directories (created as work begins)

- `frontend/` — customer storefront, owned by Ralph and deployed by Menard
- `backend/` — FastAPI, owned by Menard
- `monitoring/` — dashboard and measurement pipeline, owned by JC
- `schema/` — reviewed SQLAlchemy models / Alembic migrations, deployed by Michell
- `scripts/data/` — synthetic data generator and bulk loader, owned by Ralph
- `benchmarks/sql/` — SQL measurement scripts, JC
- `benchmarks/locust/` — HTTP load testing, Ralph
- `infra/replication/` — replication configuration, Rei + Diane
- `infra/haproxy/` — HAProxy configuration, Diane
- `infra/backup/` — backup/restore instructions, Rei
- `site-docs/` — ERD, API contract, mapping, test evidence and explanations

## Project gates

- **Gate 0:** Share repository, verify software versions, agree workflow.
- **Gate 1:** Draft and review ERD, synthetic-data mapping, API contract, migrations; approve **before** writing dependent features.
- **Gate 2:** One local PostgreSQL database, working FastAPI and sample storefront.
- **Gate 3:** Bulk synthetic data, repeatable baseline and optimized SQL measurements.
- **Gate 4:** Offline LAN, primary + logical replicas, HAProxy, distributed read tests.
- **Gate 5:** Rehearsal, transaction/concurrency and recovery demonstrations, evidence and Q&A.

## Data and secret handling

Do not commit bulk generated data files, PostgreSQL database directories, dumps, bulk outputs, `.env` files, database passwords, or API tokens. Commit only small non-sensitive fixtures, scripts, migration files, and an `.env.example` with placeholder values. **GitHub hosts source code, not the deployed database.**

## Everyday Git routine

```bash
# After cloning, before starting a NEW task:
git switch main
git pull origin main
git switch -c feat/jc-monitoring-starter  # replace with your task branch

# After a deliberate, reviewed change:
git status
git add path/to/your/file
git commit -m "feat: add monitoring starter"
git push -u origin feat/jc-monitoring-starter
```

Open a pull request on GitHub targeting `main`. Do not blindly use `git add .` when there may be local secrets/data files. Ask your agent to identify files before staging.

The complete project guide and `AGENTS.md` govern all task details. If a task appears to conflict with them, ask for a shared review first.
