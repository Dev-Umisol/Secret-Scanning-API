<a id="readme-top"></a>

<div align="center">

<h3 align="center">Secret Scanning API</h3>

  <p align="center">
    A developer security tool that scans GitHub repositories for accidentally committed secrets: API keys, tokens, passwords and returns a severity-ranked report in a React dashboard.
    <br />
    <a href="https://github.com/Dev-Umisol/Secret-Scanning-API"><strong>Explore the docs »</strong></a>
    <br />
    <br />
    <a href="https://github.com/Dev-Umisol/Secret-Scanning-API/issues/new?labels=bug">Report Bug</a>
    &middot;
    <a href="https://github.com/Dev-Umisol/Secret-Scanning-API/issues/new?labels=enhancement">Request Feature</a>
  </p>

  <p align="center">
    <strong>Status:</strong> In active development. Schema and API design complete; detection logic (regex + entropy analysis) in progress.
  </p>

</div>

<!-- BADGES -->
<p align="center">
  <img src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white" />
  <img src="https://img.shields.io/badge/FastAPI-009688?style=for-the-badge&logo=fastapi&logoColor=white" />
  <img src="https://img.shields.io/badge/PostgreSQL-4169E1?style=for-the-badge&logo=postgresql&logoColor=white" />
  <img src="https://img.shields.io/badge/SQLAlchemy-D71F00?style=for-the-badge&logo=sqlalchemy&logoColor=white" />
  <img src="https://img.shields.io/badge/Redis-DC382D?style=for-the-badge&logo=redis&logoColor=white" />
  <img src="https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react&logoColor=61DAFB" />
  <img src="https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white" />
  <img src="https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white" />
</p>

<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#about-the-project">About The Project</a>
      <ul>
        <li><a href="#why-this-project">Why This Project</a></li>
        <li><a href="#scope-v1">Scope (v1)</a></li>
        <li><a href="#built-with">Built With</a></li>
      </ul>
    </li>
    <li>
      <a href="#getting-started">Getting Started</a>
      <ul>
        <li><a href="#prerequisites">Prerequisites</a></li>
        <li><a href="#installation">Installation</a></li>
      </ul>
    </li>
    <li><a href="#usage">Usage</a></li>
    <li>
      <a href="#architecture--design">Architecture &amp; Design</a>
      <ul>
        <li><a href="#database-schema">Database Schema</a></li>
        <li><a href="#cascade-rules">Cascade Rules</a></li>
        <li><a href="#indexes">Indexes</a></li>
        <li><a href="#api-routes">API Routes</a></li>
        <li><a href="#ownership--security-pattern">Ownership &amp; Security Pattern</a></li>
        <li><a href="#secret-masking">Secret Masking</a></li>
        <li><a href="#caching-strategy">Caching Strategy</a></li>
        <li><a href="#logout--token-revocation">Logout &amp; Token Revocation</a></li>
        <li><a href="#design-decisions-explicitly-considered-and-rejected">Design Decisions Considered and Rejected</a></li>
      </ul>
    </li>
    <li><a href="#roadmap--not-yet-built">Roadmap / Not Yet Built</a></li>
    <li><a href="#license">License</a></li>
    <li><a href="#contact">Contact</a></li>
  </ol>
</details>

<!-- ABOUT THE PROJECT -->
## About The Project

<!--
*** Add a dashboard screenshot here once the React frontend is built —
*** [![Dashboard Screenshot](images/screenshot.png)]
-->

A developer security tool that accepts a GitHub repo URL, scans all files for accidentally committed secrets: API keys, tokens, passwords and returns a severity-ranked report displayed via a React dashboard.

This is the fourth project in an ongoing portfolio series focused on production-style backend patterns (async job processing, caching, structured data modeling) rather than tutorial-style CRUD apps.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

### Why This Project

Secret exposure in public repos is one of the most common, costly, and preventable security vulnerabilities. This project demonstrates async HTTP integration, security-conscious data modeling, caching strategy, and full-stack development with TypeScript.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

