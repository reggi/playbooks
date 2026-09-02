# Git commits

Use this playbook before creating a Git commit.

## Pre-flight checklist

1. Confirm the user explicitly requested a commit.
2. Run `git status --short --branch`. New repositories must use `main` as their initial and default branch. Avoid committing on an established default branch unless this is a new `reggi/*` repository where direct initial commits to `main` are appropriate.
3. Review the complete staged and unstaged diff. Preserve unrelated changes and stage only files intended for this commit.
4. Check for secrets, credentials, private identifiers, generated files, build output, and other artifacts that should not be committed.
5. If the repository uses Node.js, ensure `node_modules/` is covered by `.gitignore` before staging files.
6. Ensure the repository has a `README.md` and that its top-level heading correctly names the project.
7. Confirm the commit contains one coherent change and that directly related documentation is current.
8. Run the repository's required validation unless the user has requested remote CI instead of local tests or builds.
9. Review the final staged diff and staged file list before committing.

## Commit rules

- Never create a commit without explicit user approval.
- Do not commit directly to a default branch, except for appropriate initial work in a new `reggi/*` repository.
- Name the initial and default branch of a new repository `main`; do not use a task or setup branch as its temporary default.
- Never discard or rewrite unrelated worktree changes.
- Never amend, squash, rebase, force-push, or otherwise rewrite existing commit history, including to retrofit commits that do not follow the current message convention.
- Use [Conventional Commits 1.0.0](https://www.conventionalcommits.org/en/v1.0.0/) for all new commit messages, keeping each message concise and focused on the completed change.
- Do not claim files are committed until the commit succeeds and its contents have been inspected.
