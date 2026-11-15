# DBMS Project — Six-Member Setup, Assignments, and AI-Agent Guide

**Project:** Distributed Grocery E-Commerce Database System with Performance Optimization and Load Balancing  
**Status:** Planning / setup; no services claimed working yet  
**Objective:** Demonstrate a functional **locally hosted PostgreSQL database** containing large amounts of data, show real bottlenecks, apply DBMS techniques, and present reproducible before/after evidence. **The professor prohibits cloud-hosted databases, not GitHub or AI-assisted coding.**

> **Start here:** The project lead coordinates architecture, personally chooses/builds the **customer storefront**, and approves shared contracts. The six members use AI agents to help implement and *learn* their tasks. **No router test, full dataset, or six-node deployment is required to start coding.**

## 1. Laptop assignments — names to use consistently

| Laptop | Project role | Responsibility during the demo |
|---|---|---|
| **Windows 1 (Menard)** | **Backend & Application Integration / Hosting** | Run the FastAPI backend and serve the storefront the project lead developed; show login, browsing and checkout transactions |
| **Windows 2 (JC)** | **Performance & Monitoring** | Run the dashboard and demonstrate real SQL benchmark plans, before/after results, metrics |
| **Windows 3 (Michell)** | **Primary PostgreSQL Administrator & Schema Deployment** | Run the authoritative primary PostgreSQL database; deploy **reviewed** migrations, data integrity checks and indexes |
| **Windows 4 (Rei)** | **Replica B & Backup/Recovery** | Run Replica B; demonstrate replication status and recovery of a small test backup |
| **MacBook M2 (Diane)** | **Replica A, Networking & HAProxy** | Run Replica A and HAProxy; show read/write endpoints, read-connection distribution and LAN setup |
| **Chromebook (Ralph)** | **Dataset Engineering & Load Testing** | Prepare importer/generator scripts; drive Locust traffic and export test statistics |

**Project lead (you; additional duty within the six, not a seventh role):** Choose/adapt the storefront UI, lead Git and architecture reviews, approve shared ERD and API contracts, review pull requests, prepare the professor's early review, coordinate demonstration and hand off frontend deployment to **Windows 1 (Menard)**. **Michell is not solely responsible for database design decisions**; all six review the schema and the lead approves changes. Menard is not asked to independently recreate a second storefront.

**Storage reminder:** Windows 1 (Menard) has **300 GB free on the secondary HDD**; all storage values supplied previously referred to **free space**. Large CSVs or backup archives can be staged there if necessary, though SSD is preferable for the primary database. Avoid storing huge data on Windows 2 (JC) or Chromebook (Ralph).

## 2. Complete target architecture (after integration)

```text
                      SIX LAPTOPS ON ONE LOCAL LAN
                PLDT FiberHome HG6245D, 5 GHz preferred
                     Tenda F6 fallback; WAN not needed
                                  |
              +-------------------+--------------------+
              |                   |                    |
 Windows 1 (Menard)       Windows 2 (JC)      Chromebook (Ralph)
 Storefront + FastAPI     Metrics dashboard   Locust simulated users
       |                         ^                       |
       | read/write selection    +--- read-only metrics <-+
       v
 MacBook M2 (Diane): HAProxy database proxy
       |                             |
       | WRITE endpoint              | READ endpoint (TCP connection balancing)
       v                             +---------+---------------------+
 Windows 3 (Michell)                        |                     |
 Primary PostgreSQL                 MacBook M2 (Diane)     Windows 4 (Rei)
 All writes & critical reads        PostgreSQL Replica A   PostgreSQL Replica B
       |                                  ^                     ^
       +---------- logical replication --+---------------------+
```

