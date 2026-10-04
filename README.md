# .github (org-level)

Not a code repo. GitHub treats an org's `.github` repository specially:

- `.github/ISSUE_TEMPLATE/*` and `.github/PULL_REQUEST_TEMPLATE.md` become the **default
  templates for every repo** in the org that doesn't define its own.
- `CONTRIBUTING.md` becomes the default contributing guide.
- `.github/workflows/contract-health.yml` is a **reusable workflow** that each backend's CI calls.

> `architecture.md` sketches these under `templates/`. GitHub only picks up org-default templates
> from the root, `.github/` or `docs/`, so they live in `.github/` here.

## Setup checklist

1. Create the repo as `capstone-hpsi-2/.github`. The org name is referenced in the callers'
   `uses:` lines, in `CODEOWNERS`, and in `contracts.lock.json`. Update all three if it changes.
2. If the repo is **private**: Settings → Actions → General → *Access* → "Accessible from
   repositories in the organization", or callers can't use the reusable workflow.
3. Tag a release: `git tag v1 && git push origin v1`. Callers pin `@v1`. For non-breaking
   updates, move the tag (`git tag -f v1 && git push -f origin v1`). For breaking ones, cut `v2`.

## Reusable workflow: `contract-health.yml`

Builds the caller's image, starts it with only the given env, and asserts that
`GET <health-path>` returns 200 with `{"status":"ok","service":<service>,"version":<non-empty>}`.

| Input | Default | |
|---|---|---|
| `service` | (required) | expected `.service` |
| `health-path` | (required) | e.g. `/api/health`, `/ai/health` |
| `port` | `8000` | container port |
| `context` | `.` | build context |
| `docker-target` | `""` | multi-stage target |
| `env` | `""` | `KEY=VALUE` lines |
| `timeout-seconds` | `60` | |