### Scope (v1)

- **Public GitHub repositories only.** Scanning private repos would require storing users' GitHub access tokens, live credentials with real access, which conflicts with a tool whose entire purpose is preventing credential exposure. Revisiting this in a possible future direction.
- **Default branch only.** Full commit-history / all-branch scanning (like Gitleaks) was considered but scoped out, it multiplies GitHub API calls without teaching anything new about the core skills this project demonstrates (pattern matching, entropy analysis, async HTTP, caching).

<p align="right">(<a href="#readme-top">back to top</a>)</p>

### Built With

* [![FastAPI][FastAPI-badge]][FastAPI-url]
* [![PostgreSQL][Postgres-badge]][Postgres-url]
* [![SQLAlchemy][SQLAlchemy-badge]][SQLAlchemy-url]
* [![Redis][Redis-badge]][Redis-url]
* [![React][React-badge]][React-url]
* [![TypeScript][TypeScript-badge]][TypeScript-url]
* [![Docker][Docker-badge]][Docker-url]
* Alembic — schema migrations
* httpx — async GitHub API calls
* python-dotenv — config
* pwdlib — password hashing

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- GETTING STARTED -->
## Getting Started

### Prerequisites

* Docker & Docker Compose
* Python 3.11+
* Node.js 18+ (for the dashboard)

### Installation

<!--
*** Fill these in once your docker-compose.yml and .env.example are committed.
-->

1. Clone the repo
   ```sh
   git clone https://github.com/Dev-Umisol/Secret-Scanning-API.git
   cd Secret-Scanning-API
   ```
2. Copy the environment template and fill in your values
   ```sh
   cp .env.example .env
   ```
3. Start the services (API, PostgreSQL, Redis)
   ```sh
   docker-compose up --build
   ```
4. Run database migrations
   ```sh
   alembic upgrade head
   ```
5. Install and start the dashboard
   ```sh
   cd frontend
   npm install
   npm run dev
   ```

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- USAGE EXAMPLES -->
## Usage

<!--
*** Swap this for a real request/response pair once the detection engine is live.
-->

```sh
# Kick off a scan for a public repo
curl -X POST http://localhost:8000/scans \
  -H "Authorization: Bearer <token>" \
  -d '{"repo_url": "https://github.com/example/repo"}'

# Fetch the scan (returns metadata + nested findings in one response)
curl http://localhost:8000/scans/{id} \
  -H "Authorization: Bearer <token>"
```

Findings are severity-ranked (`critical`, `high`, `medium`, `low`) so the highest-risk exposures surface first, whether read via the API directly or browsed in the dashboard.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- ARCHITECTURE & DESIGN -->
## Architecture & Design

### Database Schema

Four tables, each representing a distinct entity with its own lifecycle:

```
Users <── Scans ──> Repos
            │
            v
          Secrets
```

**`users`**

| Column            | Notes                              |
| ----------------- | ---------------------------------- |
| `id`              | PK                                 |
| `email`           | login identifier                   |
| `hashed_password` | via `pwdlib` — never raw passwords |
| `created_at`      | audit trail                        |

**`repos`** — shared across users, not owned by any single account. If two users scan the same GitHub repo, they reference the same row.

| Column       | Notes                                                                                                                                                                                                    |
| ------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `id`         | PK (internal)                                                                                                                                                                                           |
| `github_id`  | **unique** — GitHub's permanent numeric ID, the true dedup key. Chosen over `owner`/`repo_name` because GitHub repos can be renamed or transferred; the numeric ID survives renames, the name doesn't. |
| `owner`      | refreshed on every scan (cheap, since we already hit the GitHub API)                                                                                                                                    |
| `repo_name`  | same as above                                                                                                                                                                                           |
| `created_at` | when this repo first entered the system                                                                                                                                                                 |

**`scans`** — one scan **attempt**, not the same as a repo. A repo can have many scans over time; this table is what makes scan history and re-scanning possible.