**Important distinctions**
- **Application/API server** = Windows 1 (Menard); **database nodes** = Windows 3 (Michell), Windows 4 (Rei), and MacBook M2 (Diane). Six laptops participate, but not all six need copies of PostgreSQL.
- FastAPI selects the **write** or **read** database endpoint. HAProxy at TCP level balances **connections**, not individual SQL queries. Pooling can affect how evenly connections are spread.
- Logical replication copies selected table changes from **Windows 3 (Michell)** to subscribers, but **does not automatically replicate schema changes or sequence state**. Treat replicas as read-only for the application. Some critical reads (such as checkout inventory) belong on primary because replication can lag.
- This is the **target architecture**. Begin with one PostgreSQL server + sample data. Do **not** attempt HAProxy or full replication as the first coding task.
- The existing PLDT HG6245D was previously used as a Wi-Fi extender. **Offline standalone DHCP/client-to-client LAN has not yet been tested**. The router must not block development. The group can use GitHub for code collaboration while apart, and a private LAN when physically together. VPN/Tailscale is optional for remote access, not a dependency for database benchmark results.
- Proposed addresses (if needed later) should come from DHCP reservations after testing; do not hardcode `192.168.1.x` now. The 5 GHz Wi-Fi can itself become a bottleneck; measure network performance separately (`ping`, `iperf3`) from database timings.

## 3. Build order: what gets approved before agents write major code

| Gate | Work | Completion evidence | Who drives it |
|---|---|---|---|
| **0 — Tools** | Check existing Python/Git/Node/PostgreSQL, install only missing tools; create Git repo | Version screenshots/logs; everyone can clone repo | Project lead + all |
| **1 — Contracts** | ERD (tables/relationships), normalization, Instacart CSV mapping, API contract, migration plan | Reviewed Mermaid ERD, `schema/` migration, sample SQL tests, agreed API endpoints | Lead approves; Michell implements; Ralph/Menard/JC review |
| **2 — Working base** | Backend + storefront using one local PostgreSQL and small sample data | Products appear in browser from PostgreSQL; sample checkout transaction works | Lead (UI), Menard (API/hosting), Michell (DB), Ralph (sample data) |
| **3 — Early review candidate** | Bulk import, measured slow-query baseline, indexing and query optimization | Millions of rows or documented achievable subset; saved `EXPLAIN` plans; repeatable actual timings | Ralph, JC, Michell |
| **4 — Distributed setup** | Check offline router LAN, configure replicas, HAProxy read/write endpoints | Replicated changes verified and actual read connections observed on **both** replicas | Diane + Rei + Michell + Menard |
| **5 — Demo-ready** | Concurrency, backup/recovery, monitoring, load tests and Q&A | Offline rehearsal; scripted startup/shutdown; evidence bundle; six members explain work | All six; lead coordinates |

**Early professor feedback:** Aim to show Gate 3 as soon as it is reliably reproducible; don't wait until every distributed feature is complete. No fixed deadline is assumed here because the professor rewards early review/submission.

## 4. Software: exact sites, preferred versions, and who needs what

### Common policy for Python
**Do not force everyone to install exactly Python 3.13.17.** First check `python --version` (or `py --version` on Windows). **Python 3.11+** can be acceptable if the project's dependencies install and tests pass; **3.12 or 3.13 are the easiest common targets**. Python 3.10 is end-of-life; upgrade from 3.10 or older. Use an isolated `.venv` in the project and create tested dependency locks. Different patch versions within the same supported minor version usually don't require a new installation. **Never overwrite the system Python on ChromeOS Linux or macOS.**

### Official links and installation policy
| Tool | Purpose | Version/policy | Download / docs |
|---|---|---|---|
| **Git** | shared code / branches | current stable | https://git-scm.com/downloads |
| **GitHub** | public code repository, issues and PR review (not DB hosting) | account | https://github.com/ ; https://docs.github.com/en/get-started |
| **VS Code** | editor | current stable; optional if using another editor/agent IDE | https://code.visualstudio.com/ |
| **Python** | API, data scripts, Locust | prefer installed 3.12/3.13; minimum 3.11 for planned toolchain after compatibility check | https://www.python.org/downloads/ |
| **Node.js** | React frontend tooling | **24.x LTS** for consistent project builds; current LTS also possible after tests | https://nodejs.org/en/download/ |
| **PostgreSQL** | actual DBMS | **18.x stable**, same major series on all DB nodes | https://www.postgresql.org/download/windows/ ; https://www.postgresql.org/download/macosx/ |
| **DBeaver Community** | lightweight SQL GUI client | current stable (optional but useful) | https://dbeaver.io/download/ |
| **FastAPI** | backend library | install through project `.venv` and lockfile, not standalone GUI | https://fastapi.tiangolo.com/ |
| **SQLAlchemy / Alembic** | models and DB schema migrations | 2.x SQLAlchemy; compatible Alembic lockfile | https://docs.sqlalchemy.org/en/20/ ; https://alembic.sqlalchemy.org/ |
| **Locust** | simulated HTTP users | compatible current stable; lock after testing | https://docs.locust.io/en/stable/installation.html |
| **HAProxy** | database connection proxy | macOS package after Gate 3; not needed now | https://www.haproxy.org/ ; https://formulae.brew.sh/formula/haproxy |
| **Homebrew** | macOS package manager | current stable | https://brew.sh/ |
| **Orca (stablyai/onOrca)** | optional workspace for running coding agents | choose OS-specific build | https://orca.dev/ ; https://github.com/stablyai/orca |
| **AI coding agent** | writes/edits/tests code under member supervision | Codex, Claude Code, etc.; select one primary agent per task | https://developers.openai.com/codex/ ; https://code.claude.com/docs/en/overview |

