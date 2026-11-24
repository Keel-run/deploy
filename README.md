# Deploy to keel.run

Deploys a [keel.run](https://keel.run) service from GitHub Actions and waits until it is live. The build log streams into the job; the step fails when the deployment fails.

```yaml
name: Deploy
on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v5
      - uses: keel-run/deploy@v1
        id: keel
        with:
          token: ${{ secrets.KEEL_TOKEN }}
          project: shop
          service: web
      - run: echo "Live at ${{ steps.keel.outputs.url }}"
```

Create the token with `keel tokens create --agent --scope deploy --project shop` (or in the dashboard under API tokens) and store it as the repository secret `KEEL_TOKEN`. Limit it to the project, and give it an expiry.

## Inputs

| Input | Default | |
|---|---|---|
| `token` | – | API token with the `deploy` scope (required) |
| `project` | – | Project name or ID (required) |
| `service` | – | Service name or ID (required) |
| `environment` | `production` | Environment name or ID |
| `source` | `upload` | `upload` deploys the checked-out files (`directory`); `git` builds the service's branch head or `commit` on keel.run |
| `directory` | `.` | Directory to upload |
| `commit` | – | Commit to deploy with `source: git` |
| `timeout` | `30m` | How long to wait for the deployment |
| `workspace` | – | Only for tokens that reach several workspaces |
| `cli-version` | latest | keel CLI release |
| `api-url` | `https://api.keel.run` | |

## Outputs

`deployment-id`, `status` (`active` when it worked) and `url` (the service's public URL, for web services). The URL also goes to the job summary.

Services from a container image deploy their image again; for those, `source` does not matter.
