# LikeC4 Federation Registry

Shared federated registry for the SaaS platform demo. This repository is populated by running `likec4 federation publish` from provider repos.

## Scenario

Three teams own independent architecture models that share elements via federation:

| Repo | Role | Exports | Depends On |
|------|------|---------|------------|
| [likec4-demo-auth](../likec4-demo-auth) | Provider | `auth` | — |
| [likec4-demo-billing](../likec4-demo-billing) | Provider + Consumer | `billing` | `auth` |
| [likec4-demo-dashboard](../likec4-demo-dashboard) | Consumer | — | `auth`, `billing` |

## Re-generating manifests

Requires the `likec4` CLI (built from the monorepo since federation isn't published to npm yet):

```bash
# From the likec4 monorepo
node packages/likec4/dist/cli/index.mjs federation publish ../likec4-demo-auth
node packages/likec4/dist/cli/index.mjs federation publish ../likec4-demo-billing
node packages/likec4/dist/cli/index.mjs federation sync ../likec4-demo-billing
node packages/likec4/dist/cli/index.mjs federation sync ../likec4-demo-dashboard
```

## Testing a breaking change

1. In `likec4-demo-auth/model.c4`, rename `auth.api` to `auth.gateway`
2. Run the composition check (dry-run):
   ```bash
   node packages/likec4/dist/cli/index.mjs federation check ../likec4-demo-auth
   ```
3. The check will **fail** because `billing` and `dashboard` depend on `auth.api`
4. Fix the consumers or revert the change before publishing

## Registry structure

```
likec4-demo-registry/
  registry.json          # Provider/consumer index
  auth/manifest.json     # Auth service manifest (elements, relations, spec)
  billing/manifest.json  # Billing service manifest
```
