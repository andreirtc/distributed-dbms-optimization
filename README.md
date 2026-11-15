# Distributed Grocery DBMS Project

University Database Management Systems project: a **locally hosted** grocery e-commerce application based on PostgreSQL, using the Instacart Market Basket Analysis dataset and documented synthetic extensions. We will measure real bottlenecks, optimize SQL, and later demonstrate local-network logical replication and read load balancing.

**Repository visibility: Public.** Anyone can read the source; only authorized collaborators should edit it. Never upload secrets, private records, Kaggle CSV archives, or large database dumps.

**Current stage: Gate 0 — repository and tools only.** No PostgreSQL service, dataset import, distributed node, or benchmark result is claimed operational.

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
| Windows 2 (JC) | JC | Query performance, monitoring dashboard; project leadership and storefront development |
| Windows 3 (Michell) | Michell | Primary PostgreSQL instance; deploy reviewed migrations |
| Windows 4 (Rei) | Rei | Replica B; backup and recovery |
| MacBook M2 (Diane) | Diane | Replica A; HAProxy and local networking |
| Chromebook (Ralph) | Ralph | Data importer/generator scripts and Locust load tests |

The project lead owns UI selection, architecture approvals, and the shared ERD/API contract. Michell runs the primary server but **does not unilaterally define** the database schema.

## Planned project directories (created as work begins)

- `frontend/` — customer storefront, owned by project lead and deployed by Menard
- `backend/` — FastAPI, owned by Menard
- `monitoring/` — dashboard and measurement pipeline, owned by JC
- `schema/` — reviewed SQLAlchemy models / Alembic migrations, deployed by Michell
- `scripts/data/` — CSV importer and synthetic generator, owned by Ralph
- `benchmarks/sql/` — SQL measurement scripts, JC
- `benchmarks/locust/` — HTTP load testing, Ralph
- `infra/replication/` — replication configuration, Rei + Diane
- `infra/haproxy/` — HAProxy configuration, Diane
- `infra/backup/` — backup/restore instructions, Rei
- `site-docs/` — ERD, API contract, mapping, test evidence and explanations

## Project gates

- **Gate 0:** Share repository, verify software versions, agree workflow.
- **Gate 1:** Draft and review ERD, CSV mapping, API contract, migrations; approve **before** writing dependent features.
- **Gate 2:** One local PostgreSQL database, working FastAPI and sample storefront.
- **Gate 3:** Real bulk data, repeatable baseline and optimized SQL measurements.
- **Gate 4:** Offline LAN, primary + logical replicas, HAProxy, distributed read tests.
- **Gate 5:** Rehearsal, transaction/concurrency and recovery demonstrations, evidence and Q&A.

## Data and secret handling

Do not commit downloaded Kaggle CSV files, PostgreSQL database directories, dumps, bulk outputs, `.env` files, database passwords, or API tokens. Commit only small non-sensitive fixtures, scripts, migration files, and an `.env.example` with placeholder values. **GitHub hosts source code, not the deployed database.**

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