| Column          | Notes                                                           |
| --------------- | ---------------------------------------------------------------- |
| `id`            | PK                                                                |
| `user_id`       | FK → users                                                        |
| `repo_id`       | FK → repos                                                        |
| `status`        | `queued` → `in_progress` → `completed` / `failed`                |
| `error_message` | nullable; populated only on `failed`                              |
| `created_at`    | row creation - for debugging/audit, independent of `started_at`   |
| `started_at`    | nullable - null while `queued`                                    |
| `finished_at`   | nullable - set on `completed` or `failed`                         |

Duration is deliberately **not** stored as its own column — it's derived (`finished_at - started_at`) rather than kept in sync manually.

**`secrets`** — one finding, belonging to one scan attempt (not directly to a repo — this is what lets scan-over-scan history stay distinct).

| Column         | Notes                                                                                                                                                                 |
| -------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `id`           | PK                                                                                                                                                                    |
| `scan_id`      | FK → scans                                                                                                                                                            |
| `file_path`    | relative path in repo                                                                                                                                                 |
| `line_number`  |                                                                                                                                                                        |
| `masked_value` | e.g. `AKIA...3456` - **never the full secret** (see Secret Masking below)                                                                                             |
| `secret_type`  | plain text (e.g. `aws_access_key`) - not an ENUM, since new detectors get added over time and plain text avoids Postgres's `ALTER TYPE ADD VALUE` migration friction |
| `severity`     | Postgres **ENUM** (`critical`, `high`, `medium`, `low`) — fixed, small, stable set, so ENUM's declared-order sorting is worth the tradeoff here                      |
| `created_at`   | per-finding timestamp                                                                                                                                                |

<p align="right">(<a href="#readme-top">back to top</a>)</p>

### Cascade Rules

Each foreign key's `ON DELETE` behavior was chosen deliberately, not defaulted:

| Relationship        | Rule       | Reasoning                                                                                                                                                                                                 |
| ------------------- | ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `users` → `scans`   | `CASCADE`  | Individual-user scope, no team/billing model — deleting an account should erase associated data (GDPR-style right to erasure).                                                                       |
| `scans` → `secrets` | `CASCADE`  | An orphaned finding with no parent scan is meaningless — no way to trace it back to a repo or a point in time.                                                                                        |
| `repos` → `scans`   | `RESTRICT` | Repos are shared with no owner; nothing in the app should ever delete one. `RESTRICT` fails loudly if this path is ever hit unexpectedly, rather than silently cascading away other users' scan history. |

<p align="right">(<a href="#readme-top">back to top</a>)</p>

### Indexes

- `scans.user_id` - every "my scan history" query filters on this.
- `secrets.scan_id` - every "findings for this scan" query filters on this.
- `repos.github_id` - already indexed automatically via its `UNIQUE` constraint.
- No dedicated index on `severity` for sorting, results are filtered to a single scan first (small row count), so sorting the filtered set is cheap enough not to justify the write-time cost of an extra index.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

### API Routes

| Method   | Path              | Notes                                                                                                  |
| -------- | ----------------- | -------------------------------------------------------------------------------------------------------- |
| `POST`   | `/users/register` |                                                                                                            |
| `POST`   | `/users/login`    | Returns JWT `access_token`                                                                                |
| `POST`   | `/logout`         | Revokes the current token (see Logout & Token Revocation below)                                           |
| `POST`   | `/scans`          | Creates a new scan; repo row reused if `github_id` already known                                          |
| `GET`    | `/scans/{id}`     | Scan metadata **+ nested findings** in one response, avoids a second round-trip once a scan completes    |
| `GET`    | `/scans`          | Paginated (`skip`/`limit`, server-capped), ordered `created_at DESC`                                      |
| `DELETE` | `/scans/{id}`     |                                                                                                            |

<p align="right">(<a href="#readme-top">back to top</a>)</p>

### Ownership & Security Pattern

Every scan-scoped endpoint filters ownership **inside the query itself**:

```
WHERE id = {id} AND user_id = {current_user.id}
```

rather than fetching by `id` alone and checking ownership afterward in application code. Two reasons:

