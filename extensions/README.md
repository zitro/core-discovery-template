# extensions

Drop `*.py` plugin modules in this directory. Each module exposes a
`register(app, settings)` function called at backend startup, which
gives you a hook to register routers, agents, providers, or extra
endpoints without modifying core source.

The directory is mounted read-only into the backend container at
`/data/extensions`.

See the framework docs for the full extension contract:
https://github.com/zitro/core-framework/blob/master/docs/EXTENSIONS.md