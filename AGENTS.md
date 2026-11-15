# Shared AI-Agent Instructions — Distributed Grocery DBMS Project

> Place this file in the **root of the shared Git repository**. All members should paste the **Agent Session Starter** below into their chosen AI coding agent and ask it to read this file first. These rules apply to Codex, Claude Code, Gemini/Antigravity, Hermes, and agents launched through Orca.

## Project mission
Build a **locally hosted distributed grocery e-commerce database application** for a six-member DBMS class project. The professor prohibits **cloud-hosted databases**, but GitHub and online AI coding tools are allowed unless the professor later says otherwise. The objective is to demonstrate **real**, repeatable DBMS performance bottlenecks and measurable optimizations—not simulated loading delays.

### Agreed stack (initial choices)
- PostgreSQL **18.x stable** on all final database nodes.
- Python **3.11 or newer** for development if dependencies support it; prefer 3.12/3.13 when already installed. Do **not** require an exact patch version on every laptop. Create isolated virtual environments and lock project dependencies after successful integration.
- FastAPI + SQLAlchemy 2.x + Alembic for the API, ORM, and migrations.
- React frontend; **the project lead selects and develops/adapts the main customer storefront template**.
- Separate DBMS performance dashboard; no fabricated metrics.
- Instacart Market Basket Analysis CSV files + properly labeled synthetic extensions.
- Locust for HTTP load tests; PostgreSQL `EXPLAIN (ANALYZE, BUFFERS)`, `pg_stat_statements`, and other genuine instrumentation for SQL performance.
- PostgreSQL **logical replication** and HAProxy in the integration phase, not in Phase 1.
- Deployment: six laptops connected by local Wi-Fi through the PLDT FiberHome HG6245D if verified; Tenda F6 fallback. Internet must not be needed to serve the website/database during demo.

### Device ownership and target deployment
| Laptop | Owner | Target role |
|---|---|---|
| Windows 1 (Menard) | Menard | FastAPI backend, storefront integration, application hosting |
| Windows 2 (JC) | JC | Performance measurement, optimization and monitoring dashboard |
| Windows 3 (Michell) | Michell | Primary PostgreSQL node and migrations deployment |
| Windows 4 (Rei) | Rei | PostgreSQL Replica B, backup and restore |
| MacBook M2 (Diane) | Diane | PostgreSQL Replica A, HAProxy read/write endpoints |
| Chromebook (Ralph) | Ralph | Dataset import tooling and Locust load testing |

**The project lead (the person directing this project) also owns the storefront design and final architecture/schema approval; this is an additional coordinating duty within the existing six-person group, not a seventh member.** The project lead creates the frontend in the shared repo, then **hands off deployment/integration to Menard**. Michell administrates the primary database but is **not the only person allowed to design or approve the schema**. All six review the ERD/API contract before it is locked.

## Rules the agent MUST follow
1. **Read the repository first**: README, this file, `docs/architecture.md`, `docs/erd.md`, `docs/api-contract.md`, and existing migrations. If a document does not exist, report it; do not pretend it does.
2. **Do not invent the schema or change it unilaterally**. Shared table/column names, PK/FK definitions, routes, request/response shapes, and migrations require a reviewed proposal and project-lead approval.
3. **Keep task boundaries**. Work only in the paths owned by the current issue. Ask before editing another owner's modules or shared contracts. Do not rewrite the whole app for a small fix.
4. **Do not push to `main` directly**. Work in a named branch and submit a pull request with a summary and verification evidence. Do not merge your own changes without review.
5. **Never commit secrets or large data**: no `.env`, passwords, tokens, private keys, Kaggle archives/raw CSV, PostgreSQL data directories, or large generated files. Commit `.env.example` and scripts instead.
6. **Keep the database local**. Do not introduce Supabase, Firebase, Neon, PlanetScale, cloud-hosted PostgreSQL, or hidden hosted persistence. GitHub for *code* is permitted. Any external AI development services must not be used as the deployed database.
7. **Do not fake benchmark results**. Do not use intentional `sleep()` delays or mock metrics as proof. If tests cannot run locally, state that clearly and provide reproducible manual commands.
8. **Use data correctly**. The Instacart CSV files are historical/anonymized data; do not assume they have prices, passwords, inventory, or real calendar timestamps. Generate additional application data separately, visibly labeled synthetic. Respect dataset competition rules/attribution.
9. **Build safely**: parameterized SQL, least-privilege accounts, hashed demo passwords, transactions for checkout, environment variables for credentials, private-LAN access controls, and no destructive database operations against shared nodes without explicit approval/backups.
10. **Replication limitations**: logical replication does *not* replicate DDL/schema changes or sequence state automatically. Plan matching schema migrations on subscribers, account for lag, and make subscription tables read-only to the application. HAProxy balances TCP **connections**, not SQL statements; API chooses a read or write endpoint.
11. **Conserve resources**: Chromebook (Ralph) has limited Linux storage; Windows 2 (JC) has limited free SSD storage. Never put the full Instacart dataset or a full PostgreSQL copy on these devices by default.
12. **Explain like a mentor**. For each task, describe what was done, why it matters in DBMS terms, exact OS-specific install/run commands, the tests performed, expected outputs, and 3–5 likely professor Q&A items.
13. **Review command permissions**. Never use `--dangerously-skip-permissions`, `--yolo`, unrestricted remote shell access, or similar bypass flags by default. Ask the human before running downloads, installers, destructive scripts, changing router/firewall settings, or touching another person's laptop/server.