**Orca details:** Orca is a **manager/workspace**, not the underlying AI model or subscription. Install Orca only if you want it; install/sign in to a supported coding agent separately. Choose **Windows x64**, **macOS Apple Silicon** for Diane, or **Linux** for Chromebook (Ralph) if supported by that Chromebook's Linux environment. On MacBook M2 (Diane), official repository instructions show `brew install --cask stablyai/orca/orca` (not bare `orca`). **Review Orca's agent permission flags**: do not use automatic approval-bypass / `--yolo` / `--dangerously-*` configurations for commands affecting real computers or databases.

### What each member installs **now** (don't install everything)
| Laptop | Check/install now | Save for later |
|---|---|---|
| **Windows 1 (Menard)** | Git; VS Code or Orca/agent; Python >=3.11; Node.js 24.x LTS; virtual environment | local/remote PostgreSQL connection, app hosting setup |
| **Windows 2 (JC)** | Git; VS Code or Orca/agent; Python >=3.11; DBeaver Community; Node only if building the React monitoring UI | DB read-only monitoring credentials, metrics connection |
| **Windows 3 (Michell)** | Git; VS Code/agent; PostgreSQL 18.x server + CLI/pgAdmin; Python >=3.11 if running migrations/scripts | replication publication, secure LAN access, full CSV imports |
| **Windows 4 (Rei)** | Git; PostgreSQL 18.x server + CLI; VS Code/agent | subscriber configuration, scheduled backups |
| **MacBook M2 (Diane)** | Git, Homebrew, VS Code/agent; PostgreSQL 18.x | HAProxy, logical subscription, LAN rules |
| **Chromebook (Ralph)** | Git; Python >=3.11 (in Linux), `python3-venv`, agent/VS Code if feasible; Locust in virtual environment | final Locust scripts; **do not store full Kaggle dataset locally** |

> Installation is a human-reviewed task. If something is already installed and compatible, record its current version and **skip reinstalling**.

### Initial version-check commands

**Windows PowerShell** (Windows 1 (Menard), Windows 2 (JC), Windows 3 (Michell), Windows 4 (Rei)):
```powershell
py -0p             # available Python interpreters, if Python launcher installed
python --version    # if command resolves
 git --version
node --version      # only members who need Node
npm --version       # only members who need Node
psql --version      # PostgreSQL nodes; use SQL Shell if not on PATH
```
If `node` or `psql` is not recognized and the member doesn't need it yet, that is **not an error**. If `python` points to a Windows Store alias, use `py -3.12` or the installed compatible version instead.

**macOS Terminal** (MacBook M2 (Diane)):
```bash
python3 --version
 git --version
brew --version
# After brew install postgresql@18:
"$(brew --prefix postgresql@18)/bin/psql" --version
```
**ChromeOS Linux Terminal** (Chromebook (Ralph)):
```bash
python3 --version
git --version
```

### Minimal install / verify commands by OS
**Windows 3 (Michell) / Windows 4 (Rei): PostgreSQL**
1. Download EDB's interactive installer through the official PostgreSQL **Windows** download page above; pick **18.x stable**, not PostgreSQL 19 release candidates. Install Server + Command Line Tools; pgAdmin optional; note local administrator password; default PostgreSQL port 5432.
2. Open PostgreSQL SQL Shell (`psql`) or pgAdmin Query Tool and run:
```sql
SELECT version();
```
3. On **Windows 3 (Michell)** only, after the schema plan is approved, create a local dev database (example):
```sql
CREATE DATABASE grocery_db;
```
Do not change `listen_addresses`, open firewalls, or create subscriptions yet.

