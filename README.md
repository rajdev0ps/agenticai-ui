# Agentic AI Platform — UI

Static HTML/CSS site deployed to AWS S3 + CloudFront.
Infrastructure is managed in the [claude-devops](https://github.com/rajdev0ps/claude-devops) repo.

## Branches

| Branch | Deploys to | Auto |
|---|---|---|
| `release-dev` | DEV CloudFront distribution | Yes |
| `release-stg` | STG CloudFront distribution | Requires approval |

## Making changes

1. Edit `index.html` or `style.css` on `main`
2. Merge to `release-dev` — pipeline syncs to S3 and invalidates CloudFront
3. Verify on the DEV URL, then merge `release-dev` → `release-stg` for staging

## Pipeline

Each deploy runs two jobs:

```
push to release-dev / release-stg
       │
       ▼
[deploy]  OIDC auth → s3 sync → CloudFront invalidation
       │
       ▼
[tag]     release-{env}-SHA-{7-char-sha}
```

## GitHub setup required

Before first run, add these in **Settings → Secrets / Variables**:

| Type | Name | Value |
|---|---|---|
| Secret | `AWS_ROLE_ARN_DEV` | UI IAM role ARN for DEV (`terraform output github_actions_ui_role_arn` in claude-devops) |
| Secret | `AWS_ROLE_ARN_STG` | UI IAM role ARN for STG |
| Variable | `S3_BUCKET_NAME_DEV` | `terraform output s3_bucket_name` (DEV) |
| Variable | `S3_BUCKET_NAME_STG` | `terraform output s3_bucket_name` (STG) |
| Variable | `CLOUDFRONT_DISTRIBUTION_ID_DEV` | `terraform output cloudfront_distribution_id` (DEV) |
| Variable | `CLOUDFRONT_DISTRIBUTION_ID_STG` | `terraform output cloudfront_distribution_id` (STG) |
