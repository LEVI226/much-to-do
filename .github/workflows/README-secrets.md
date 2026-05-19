# GitHub Actions Secrets

Set these in `LEVI226/much-to-do` → Settings → Secrets and variables → Actions.

All values come from `terraform output` after deploying the infra repo.

## Required Secrets

| Secret | Source | Used by |
|:---|:---|:---|
| `WIF_PROVIDER` | `terraform output -raw wif_provider` | Both workflows (WIF auth) |
| `DEPLOYER_SA_EMAIL` | `terraform output -raw github_deployer_service_account` | Both workflows (WIF auth) |
| `GCP_PROJECT_ID` | `project-3af03b27-5270-4519-8d1` | Backend deploy |
| `GCP_REGION` | `us-central1` | Backend deploy |
| `MIG_NAME` | `terraform output -raw mig_name` | Backend deploy |
| `VITE_API_BASE_URL` | `terraform output -raw backend_https_url` | Frontend build + backend health check |
| `FIREBASE_PROJECT_ID` | `project-3af03b27-5270-4519-8d1` | Frontend deploy |

> **Important:** `VITE_API_BASE_URL` must be the **HTTPS** URL (`https://api.<ip>.sslip.io`),
> not the HTTP IP. The frontend is served from Firebase Hosting over HTTPS; browsers block
> mixed-content HTTP calls from HTTPS pages.

## How to set all at once (after terraform apply)

```bash
cd much-to-do-infra

gh secret set WIF_PROVIDER \
  --body "$(terraform output -raw wif_provider)" \
  --repo LEVI226/much-to-do

gh secret set DEPLOYER_SA_EMAIL \
  --body "$(terraform output -raw github_deployer_service_account)" \
  --repo LEVI226/much-to-do

gh secret set GCP_PROJECT_ID \
  --body "project-3af03b27-5270-4519-8d1" \
  --repo LEVI226/much-to-do

gh secret set GCP_REGION \
  --body "us-central1" \
  --repo LEVI226/much-to-do

gh secret set MIG_NAME \
  --body "$(terraform output -raw mig_name)" \
  --repo LEVI226/much-to-do

gh secret set VITE_API_BASE_URL \
  --body "$(terraform output -raw backend_https_url)" \
  --repo LEVI226/much-to-do

gh secret set FIREBASE_PROJECT_ID \
  --body "project-3af03b27-5270-4519-8d1" \
  --repo LEVI226/much-to-do
```

## Notes

- Workflows use **Workload Identity Federation** — no JSON service account keys in GitHub
- `id-token: write` permission in each workflow grants OIDC token generation
- The WIF pool restricts to this repo only (`LEVI226/much-to-do`) via `attribute_condition`
- Old AWS secrets (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, `EC2_HOST_1`, etc.) should be removed
