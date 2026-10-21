# Survey Claim App — deployment setup

## Current repository status

This repository currently contains GitHub Actions deployment templates, not a working survey application. The root does not yet contain application source code, a `package.json`, a `Dockerfile`, or an ECS task definition. Consequently, the deployment workflows are examples that require project code and provider configuration before they can deploy a functional app.

**No survey-taking, survey-claiming, rewards, account, or payment functionality is implemented by these workflow files.** Do not enter survey-site passwords, reward account credentials, or payment details into GitHub Actions or a future app until the application architecture and data handling are reviewed.

## Workflow inventory

| Workflow | Trigger | Purpose | Required setup / current blocker |
|---|---|---|---|
| `.github/workflows/aws.yml` | Push to `main` | Build a container, push to ECR, deploy to ECS | Placeholder region/resource names, AWS credentials, task definition, and a root `Dockerfile` are required |
| `.github/workflows/azure-webapps-node.yml` | Push to `main` or manual | Build/test and deploy a Node app to Azure | Requires `package.json`, build/test scripts as appropriate, Azure app name, and publish-profile secret |
| `.github/workflows/openshift.yml` | Push to `main` or manual | Build and deploy a container to OpenShift | Requires a root `Dockerfile`, cluster secrets, namespace/port configuration, and trusted TLS |
| `.github/workflows/manual.yml` | Manual | Print a greeting | Demonstration only; does not deploy an app |

Avoid enabling multiple provider workflows on the default branch until the intended hosting platform has been selected and configured. Otherwise a push can trigger several incomplete deployment attempts.

## Safe setup checklist

1. Choose one hosting target: AWS ECS, Azure App Service, or OpenShift.
2. Add the actual application and its documented build/test commands.
3. Add only the selected provider's required configuration and least-privilege credentials.
4. Prefer GitHub OIDC/federated identity over long-lived cloud access keys when the provider supports it.
5. Keep credentials in GitHub Actions secrets or an external secret manager; never commit them to source files.
6. Require TLS certificate verification for production connections. Install the correct trusted CA certificate if your cluster uses a private CA; do not disable verification.
7. Test on a non-production environment before enabling deployment on pushes to `main`.
8. Confirm health checks and rollback procedures before calling a deployment successful.

## Security notes

- Workflow input is untrusted data. The manual greeting workflow passes the supplied name through an environment variable instead of interpolating it directly into shell source.
- OpenShift login requires TLS certificate validation.
- Deployment workflows can publish images or expose services publicly. Review permissions, network exposure, costs, and provider configuration before running them.
- This repository does not automate survey answers, bypass eligibility checks, fabricate survey responses, or guarantee rewards.

## Next development step

Define the intended app before writing application code: target platform, user-facing features, data collected, privacy policy, and how users will review any suggested answers. Then add the smallest working app, automated tests, a health endpoint, and one configured deployment workflow.
