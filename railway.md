# Railway

Use Railway's TypeScript Infrastructure-as-Code system for self-hosted deployments:

```text
.railway/railway.ts
```

The Git repository is the infrastructure source of truth. Do not use Railway one-click templates, imported community recipes, or an already-configured dashboard project as the intended configuration.

## Repository naming

Name repositories that primarily define a Railway deployment:

```text
railway-<application>
```

For example:

- `railway-plausible`
- `railway-vikunja`

Use the application's recognizable project name after the prefix. Keep this repository name even when the deployment includes supporting databases, caches, workers, or other services.

## Portability

Each repository must support both:

- deploying a personal instance for the repository owner;
- being cloned and deployed independently by anyone else.

The committed infrastructure must not depend on an existing Railway project, account, workspace, environment, domain, or resource identifier. Do not hard-code personal domains, usernames, email addresses, account details, generated Railway URLs, or other owner-specific values.

Make instance-specific values configurable and document how each deployer supplies them. A new user should be able to create a fresh Railway project from the repository without inheriting access to, references to, or assumptions about the original author's infrastructure.

Validate user-provided domains before using them in infrastructure. Domain
inputs should contain only the hostname expected by Railway, without a URL
scheme, path, or whitespace. Reject unchanged example placeholders during
planning rather than attempting to provision them. Derive related values such
as an application's HTTPS base URL from the validated hostname instead of
requiring duplicate configuration.

## Resource policy

Design self-hosted projects for Railway's Hobby plan and low traffic by default.

- Use the minimum supported CPU, memory, replica count, and persistent storage that can run the application reliably.
- Start each service with one replica unless the application requires otherwise.
- Enable application sleeping when it is compatible with the workload and does not risk data integrity.
- Do not add high availability, multi-region placement, redundant databases, or speculative scaling.
- Keep optional workers, caches, search services, and observability services out of the default deployment unless the application requires them.
- Size volumes conservatively and document how to expand them.
- Increase resources only when upstream minimum requirements, observed usage, or a measured reliability issue justify the change.

Document any component that is likely to exceed Hobby-plan limits or cannot safely use the minimum configuration.

## Required approach

1. Model the whole project in `.railway/railway.ts` with `railway/iac`.
2. Use product-level helpers such as `project()`, `service()`, `image()`, `postgres()`, `redis()`, `volume()`, `bucket()`, and `group()`.
3. Pin application and supporting-service images to explicit versions.
4. Express relationships through resource environment references rather than copied URLs or identifiers.
5. Keep secrets out of source. Use `preserve()` for an existing unknown secret when appropriate.
6. Return every managed resource from the project definition.
7. Run `railway config plan` after editing the infrastructure.
8. Apply the plan only after explicit user approval.

Do not add Railway UUIDs, generated Railway domains, encrypted values, platform internals, or unnecessary defaults to the file.

## Example shape

```ts
import {
  defineRailway,
  image,
  postgres,
  preserve,
  project,
  service,
  volume,
} from "railway/iac";

export default defineRailway(() => {
  const database = postgres("postgres");
  const data = volume("app-data", {
    sizeMB: 1_024,
  });

  const app = service("app", {
    source: image("ghcr.io/example/app:1.2.3"),
    healthcheck: "/health",
    env: {
      DATABASE_URL: database.env.DATABASE_URL,
      APP_SECRET: preserve(),
      PORT: "3000",
    },
    volumeMounts: {
      "/app/data": data,
    },
  });

  return project("railway-example-app", {
    resources: [database, data, app],
  });
});
```

Adapt this shape to the application's official deployment documentation. Do not assume its ports, paths, variables, database requirements, or health endpoint.

## README requirements

Document every environment variable, including variables represented by resource references or `preserve()`.

For each variable, record:

| Field | Meaning |
| --- | --- |
| Name | Exact environment variable name |
| Service | Service that receives it |
| Required | Whether the application requires it |
| Secret | Whether its value must remain sealed |
| Source | Literal configuration, Railway resource reference, or user-provided secret |
| Purpose | What behavior it controls |
| Example | A safe non-secret example, when useful |

Also document:

- the pinned version of every image;
- service and dependency architecture;
- persistent volumes and mount paths;
- public ports, domains, and health checks;
- backup and restore expectations;
- upgrade procedure and migration requirements;
- `railway config plan` and the explicit approval required before `railway config apply`.

An `.env.example` may list user-supplied variable names with safe placeholders. It must not contain generated credentials, Railway resource values, or real secrets.

## Secret hygiene

- Never commit passwords, tokens, private keys, API keys, signing secrets, database credentials, or secret-bearing URLs.
- Never include real secrets in examples, documentation, screenshots, fixtures, plan output, logs, or generated files.
- Use Railway resource references for generated connection values.
- Use `preserve()` only to retain a secret already stored in Railway; do not use it to conceal an undocumented requirement.
- Require each deployer to provide their own external-service credentials and application secrets.
- Prefer generating secrets during setup when Railway or the application supports it. Otherwise document a local generation command that does not print or transmit the resulting value unnecessarily.
- Keep `.env` and other local secret files ignored. Commit only `.env.example` with empty values or unmistakably non-secret placeholders.
- Before publishing changes, inspect tracked files and the staged diff for accidental credentials and owner-specific values.

## Ownership rules

- Do not manage the same service with both `.railway/railway.ts` and `railway.json`.
- Prefer `.railway/railway.ts` even when the application itself is not written in TypeScript.
- Use `railway.json` only when TypeScript IaC cannot represent a required service-level setting.
- If a fallback is necessary, explain the gap and keep ownership boundaries explicit.

## Agent safety

- Planning is allowed when requested as part of authoring or reviewing the infrastructure.
- Applying, deploying, adding domains, or changing live variables requires an explicit request.
- Never use automatic destructive confirmation.
- Never expose secret values merely to make a plan easier to inspect.
- A queued build is not a successful deployment. Verify terminal deployment state and service health before reporting success.