1. **No data exposure window** - another user's row is never even pulled into server memory.
2. **Uniform 404** - "doesn't exist" and "exists but isn't yours" produce the identical response, so there's no way to enumerate valid IDs by watching for a 403 vs. 404 split.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

### Secret Masking

Findings store only a masked fragment (e.g. first 4 + last 4 characters), never the live secret value. This means:

- The database is never itself a second source of leaked, working credentials.
- Full scan history can be retained indefinitely for trend comparison, since there's nothing sensitive left to protect in old rows.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

### Caching Strategy

Redis caches **final findings** (not raw file contents) keyed by `github_id`, with a TTL. If User B scans a repo User A scanned recently, the expensive work, GitHub API calls + entropy analysis, is skipped entirely; only a new `scans`/`secrets` row is written. Ownership is preserved: caching reuses *results*, never reuses another user's `scans` row.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

### Logout & Token Revocation

JWTs are stateless by design, the server holds no session record, so there's normally nothing to invalidate on logout. This project reintroduces minimal state deliberately: each token's `jti` (unique token ID) is stored in Redis with a TTL matching the token's remaining lifetime on logout. Every protected request now does one extra Redis lookup to check the token hasn't been revoked.

**Known tradeoff:** every authenticated request incurs a small added-latency Redis round-trip, in exchange for being able to immediately invalidate a token rather than waiting out its natural expiry.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

### Design Decisions Explicitly Considered and Rejected

- **Django/DRF instead of FastAPI** - deferred to the *next* portfolio project rather than adopted here, to avoid stacking a new backend framework on top of React/TypeScript, entropy analysis, and Redis dedup logic all being learned for the first time in this project.
- **Per-user duplicate `repos` rows** - rejected in favor of one shared row per `github_id`; ownership is enforced at the `scans` level, so sharing the repo row creates no security gap and avoids duplicate bookkeeping.
- **Storing full secret values, even encrypted** - rejected outright; masked fragments provide enough information to act on a finding without the database becoming a second leak vector.
- **Faking a loading delay on cache-hit scans** - rejected in favor of honest, fast results with no artificial UX delay or "found in cache" messaging (which itself could read as alarming, given the subject matter).

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- ROADMAP -->
## Roadmap / Not Yet Built

- [ ] Regex pattern matching + Shannon entropy analysis (detection engine — in progress)
- [ ] Rate limiting on `/scans` (prevent abuse of the GitHub API proxying)
- [ ] Input validation on submitted repo URLs
- [ ] React/TypeScript dashboard

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- CONTACT -->
## Contact

Destin Nguyen — [Portfolio](https://devdestinportfolio.vercel.app) — [GitHub](https://github.com/Dev-Umisol)

Project Link: [https://github.com/Dev-Umisol/Secret-Scanning-API](https://github.com/Dev-Umisol/Secret-Scanning-API)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- MARKDOWN LINKS & IMAGES -->
[FastAPI-badge]: https://img.shields.io/badge/FastAPI-009688?style=for-the-badge&logo=fastapi&logoColor=white
[FastAPI-url]: https://fastapi.tiangolo.com/
[Postgres-badge]: https://img.shields.io/badge/PostgreSQL-4169E1?style=for-the-badge&logo=postgresql&logoColor=white
[Postgres-url]: https://www.postgresql.org/
[SQLAlchemy-badge]: https://img.shields.io/badge/SQLAlchemy-D71F00?style=for-the-badge&logo=sqlalchemy&logoColor=white
[SQLAlchemy-url]: https://www.sqlalchemy.org/
[Redis-badge]: https://img.shields.io/badge/Redis-DC382D?style=for-the-badge&logo=redis&logoColor=white
[Redis-url]: https://redis.io/
[React-badge]: https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react&logoColor=61DAFB
[React-url]: https://reactjs.org/
[TypeScript-badge]: https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white
[TypeScript-url]: https://www.typescriptlang.org/
[Docker-badge]: https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white
[Docker-url]: https://www.docker.com/
