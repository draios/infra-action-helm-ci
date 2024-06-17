# action-helm-ci

GitHub Action for handle CI of helm charts in a repo
This is a `composite` action leveraging the code of [draios/action-helm-tools](github.com/draios/action-helm-tools)

**WARNING** You need an explicit checkout before calling this action

## Inputs

## Required

- `chart_dir`: path to chart directory. Example: k8s/redis
- `artifactory_password`: Artifactory Password
- `github_token`:  GITHUB_TOKEN secret

## Optional

- `dry_run`: whether to actually create git tag and push the chart
- `artifactory_url`: Artifactory repo URL (default: "https://artifactory.internal.sysdig.com:443/artifactory/helm-local/)
- `artifactory_username`: Artifcatory Username

## Outputs

- `helm_chart_new_version`: The version of the bumped Helm chart

## Example workflow

Perform all checks on pull requests

```yaml
name: publish helm chart - redis

on:

  pull_request:
    types: [opened, edited, synchronize, reopened]
    paths:
      - 'k8s/redis/**'

  push:
    branches: ["main"]
    paths:
      - 'k8s/redis/**'

jobs:
  publish:
    runs-on: self-hosted
    steps:
    - uses: actions/checkout@v2
    - uses: draios/infra-action-helm-ci@v1
      with:
        chart_dir: k8s/redis
        dry_run: ${{ ! (github.event_name == 'push' && github.ref == 'refs/heads/main') }}
        artifactory_username: ${{ secrets.ARTIFACTORY_HELM_USERNAME }}
        artifactory_password: ${{ secrets.ARTIFACTORY_HELM_PASSWORD }}
        github_token: ${{ secrets.GITHUB_TOKEN }}

```
