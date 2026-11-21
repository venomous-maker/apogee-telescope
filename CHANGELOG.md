# Changelog

Every release of apogee-telescope, newest first. Each is a `vX.Y.Z` tag with a GitHub
release carrying its section; the `grav` line in `grav.toml` says which
compiler versions it builds with.

## Unreleased

The first release. Needs grav 1.10.1 or newer.

- `Telescope`: requests (with their batch: queries, logs, exceptions, model
  changes, dumps), queries (slow and repeated ones tagged), exceptions, log
  lines, jobs and model changes; secrets hidden; pause, filters, ignored paths.
- Memory and database stores; pruning.
- The dashboard page and its JSON API, behind a gate.
