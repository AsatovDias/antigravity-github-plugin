# GitHub Rules

When performing Git or GitHub operations on behalf of the user:

1. **Tool Resolution on Windows**:
   - If `git` or `gh` is not yet on the session `PATH` (for example, right after installing via `winget`), check their standard installation paths:
     - Git: `"$env:LOCALAPPDATA\Microsoft\WinGet\Links\git.exe"` or `"C:\Program Files\Git\cmd\git.exe"`
     - GitHub CLI: `"C:\Program Files\GitHub CLI\gh.exe"`
2. **Authentication Verification**:
   - Before attempting operations that require GitHub authentication (creating repositories, pushing code, opening PRs), verify authentication with `gh auth status`.
   - If the user is not signed in, guide them through `gh auth login` (or use a Personal Access Token if they provide one).
3. **Safe Repository Creation & Pushing**:
   - Never commit `.env` files, credentials, API keys, or Personal Access Tokens to a repository.
   - Always create a `.gitignore` appropriate for the project stack before staging all files (`git add .`) in a new repository.
   - Unless the user specifies otherwise, ask whether a newly created repository should be **public** or **private**.
