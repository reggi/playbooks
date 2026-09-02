# Self-hosted software

Use this playbook when installing or deploying an open-source application.

## Goal

Produce a reproducible, reviewable deployment whose configuration lives in source control. Do not rely on mutable third-party deployment recipes as infrastructure source of truth.

The result must work for both a personal instance and an independent deployment by another user. Keep account-specific state, private identifiers, personal domains, and secrets out of the reusable repository.

## Required process

1. Confirm the requested software is open source and identify its official repository and documentation.
2. Determine the requested or current stable version. Pin container images and important dependencies to explicit versions rather than `latest`.
3. Read the upstream installation, configuration, persistence, backup, upgrade, and health-check documentation.
4. Identify every required service, database, cache, volume, port, domain, and environment variable.
5. Implement the deployment using the platform-specific source-controlled infrastructure playbook.
6. Document all configuration and environment variables in the deploying repository's README.
7. Plan and review infrastructure changes before applying them.

For Railway, follow [`railway.md`](railway.md).

## Trust policy

- Do not search for or use Railway one-click templates.
- Do not treat marketplace recipes, dashboard state, blog posts, or copied deployment configurations as authoritative.
- A third-party publisher can change a template after it has been inspected. Recreate the required infrastructure from pinned upstream sources instead.
- Prefer the application's official documentation and source repository.
- Verify container ownership, version, architecture support, exposed ports, persistent paths, and health endpoints.
- Never copy unknown secrets or opaque environment values into source control.

## Repository output

A self-hosted deployment should normally include:

- source-controlled infrastructure configuration;
- a README describing architecture, versions, variables, secrets, persistence, backups, upgrades, and deployment steps;
- an example environment file containing names and safe placeholders only;
- explicit health checks where the application supports them.

A new user should be able to clone the repository, provide their own configuration and secrets, and deploy a separate instance without access to the original author's infrastructure.

## Safety

- Do not deploy or apply infrastructure changes unless explicitly requested.
- Surface destructive plan changes before applying them.
- Preserve existing secret values rather than replacing them with placeholders.
- Do not claim success until the deployed services are healthy.
