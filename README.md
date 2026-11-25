# apogee-telescope

A debugging dashboard for [Apogee](https://github.com/venomous-maker) apps:
every request and everything it did (its queries, log lines, exceptions, model
changes, dumps), every query (slow ones and repeated ones, the N+1 kind,
flagged), exceptions, log lines, queued jobs and model changes, kept for a
while and shown at `/telescope`. One page with its own CSS and script (no
build step), light and dark.

```typescript
import "std"
import "apogee-telescope"
use apogee.*

Telescope.use(new DatabaseTelescopeStore(pool))     // then .createTable() (in an app: the migration); or the memory store (the default)
Telescope.watchQueries()                             // Orbit's queries (DB.listen)
Telescope.watchExceptions(kernel)                    // what the HTTP kernel reports
Telescope.watchJobs(events)                          // the queue's events
Telescope.watchModels()                              // Orbit saves and deletes
logs.addHandler(new TelescopeLogHandler())           // grav-logger lines

router.aliasMiddleware("telescope", fn(a: Array<string>) -> Middleware { return new TelescopeMiddleware() })
router.use("telescope")                              // record every request
Telescope.routes(router)                             // the page at /telescope

Telescope.dump(cart, "the cart")                     // a value to look at later
```

It is meant for development and staging: requests are recorded with their
headers, payload and response, with passwords, tokens, cookies and the like
hidden (`Telescope.hidden` lists the names).

## In an apogee-framework app

Importing the module is all it takes: `TelescopePackage` (apogee-package) connects it when
the app is created. When `telescope.enabled`, every request and what it did is recorded and shown at `/telescope`: queries (slow ones and N+1 flagged), models, exceptions, jobs and log lines. The page is open to anyone on the machine in the `local` environment; elsewhere only to a signed-in user the gate allows `viewTelescope` (define it). Its routes run with the session and refuse cross-site requests that change anything.

**Migration:** `CreateTelescopeEntriesTable` makes the `telescope_entries` table. Run `migrate` after adding the module.

**Commands:** `telescope:prune --hours=24` and `telescope:clear`.

| Setting | Default |
|---|---|
| `telescope.enabled` | `TELESCOPE_ENABLED`: on only when `APP_ENV` is `local` |
| `telescope.driver` | `TELESCOPE_DRIVER`: `database` (default) or `memory` |
| `telescope.path` | `TELESCOPE_PATH`, `telescope` |
| `telescope.slow_query_ms` | `TELESCOPE_SLOW_QUERY_MS`, 100 |
| `telescope.ignore_paths` | `TELESCOPE_IGNORE_PATHS`, comma-separated |

Your own configuration wins over these defaults.

## What it records

| Tab | From | Tags |
|---|---|---|
| Requests | `TelescopeMiddleware` | `status:200`, `failed` (5xx), `repeated-queries` |
| Queries | `watchQueries()` | `slow` (over `Telescope.slowQueryMs`, 100), `repeated` (the same statement `Telescope.repeatedQueries` times, 5, in one request) |
| Exceptions | `watchExceptions(kernel)`, `Telescope.recordException(e)` | the exception's kind |
| Logs | `TelescopeLogHandler` | the level |
| Jobs | `watchJobs(events)` | `queued`, `processed`, `failed`, and the job's own tags |
| Models | `watchModels()` | `created`, `updated` (with the changes), `deleted` |
| Dumps | `Telescope.dump(value, label)` | |

Entries made while a request runs belong to its batch: a request's page lists
everything it did, and each entry links back. `Telescope.startBatch()` and
`endBatch()` make a batch elsewhere (around a job, a command).

`Telescope.ignorePaths` (prefixes) are not recorded, nor is the dashboard
itself; `Telescope.filter` decides entry by entry; `Telescope.pause()` and
`resume()` stop and start recording (also from the page).

## Stores

| | |
|---|---|
| `MemoryTelescopeStore(keep = 1000)` | the newest `keep` of each type, in this process |
| `DatabaseTelescopeStore(pool, table = "telescope_entries")` | SQLite, MySQL or PostgreSQL; shared by every process; `prune(hours)` removes old entries (from a scheduled task) |

## The page and its API

Tabs per kind with counts, a tag filter (click any tag), older entries on
demand, and a detail panel with every field and the rest of the batch; clear
and pause buttons. The API under the prefix: `GET /api/entries?type=&tag=&batch=&before=&limit=`,
`GET /api/entries/{id}` (with `related`), `GET /api/counts`,
`POST /api/clear`, `/api/pause`, `/api/resume`.

By default only requests from this machine may see it; `Telescope.setGate(fn(Request) -> bool)`
decides otherwise.

## Tests

```bash
grav test tests/telescope.grav
grav test tests/package.grav
```
