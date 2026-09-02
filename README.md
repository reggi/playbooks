# brain

This repository is a collection of small, high-value prompt files that describe how I want LLMs and agents to work across projects.

The goal is consistency without loading a large instruction set into every conversation. Start with the smallest relevant file and follow links only when the task requires more context.

## Usage

Reference the relevant playbook directly:

> I want to deploy an instance of Vikunja on Railway. Follow `https://github.com/<owner>/<repository>/blob/main/self-host.md`.

Agents should:

1. Treat the referenced file as the default approach for the task.
2. Read only the additional files it explicitly points to.
3. Inspect the target repository before changing it.
4. Preserve existing project conventions unless the playbook explicitly overrides them.
5. Ask before deploying, applying infrastructure changes, publishing, or performing destructive operations.

## Playbooks

| File | Scope |
| --- | --- |
| [`self-host.md`](self-host.md) | Selecting, verifying, and operating open-source self-hosted software |
| [`railway.md`](railway.md) | Railway infrastructure using `.railway/railway.ts` |
| [`static-html.md`](static-html.md) | Single-file static HTML generation |
| [`typescript.md`](typescript.md) | TypeScript project organization and defaults |
| [`html-ui.md`](html-ui.md) | Consistent HTML and UI design across repositories |
| [`image-editing.md`](image-editing.md) | Image editing and UI tooling preferences |
| [`openscad.md`](openscad.md) | Parametric 3D-printing work with OpenSCAD |
| [`local-machine.md`](local-machine.md) | Local machine management with NixOS and Nixpacks |
| [`lean-specs.md`](lean-specs.md) | Using Lean proofs to specify application behavior and state |

## Writing rules

- Keep each file short enough to use as prompt context.
- State decisions and defaults, not broad tutorials.
- Prefer explicit constraints over lists of possible approaches.
- Add examples only when they remove ambiguity.
- Update earlier guidance when new information changes the preferred approach.
