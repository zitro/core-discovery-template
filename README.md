# core-discovery-template

A **GitHub template repository** for spinning up a new CORE Discovery
instance. CORE Discovery is a multi-customer, multi-project framework
for running discovery engagements. The framework itself ships as signed
container images on GHCR -- this repo holds **only** deploy
configuration, project content, and customizations.

## Two ways to start

### 1. Click "Use this template" on GitHub

Best for one-off setup. Click the green **Use this template** button at
the top of this repo, name your new repo (e.g. `my-core`), then:

```bash
git clone https://github.com/<you>/my-core.git
cd my-core
cp .env.example .env
# edit .env to pick LLM / storage / auth providers and set SECRET_KEY
docker compose pull
docker compose up -d
```

Open http://localhost:3000.

### 2. Use the CLI (recommended for new instances)

The CLI fills in the instance name, picks providers, and pins the right
framework version automatically.

```bash
npx create-core-discovery-app my-core
cd my-core
docker compose up -d
```

CLI source: https://www.npmjs.com/package/create-core-discovery-app

## What you get

```
.
|-- compose.yaml          # pinned to ghcr.io/zitro/core-framework-*:<version>
|-- .env.example          # provider knobs (LLM, storage, auth, secret_key)
|-- renovate.json         # grouped image bumps with auto-merge for patches
|-- projects/             # per-project Markdown content (mounted read-only)
|-- extensions/           # optional Python plugins, loaded at backend startup
|-- config/prompts/       # custom prompt overrides
`-- infra/                # placeholder for IaC (Bicep / Terraform / Pulumi)
```

## Adding a project

Drop content into `projects/<slug>/`, then register it with the running
backend:

```powershell
$body = @{ name = "My Project"; slug = "my-project"; repo_path = "my-project" } |
  ConvertTo-Json
Invoke-RestMethod -Uri http://localhost:8000/api/projects -Method Post `
  -ContentType "application/json" -Body $body
```

You can also use the in-app **Settings -> Customer** panel to attach
GitHub repositories, local clones, or plain folders as content sources
for a customer. PATs for private GitHub sources are encrypted at rest
using the symmetric `SECRET_KEY` you set in `.env`.

## Updating the framework

Renovate watches the GHCR image tags pinned in `compose.yaml` and opens
PRs when new releases ship. Patch bumps auto-merge after CI; minor and
major bumps need human review.

## Pointing at external content

`compose.yaml` mounts `./projects` by default. If your real project
content lives elsewhere (a sibling repo, a notes folder, etc.), set
`PROJECTS_SOURCE` in your `.env` and that path will be mounted instead:

```bash
# Mount a sibling content repo:
PROJECTS_SOURCE=../my-content-repo

# Or an absolute path on the host:
PROJECTS_SOURCE=/path/to/content
```

## Links

- Framework source: https://github.com/zitro/core-framework
- CLI scaffolder:   https://www.npmjs.com/package/create-core-discovery-app
- Container images: https://github.com/zitro?tab=packages&q=core-framework
- Changelog:        https://github.com/zitro/core-framework/blob/master/CHANGELOG.md
