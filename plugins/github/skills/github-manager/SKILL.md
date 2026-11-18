---
name: github-manager
description: Create and manage GitHub repositories, authenticate the GitHub CLI (gh) and Git, push commits, manage branches, open pull requests, and manage issues. Activate this skill whenever the user asks to connect their GitHub account, create a GitHub repo, push a project to GitHub, clone a repo, or manage PRs/issues.
---

# GitHub Manager Skill

This skill provides end-to-end workflows for managing GitHub repositories, authentication, commits, pull requests, and issues on Windows.

## 1. Prerequisites & Installation Check

Before running Git or GitHub commands, check if `git` and `gh` are available:

```powershell
$gitCmd = if (Get-Command git -ErrorAction SilentlyContinue) { "git" } elseif (Test-Path "C:\Program Files\Git\cmd\git.exe") { "C:\Program Files\Git\cmd\git.exe" } else { $null }
$ghCmd  = if (Get-Command gh -ErrorAction SilentlyContinue)  { "gh" }  elseif (Test-Path "C:\Program Files\GitHub CLI\gh.exe")  { "C:\Program Files\GitHub CLI\gh.exe" }  else { $null }
```

If either tool is missing, you can install them via `winget`:

```powershell
winget install --id Git.Git -e --source winget --accept-package-agreements --accept-source-agreements
winget install --id GitHub.cli -e --source winget --accept-package-agreements --accept-source-agreements
```

> **Note:** After installing via `winget`, use the full executable paths (`"C:\Program Files\Git\cmd\git.exe"` and `"C:\Program Files\GitHub CLI\gh.exe"`) or prepend their folders to `$env:PATH` in each PowerShell command.

---

## 2. Connecting / Authenticating a GitHub Account

Check authentication status first:

```powershell
& "C:\Program Files\GitHub CLI\gh.exe" auth status
```

### Option A: Browser Device Flow (`gh auth login`)
Run:
```powershell
& "C:\Program Files\GitHub CLI\gh.exe" auth login --hostname github.com --git-protocol https --web
```
This outputs a one-time code and opens `https://github.com/login/device` where the user can authorize their account.

### Option B: Token Authentication
If the user provides a Personal Access Token (classic `ghp_...` with `repo` and `workflow` scopes, or fine-grained token):
```powershell
"<TOKEN>" | & "C:\Program Files\GitHub CLI\gh.exe" auth login --with-token
& "C:\Program Files\GitHub CLI\gh.exe" auth setup-git
```

Also ensure Git `user.name` and `user.email` are configured:
```powershell
$ghUser = (& "C:\Program Files\GitHub CLI\gh.exe" api user | ConvertFrom-Json)
& "C:\Program Files\Git\cmd\git.exe" config --global user.name $ghUser.login
if ($ghUser.email) {
  & "C:\Program Files\Git\cmd\git.exe" config --global user.email $ghUser.email
} else {
  & "C:\Program Files\Git\cmd\git.exe" config --global user.email "$($ghUser.id)+$($ghUser.login)@users.noreply.github.com"
}
```

---

## 3. Creating a New Repository & Pushing Code

To create a brand new repository on GitHub and push a local project directory:

1. **Initialize Local Git Repo (if not already initialized)**:
   ```powershell
   Set-Location "<project-path>"
   & "C:\Program Files\Git\cmd\git.exe" init -b main
   ```
2. **Ensure `.gitignore` Exists**:
   Create a `.gitignore` tailored to the project before staging files so secrets and build artifacts (`node_modules/`, `.env`, `__pycache__/`, `dist/`) are never committed.
3. **Create Initial Commit**:
   ```powershell
   & "C:\Program Files\Git\cmd\git.exe" add .
   & "C:\Program Files\Git\cmd\git.exe" commit -m "Initial commit"
   ```
4. **Create Remote Repository and Push in One Step**:
   - For a **private** repository:
     ```powershell
     & "C:\Program Files\GitHub CLI\gh.exe" repo create "<repo-name>" --private --source=. --remote=origin --push
     ```
   - For a **public** repository:
     ```powershell
     & "C:\Program Files\GitHub CLI\gh.exe" repo create "<repo-name>" --public --source=. --remote=origin --push
     ```
   - With a description:
     Add `--description "<description>"`.

---

## 4. Common Repository & PR Workflows

- **List Repositories**:
  `& "C:\Program Files\GitHub CLI\gh.exe" repo list --limit 20`
- **Clone a Repository**:
  `& "C:\Program Files\GitHub CLI\gh.exe" repo clone <owner>/<repo>`
- **Create a Pull Request**:
  `& "C:\Program Files\GitHub CLI\gh.exe" pr create --title "<title>" --body "<body>" --base main`
- **List / View Issues**:
  `& "C:\Program Files\GitHub CLI\gh.exe" issue list`
  `& "C:\Program Files\GitHub CLI\gh.exe" issue create --title "<title>" --body "<body>"`
