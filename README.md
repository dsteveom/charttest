# charttest Helm Charts

This repository hosts Helm charts released via GitHub Pages using the `helm/chart-releaser-action`.

## Repository Layout

- `helm-charts/` : Source charts (each directory is one chart)
- `.github/workflows/release-charts.yml` : Release workflow
- `cr.yaml` : Chart Releaser configuration

## How Releases Work

1. You bump the `version` field in `Chart.yaml` of a chart under `helm-charts/<chartName>`.
2. Commit and push to `main` (the workflow is triggered only when paths under `helm-charts/**` change or when run manually).
3. The GitHub Action packages changed charts and creates GitHub Releases (one per chart version) attaching the `.tgz` artifact(s).
4. The action regenerates/updates `index.yaml` in the `gh-pages` branch which backs the public Helm repo.

## First-Time Setup (manual once)

Run these steps once after merging the workflow:

```bash
# Create the gh-pages branch (empty orphan) if it does not exist
git checkout --orphan gh-pages
rm -rf *
echo "Helm chart index will be published here" > README.md
git add README.md
git commit -m "Initialize gh-pages for Helm index"
git push origin gh-pages
# Return to main
git checkout main
```

Then in GitHub repository settings:
- Settings > Pages > Build and deployment: Select `Deploy from a branch`
- Branch: `gh-pages` / root (`/`)

After the first successful workflow run, `index.yaml` will appear on `gh-pages`.

## Adding a New Chart

1. Create a directory `helm-charts/<newchart>`.
2. Add `Chart.yaml`, `values.yaml`, `templates/` etc.
3. Commit and push (ensure `Chart.yaml` has a unique `version` not previously released for that chart).

## Consuming the Charts

Once `gh-pages` is published (replace USER/REPO):

```bash
helm repo add charttest https://dsteveom.github.io/charttest
helm repo update
helm search repo charttest
```

## Releasing Updates

- Increment the `version` in the chart's `Chart.yaml` (SemVer). The action will skip if the version already exists.
- Optionally update `appVersion` to reflect upstream application changes.

## Troubleshooting

| Symptom | Cause | Fix |
|---------|-------|-----|
| Workflow succeeds but index.yaml missing | gh-pages branch not initialized | Follow first-time setup steps |
| Release not created | Version unchanged or already released | Bump `version` in Chart.yaml |
| 404 when adding repo | Pages not configured | Enable GitHub Pages for gh-pages branch |

## Security / Tokens

`CR_TOKEN` uses the default `GITHUB_TOKEN` with `contents: write` permission. No PAT is required unless you need cross-repo writes.

---
Happy charting!
