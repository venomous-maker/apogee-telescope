# Changelog

Every release of apogee-telescope, newest first. Each is a `vX.Y.Z` tag with a GitHub
release carrying its section; the `grav` line in `grav.toml` says which
compiler versions it builds with.

## Unreleased

## 0.2.2 (2026-11-28)

### Fixes

- A Redis connection shared by several threads (the cache, session and queue-dashboard stores, used by a multi-threaded server or queue worker) no longer mixes up replies or stalls: every command is one locked round trip (grav-redis 0.2.2). Uses the new releases of apogee-console, apogee-package, apogee-queue.

## 0.2.1 (2026-11-27)

### Fixes

- Database connections that the server dropped while idle (MySQL's `wait_timeout`, a restart, a proxy's idle limit) reconnect on their next use instead of failing with "server has gone away" (grav-db 0.2.1). Uses the new releases of apogee-console, apogee-package, apogee-queue, grav-db, grav-orbit.

## 0.2.0 (2026-11-25)

Needs grav 1.12.0 or newer.

- `TelescopePackage`: the module connects itself to an apogee-framework app (apogee-package):
  its settings, wiring and commands moved here from the framework.
- Its tables as a migration (`CreateTelescopeEntriesTable`, run by `migrate`); the store's own
  table-making method uses the same definition.

### Dependencies

- Uses apogee-package 0.1.0, apogee-console 0.2.2, apogee-http 0.3.0, apogee-routing 0.3.0, apogee-queue 0.3.1, grav-orbit 0.3.0, apogee-testing 0.1.1 (from their GitHub tags).

## 0.1.2 (2026-11-21)

- Recording a query no longer goes over the request's earlier entries (for
  the N+1 check): a request of 2000 queries costs Telescope 27 ms, not 280.

## 0.1.1 (2026-11-21)

- A request that ends in an `HttpException` (a 404 from the router, say) is
  recorded with that status, not as a 500 failure.

## 0.1.0 (2026-11-21)

The first release. Needs grav 1.10.1 or newer.

- `Telescope`: requests (with their batch: queries, logs, exceptions, model
  changes, dumps), queries (slow and repeated ones tagged), exceptions, log
  lines, jobs and model changes; secrets hidden; pause, filters, ignored paths.
- Memory and database stores; pruning.
- The dashboard page and its JSON API, behind a gate.
