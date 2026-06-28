# AGENTS.md

Guidance for Codex and other coding agents working in this repository.

## Project

- This is a simple Python Flask app intended for containerized deployment to AWS ECS.
- The app entrypoint is `app.py`.
- Tests live in `tests/test_app.py`.
- Dependencies are listed in `requirements.txt`.
- A `Dockerfile` is present for image builds.

## Local Validation

Use the existing test flow before changing deploy behavior or app routes:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
pytest -q
```

If dependencies are already installed, `pytest -q` is enough.

## CI/CD

- Primary workflow: `.github/workflows/deploy.yml`.
- The `validate` job runs Python tests with pytest.
- The `deploy` job builds and pushes Docker images to ECR, then updates the ECS service.
- Keep `environment: dev` on the deploy job so GitHub environment variables are available.
- The workflow uses GitHub OIDC to assume an AWS role via the `AWS_ROLE_TO_ASSUME` secret.

## AWS Deployment Notes

- ECR image tags use two forms:
  - Immutable tag: `dev-<sha>`.
  - Moving alias: `dev-latest`.
- ECS task definition image references must use repository/tag syntax, for example:

```text
<account>.dkr.ecr.<region>.amazonaws.com/<repo>:dev-latest
```

- Do not use a slash in place of the tag separator, such as `<repo>/dev-latest`.
- If ECS tasks run in private subnets, they need outbound access through NAT or VPC endpoints for ECR and S3.

## Preferred Future Direction

The current workflow updates the ECS service with `--force-new-deployment` after pushing `dev-latest`.

Preferred improvement: register a new ECS task definition revision using the immutable `dev-<sha>` image, then update the service to that revision. This makes deployments more auditable and avoids relying only on a moving tag.

## Private Local Context

Detailed imported project memory may exist locally under `.codex/`. That folder is intentionally ignored by Git and should not be committed.
