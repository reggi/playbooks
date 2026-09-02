# GitHub repositories

Use this playbook when creating a GitHub repository or publishing an existing
local repository.

## Approval

- Require an explicit request before creating a repository, changing its
  visibility, or pushing local commits.
- Confirm the requested owner, repository name, and visibility before acting.
- Never make a repository public merely because its contents appear safe.

## Publishing

1. Inspect the local branch, commits, worktree, and configured remotes.
2. Follow [`git-commit.md`](git-commit.md) if a commit is required.
3. Check tracked files and commit history for secrets, credentials, private
   identifiers, personal domains, generated output, and oversized artifacts.
4. Create or update the repository with the explicitly requested visibility.
5. Use `main` as the default branch for a new repository.
6. Add the intended remote without replacing an unrelated existing remote.
7. Push only the commits and branches the user requested.

## Verification

After publishing, read back and report:

- repository owner and name;
- public or private visibility;
- repository URL;
- default branch;
- local remote URL and upstream branch;
- clean or remaining local worktree changes.

Do not claim the repository is created, public, private, or pushed until those
properties have been verified from GitHub.