**MacBook M2 (Diane): PostgreSQL**
```bash
# After Homebrew installation
brew install postgresql@18
brew services start postgresql@18
"$(brew --prefix postgresql@18)/bin/psql" --version
# LATER (Gate 4), not today:
# brew install haproxy
```

**Chromebook (Ralph): Python environment and Locust**
```bash
sudo apt update
sudo apt install -y git python3 python3-pip python3-venv
python3 --version                   # check: ideally 3.11+
mkdir -p ~/dbms-loadtest && cd ~/dbms-loadtest
python3 -m venv .venv
source .venv/bin/activate
python -m pip install locust
locust --version
```
If Linux Python is **below 3.11**, pause Locust installation, tell the agent the exact version and distro (`cat /etc/os-release`), and ask for a compatible separate Python interpreter. **Do not replace the OS Python.**

**Windows 1 (Menard): FastAPI smoke test** (once Python is available and the project folder is created on the drive with space):
```powershell
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install fastapi uvicorn
```
Save `main.py` in that test folder:
```python
from fastapi import FastAPI
app = FastAPI()
@app.get('/health')
def health():
    return {'status': 'ok'}
```
Run:
```powershell
.\.venv\Scripts\python.exe -m uvicorn main:app --reload
```
Open `http://127.0.0.1:8000/health` — should return `{"status":"ok"}`. This checks the backend toolchain **only**, not yet the real database connection.

**Windows 2 (JC): DBeaver** — install Community edition and open it. **Do not** request a shared PostgreSQL password before secure LAN/database users exist. Later create a PostgreSQL connection using the assigned read-only account; never share the `postgres` administrator password in chat or Git.

## 5. How to set up GitHub collaboration (the project lead does this first)

1. Create a **private GitHub repository**, e.g. `distributed-grocery-dbms`, and invite all six GitHub accounts as collaborators. Code may be hosted on GitHub; PostgreSQL data remains on laptops.
2. Put **`AGENTS.md`** (the accompanying file), this guide, and a minimal `README.md` at repository root. The guide can later be moved under `site-docs/`.
3. Add `.gitignore` entries for `.env`, `.venv/`, `__pycache__/`, `node_modules/`, `.pytest_cache/`, `data/raw/`, `data/exports/`, `*.csv` for bulk data (allow tiny documented fixtures explicitly), `*.backup`, `*.dump`, `pgdata/`, and benchmark output files too large to review. Never commit credentials or full Kaggle CSV files.
4. Each member clones the repo using their own GitHub access:
```bash
git clone <REPOSITORY_URL>
cd distributed-grocery-dbms
git checkout -b feat/<owner>-<feature>
# Edit files; run local tests
git add <specific-files>
git commit -m "feat: concise description"
git push -u origin feat/<owner>-<feature>
```
5. Open a **pull request (PR)** on GitHub. A PR is a proposed change the lead and relevant owner can review before merging. Never have six agents write the same files directly on `main`.
6. **Contract review first:** Have the AI agent generate `site-docs/erd.md`, `site-docs/api-contract.md`, `site-docs/data-mapping.md`, and migration proposal. Lead reviews, then *all* members approve relevant table names, column names, data relationships and endpoints. Only then create feature branches against the same agreed contract.
7. If schema must change later, use a new reviewed **Alembic migration**, explain who it affects, update test fixtures/contracts, and apply schema changes to subscriber DBs in the correct order during replication phase.

**Do not upload exported PostgreSQL data or backup dumps to GitHub**. Test data should be reproducible from scripts and small permitted sample fixtures.

### Folder ownership (to reduce AI merge conflicts)
```text
frontend/               project lead builds customer storefront; Menard deploys/integrates
backend/                Windows 1 (Menard)
monitoring/              Windows 2 (JC)
schema/                  Windows 3 (Michell) applies reviewed database decisions
scripts/data/            Chromebook (Ralph) creates importer/generator scripts
benchmarks/sql/          Windows 2 (JC)
benchmarks/locust/       Chromebook (Ralph)
infra/replication/       Windows 4 (Rei) + MacBook M2 (Diane), coordinated
infra/haproxy/           MacBook M2 (Diane)
infra/backup/            Windows 4 (Rei)
site-docs/               shared; final approval by project lead
AGENTS.md                shared rules; changes require lead review
```

