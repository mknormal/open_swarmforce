# SWARMFORGE — Attribution and Notices

SWARMFORGE is a fork of **OpenSwarm** (https://github.com/Intrect-io/OpenSwarm),
an agentic CI/CD gate created and maintained by the OpenSwarm project and its
contributors.

## Upstream license

The upstream project is licensed under the MIT License. Its copyright notice
and license text are preserved unmodified in [`LICENSE`](LICENSE):

> Copyright (c) 2026 Heewon Oh

The v1 fork keeps the upstream engine — CLI, daemon, agent pipeline, provider
adapters, task state, and execution infrastructure — intact. Only user-facing
branding, dashboard presentation, metadata, and documentation differ. Large
portions of the source and documentation remain upstream work.

## No endorsement

SWARMFORGE is an independent fork. It is not affiliated with, sponsored by, or
endorsed by the OpenSwarm project, Intrect-io, or the upstream maintainers.
"OpenSwarm" is used here only to identify the upstream project this fork is
derived from.

## Third-party components

Dependencies keep their own licenses; see each package's LICENSE file after
install. The vendored Inter font under `web/static/fonts/` is licensed under
the SIL Open Font License (see `web/static/fonts/LICENSE.inter`).

## Notes for redistribution

- Do not remove this file or the upstream `LICENSE`.
- Branding must not imply upstream endorsement.