## Shared architecture (target, not the Day 1 requirement)
```
Browser / Locust on laptops
        |
     local Wi-Fi (PLDT HG6245D; Tenda F6 fallback)
        |
Windows 1 (Menard): Storefront + FastAPI
        |
        +--------------------------+
        |                          |
   READ DB endpoint           WRITE DB endpoint
        |                          |
MacBook M2 (Diane): HAProxy (TCP connections)
        |                          |
        +-> Replica A         Windows 3 (Michell)
        |   MacBook M2             Primary PostgreSQL
        +-> Replica B                 |
            Windows 4 (Rei)   logical publications
                                      |       |
                                 Replica A   Replica B

Windows 2 (JC): dashboard / metrics (read-only access)
Chromebook (Ralph): Locust / dataset scripts; bulk imports run on roomy laptop
```
- The API decides which operations are safe for read replicas. Critical reads and writes go to the primary.
- HAProxy's read endpoint routes **new connections** to replicas; connection pooling may make distribution uneven. Design tests accordingly.
- Proposed example LAN addresses/ports are illustrative until DHCP/router testing succeeds; never hardcode a particular subnet.
- Primary+replicas are **three database nodes**; the other laptops still perform key project roles.

## Milestone gates (do not skip)
**Gate 0 — Tool inventory:** record actual versions (`python --version`, `git --version`, `node -v`, etc.). Only install missing/incompatible tools.

**Gate 1 — One schema and contracts:** approved ERD, field definitions, source-to-table mapping, migration plan, API contract, folder structure, and small sample seed. Each member acknowledges these.

**Gate 2 — Single PostgreSQL + functioning app:** FastAPI accesses local PostgreSQL, frontend can browse/search real sample products, basic checkout uses transactions, automated tests pass.

**Gate 3 — Large data + genuine benchmarks:** progressive bulk import with integrity checks; real SQL baseline, index/query optimization, exported execution plans and repeated timing measurements. Early professor review possible here.

**Gate 4 — Distributed system:** test LAN; configure primary/replicas; sync schema and data; configure HAProxy read/write endpoints; demonstrate replication and read-connection distribution with real metrics.

**Gate 5 — Defense-ready:** concurrency, backup/restore, controlled repeated comparisons, all six members able to explain and restart their components, offline demo rehearsal.

## Shared folders (proposed)
```
backend/       # Menard: API, auth, cart, checkout; project lead reviews contracts
frontend/      # Project lead: customer storefront, handoff to Menard
monitoring/    # JC: dashboard UI + metrics collection
schema/        # Michell implements reviewed models, migrations, ERD exports
scripts/data/  # Ralph: importer, synthetic generator (NOT full CSV)
benchmarks/    # Ralph + JC: Locust, SQL workloads, measurement scripts
infra/         # Diane + Rei: HAProxy, replication, recovery guidance
infra/backups/ # Scripts/config only — backup files excluded from Git
site-docs/     # architecture, ERD, API contract, setup, test results, Q&A
AGENTS.md      # THIS FILE
README.md      # first-run steps for each OS
.env.example   # placeholder configuration only
```
If the actual repo already has different paths, preserve them and update documentation rather than silently restructuring.

## Agent session starter — paste at the beginning of every agent conversation
> You are assisting the **Distributed Grocery DBMS Project**. Read `AGENTS.md`, `README.md`, and relevant `site-docs/` documents before suggesting edits. I am **[MEMBER NAME]** on **[EXACT LAPTOP NAME]**, responsible for **[ROLE]**. Current milestone: **[GATE NUMBER]**. My next task: **[TASK]**. First inspect existing code and report assumptions, files you intend to modify, required tools/versions, and possible conflicts. Work in my own branch. Keep schema/API contracts unchanged unless a reviewed proposal is approved. Implement only this task, execute tests you can genuinely run, give the exact commands and expected outputs for my OS, and explain the DBMS concepts so I can answer professor Q&A. Never invent tests/results or enable permission-bypass flags.

## Task completion report format
- **Owner / laptop:**
- **Task and branch:**
- **Files created or modified:**
- **Manual commands to run (OS-specific):**
- **Verified tests and outputs:**
- **Unverified items / blockers:**
- **DBMS concepts learned:**
- **Schema / API contract changed?** (If yes, approval and migration link)
- **Pull request URL and review request:**

## Authoritative references
- PostgreSQL 18: https://www.postgresql.org/docs/18/
- PostgreSQL logical replication limitations: https://www.postgresql.org/docs/18/logical-replication-restrictions.html
- FastAPI: https://fastapi.tiangolo.com/
- SQLAlchemy: https://docs.sqlalchemy.org/en/20/
- Alembic: https://alembic.sqlalchemy.org/
- Locust: https://docs.locust.io/en/stable/
- Instacart dataset: https://www.kaggle.com/competitions/instacart-market-basket-analysis/data
- GitHub pull requests: https://docs.github.com/en/pull-requests/get-started/pull-request-quickstart