## 6. What is being built? (not just dashboard appearance)

**Customer storefront** (project lead develops, Menard hosts/integrates): real product browsing, search and filters, demo login, cart, order history, simulated checkout. UI template may be selected later; agent should keep styling replaceable. No real payment processing.

**Admin/DBMS dashboard** (Windows 2 (JC)): report **measured** records, query plans/times, CPU/RAM where available, node health, replication lag, Locust request metrics, read distribution, and clearly separated baseline vs optimized benchmark results. **It is not only a pretty UI**; JC must create/reuse scripts that gather these metrics.

**Original CSV dataset** (Chromebook (Ralph) designs importer; run full imports on Windows 3 (Michell) or storage-rich staging): [Instacart Market Basket Analysis](https://www.kaggle.com/competitions/instacart-market-basket-analysis/data). Files typically include `orders.csv`, `products.csv`, `aisles.csv`, `departments.csv`, `order_products__prior.csv`, and `order_products__train.csv`. Historical data are anonymized and not a full ready-to-run shop. Add **synthetic** demo credentials, prices, inventory, carts, and new checkout records, with provenance labeling. Follow competition/data reuse rules. Initial small sample → 100K → 1M → higher volumes only after storage/benchmark review.

**ERD** = diagram of tables and their PK/FK relationships. **Normalization** (usually targeting 3NF for operational tables) is part of schema design. **Migration** = a version-controlled schema change (`CREATE TABLE`, `ALTER TABLE`, etc.), normally run with Alembic. **Benchmark query** = *actual SQL executed* while measuring plan, runtime and resource use—not a decorative dashboard card.

**Sample benchmark scenario:** Query historical orders for a selected customer and `ORDER BY` order number with a `LIMIT`; measure `EXPLAIN (ANALYZE, BUFFERS)` before and after a properly chosen composite index. Repeat trials on the same node/data set, record cold/warm-cache issues. The real table/column names must come from the approved schema. Also test a large join/aggregation, and simultaneous read traffic with Locust. Do not fake slowdowns with `sleep()`.

## 7. Agent instructions: how every member starts and follows up

**A. One-time setup for all agents:** clone/open the repo, then paste the following text into Codex, Claude Code or other chosen agent; if using Orca, open the same repo/worktree with the agent. Also keep `AGENTS.md` at repository root.

```text
We are a six-student team building a locally hosted Distributed Grocery DBMS project.
Read AGENTS.md and this project's README/setup guide first. Follow the
approved architecture, DB schema, API contract, member boundaries, milestone
gates, local-only database rule, and review process. I am [YOUR NAME] on
[EXACT LAPTOP NAME] and I own [ROLE]. Be a patient mentor: tell me commands
for my OS, explain what each part does, implement small tested increments,
report every test honestly, and give me likely professor Q&A questions.
Never silently redesign the shared schema, APIs or repo structure.
Before writing code, list files you intend to change and any dependencies.
```

**B. Follow-up on future sessions:**
```text
Continue our project using AGENTS.md and the current repository state.
I am [YOUR NAME] on [EXACT LAPTOP]. Current gate: [0–5].
My next assigned task: [DESCRIPTION]. Inspect existing code and contracts,
implement only my task on branch feat/[owner]-[feature], run tests, explain
what I must run manually, and draft a PR summary and verification report.
```

### Ready-to-paste first tasks for each member

#### Project lead (you) — build storefront, own architectural approvals
```text
Read AGENTS.md. I'm the project lead. Propose and set up the shared repo,
ERD/API-contract review workflow and acceptance tests. I'm personally
building/adapting the grocery customer storefront (React) from a legal
editable template I will choose. Keep frontend components modular and
API-driven; use temporary mock data only while FastAPI endpoints are
unavailable, label it mock, and replace it when contracts are approved.
Do not create a second competing backend or change the approved schema.
Prepare the eventual handoff to Windows 1 (Menard) with exact deploy/start
commands, frontend environment variables and build assets for an offline LAN.
```

#### Windows 1 (Menard) — backend and storefront handoff
```text
Read AGENTS.md and approved ERD/API contracts. I am Menard on Windows 1
(Menard). I do NOT need to design a separate storefront: the project lead
will hand off the React frontend from GitHub. For Gate 0, inventory Python,
Git and Node versions; prepare a .venv and a FastAPI /health smoke test.
After Gate 1, implement tested PostgreSQL-backed product listing/search,
demo auth, cart, order history and transactional checkout endpoints matching
the shared API contract. Initially read/write on one local PostgreSQL server;
prepare separate configurable endpoints for later. Later integrate and host
the project lead's storefront on this laptop. Give exact PowerShell commands,
manual tests and a Browser→FastAPI→PostgreSQL explanation.
```

#### Windows 2 (JC) — real benchmarks and metrics
```text
Read AGENTS.md and approved schema. I am JC on Windows 2 (JC). For Gate 0,
check Python, Git and DBeaver; avoid a full database install on my limited
SSD. Propose three real PostgreSQL SQL experiments and their metrics
(EXPLAIN ANALYZE BUFFERS, rows, plans, timings), with a repeatable baseline
and optimization procedure. Create scripts to export actual measurements
and then a dashboard to display measured values, not placeholder charts.
Coordinate index changes with Windows 3 (Michell); no shared schema changes
without approval. Explain how query time differs from HTTP latency and
throughput. Include tests and Windows setup commands.
```

#### Windows 3 (Michell) — primary DB deployment (shared design review)
```text
Read AGENTS.md. I am Michell on Windows 3 (Michell). I administer the primary
PostgreSQL server and implement the SCHEMA ONLY AFTER group/lead review.
For Gate 0, install/verify PostgreSQL 18.x and Git; create a local test DB.
For Gate 1, propose Instacart CSV mapping, ERD, constraints and normal forms
for shared review; implement approved SQLAlchemy models/Alembic migrations
and validate with sample inserts and FK tests. Coordinate import and indexes
with Ralph and JC. Do not act as the only architecture decision-maker or
prematurely enable LAN/replication. Provide exact PowerShell commands,
version checks, backups before destructive changes and verification results.
```

#### Windows 4 (Rei) — backup first, replica later
```text
Read AGENTS.md. I am Rei on Windows 4 (Rei). For Gate 0, install/verify
PostgreSQL 18.x. Practice backing up and restoring a SMALL local database,
with commands for pg_dump, pg_restore/psql and row-count verification.
Later (Gate 4), build a logical subscriber from Windows 3 (Michell), deploy
matching approved schema, verify initial copy and incoming changes, use an
application read-only account, monitor replication delay, and document
recovery limits. Do not alter the primary DB or expose PostgreSQL broadly.
Provide exact PowerShell commands, tests and troubleshooting.
```

#### MacBook M2 (Diane) — replication and HAProxy
```text
Read AGENTS.md. I am Diane on MacBook M2 (Diane). For Gate 0, verify Homebrew,
Git and PostgreSQL 18.x for Apple Silicon; do not configure HAProxy until the
primary and schema are ready. For Gate 4, configure Replica A as a logical
subscriber from Windows 3 (Michell); then design HAProxy separate TCP write
endpoint (primary only) and read endpoint (replicas A and B) and document
health checks, connection distribution, API read/write routing, network
firewall and LAN prerequisites. Never claim HAProxy routes SQL statements by
itself. Provide macOS commands and real verification steps.
```

#### Chromebook (Ralph) — importer and load tests
```text
Read AGENTS.md. I am Ralph on Chromebook (Ralph). For Gate 0, verify Linux
Python (do not replace system Python), create a .venv, install and verify
Locust. My Linux free space is limited: do not put the complete Instacart CSVs
or database here. Design restartable CSV import scripts and a clearly labeled
synthetic generator to RUN on Windows 3 (Michell) or an approved staging disk.
Start with a tiny fixture; validate foreign keys, row counts and provenance.
After backend endpoints work, create Locust scripts simulating genuine
product browse/search/cart actions, with configurable users, spawn rate,
run duration and CSV export. Explain throughput, p95 latency and errors.
```

## 8. Day of integration / professor demo: run order and evidence

**Before everyone meets:** work independently with small local test DBs and Git feature branches. Do not leave shared logical subscriptions disconnected for days without a plan—replication slots can retain WAL on the primary. Import big data and complete initial replica synchronization **ahead of the demo**.

**At integration:** verify the router (no fiber/WAN, 5 GHz and DHCP if standalone, no client isolation), identify LAN IPs, ping/TCP test and optionally `iperf3`. Have **Windows 3 (Michell)** start primary DB; **Windows 4 (Rei)** and **MacBook M2 (Diane)** start subscribers; **MacBook M2 (Diane)** verify HAProxy; **Windows 1 (Menard)** start API/frontend; **Windows 2 (JC)** start real monitoring; **Chromebook (Ralph)** run Locust. Confirm all services before large tests.

**Suggested demonstration:**
1. Project lead opens the polished storefront and briefly explains architecture.
2. **Windows 1 (Menard):** product search, login, cart, simulated checkout through API.
3. **Windows 3 (Michell):** actual tables, PK/FK relationships, row counts, primary status.
4. **Windows 2 (JC):** query before/after index and actual execution plans.
5. **Chromebook (Ralph):** controlled Locust traffic and measured throughput/p95/errors.
6. **Windows 4 (Rei):** show replicated records and safe small-test backup/restore.
7. **MacBook M2 (Diane):** show replication and actual connections distributed across both read replicas.
8. All six answer individual DBMS questions. Avoid promising a speedup that the measured workload does not show.

**After demo:** export benchmark results, logs and screenshots; confirm data integrity; stop Locust, HAProxy, API, and DB services safely. Backups/dumps are local, outside Git.

## 9. Minimum first-day checklist and what to report

- [ ] **Project lead:** Create private GitHub repo; upload this guide + `AGENTS.md`; choose agent; create initial issues and approve the first task boundaries.
- [ ] **Windows 1 (Menard):** Verify Git/Python/Node and make FastAPI `/health` return JSON.
- [ ] **Windows 2 (JC):** Verify Git/Python/DBeaver and draft 3 SQL benchmark scenarios (not dashboard screenshots).
- [ ] **Windows 3 (Michell):** Install/verify PostgreSQL 18.x and create small test DB; draft schema for *group* review.
- [ ] **Windows 4 (Rei):** Install/verify PostgreSQL 18.x, make a tiny backup and restore.
- [ ] **MacBook M2 (Diane):** Verify Homebrew/Git/PostgreSQL 18.x; prepare HAProxy prerequisites **only**.
- [ ] **Chromebook (Ralph):** Verify Python >=3.11 + Git + Locust in Linux .venv; prepare first tiny data import fixture.
- [ ] **Group:** Agree that all schema and API changes require a PR and lead approval before integration.

**Group chat report format:**
```text
Member + laptop:
Installed tools / versions:
Task + feature branch:
What works (verified command and output):
What's blocked / needs approval:
Next task:
PR or commit link:
```

## 10. Sources, downloads, and learning resources
- [PostgreSQL main download](https://www.postgresql.org/download/) · [PostgreSQL 18 docs](https://www.postgresql.org/docs/18/) · [Logical replication limitations](https://www.postgresql.org/docs/18/logical-replication-restrictions.html)
- [Python downloads](https://www.python.org/downloads/) · [Supported Python versions](https://devguide.python.org/versions/)
- [FastAPI docs](https://fastapi.tiangolo.com/) · [FastAPI SQL database guide](https://fastapi.tiangolo.com/tutorial/sql-databases/)
- [SQLAlchemy 2.0](https://docs.sqlalchemy.org/en/20/) · [Alembic](https://alembic.sqlalchemy.org/)
- [Node.js downloads](https://nodejs.org/en/download/) · [React](https://react.dev/)
- [Git download](https://git-scm.com/downloads) · [GitHub PR quickstart](https://docs.github.com/en/pull-requests/get-started/pull-request-quickstart)
- [DBeaver](https://dbeaver.io/download/) · [Locust install](https://docs.locust.io/en/stable/installation.html) · [Locust quickstart](https://docs.locust.io/en/stable/quickstart.html)
- [Homebrew](https://brew.sh/) · [HAProxy](https://www.haproxy.org/) · [HAProxy Homebrew](https://formulae.brew.sh/formula/haproxy)
- [Orca app (formerly onOrca)](https://orca.dev/) · [Orca source/install details](https://github.com/stablyai/orca)
- [Instacart Market Basket Analysis dataset](https://www.kaggle.com/competitions/instacart-market-basket-analysis/data)

---
**Document purpose:** This is a *working team playbook*, not evidence that systems are already installed or configured. Adjust role ownership only through team agreement; keep device names exactly as shown. The project lead can replace UI templates later without changing approved DB schema/API contracts.
