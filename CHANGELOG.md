# Changelog

Every release of apogee-telescope, newest first. Each is a `vX.Y.Z` tag with a GitHub
release carrying its section; the `grav` line in `grav.toml` says which
compiler versions it builds with.

## Unreleased

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
