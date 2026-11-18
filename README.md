# Connecting Your GitHub Account to Google Antigravity

A complete guide and ready-to-use **GitHub Plugin** for [Google Antigravity](https://antigravity.google) that lets the agent create repositories, push commits, manage branches, open pull requests, and manage issues directly from chat.

---

## Ways to Connect GitHub to Antigravity

### Method 1: GitHub CLI (`gh`) + Git + Custom Antigravity Plugin (Recommended)

This repository includes a ready-to-install Antigravity plugin (`plugins/github/`) that bundles:
- **`github-manager` Skill** (`skills/github-manager/SKILL.md`): Teaches the agent end-to-end GitHub workflows (`gh repo create`, `gh pr create`, `gh issue`, credential setup).
- **Safety Rules** (`rules/AGENTS.md`): Ensures `.gitignore` is always created, secrets are never committed, and Windows binary paths are resolved automatically.
- **Plugin Manifest & Logo** (`plugin.json` & `assets/logo.svg`): Appears directly in your **Skills & Customizations** panel so you can toggle it on or off anytime.

#### Step 1: Install Git & GitHub CLI (`gh`)

On Windows (using `winget`):

```powershell
winget install --id GitHub.cli -e --source winget
winget install --id Git.MinGit -e --source winget
```

#### Step 2: Authenticate Your GitHub Account

Run the interactive web login flow:

```powershell
gh auth login --hostname github.com --git-protocol https --web
gh auth setup-git
```

1. Copy the one-time device code shown in the terminal.
2. Open **https://github.com/login/device** in your browser.
3. Paste the code and click **Authorize GitHub CLI**.

Configure your Git identity:

```powershell
$ghUser = (gh api user | ConvertFrom-Json)
git config --global user.name $ghUser.login
git config --global user.email "$($ghUser.id)+$($ghUser.login)@users.noreply.github.com"
```

#### Step 3: Install the Antigravity GitHub Plugin

Copy the `plugins/github` folder from this repository into your global Antigravity plugins directory:

```powershell
Copy-Item -Path ".\plugins\github" -Destination "$HOME\.gemini\config\plugins\github" -Recurse -Force
```

Once copied, the **GitHub** plugin automatically loads in Antigravity under **Skills & Customizations → Plugins**.

---

### Method 2: GitHub MCP Server (`mcp_config.json`)

If you prefer exposing GitHub operations as Model Context Protocol (MCP) tools using a Personal Access Token (PAT), add the GitHub server to `~/.gemini/config/mcp_config.json`:

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "ghp_your_personal_access_token_here"
      }
    }
  }
}
```

> **Security Tip:** Never commit your `mcp_config.json` or Personal Access Tokens to version control.

---

## Example Prompts in Antigravity

Once connected, you can ask Antigravity directly in chat:

- *"Create a new GitHub repository for my project and push the initial commit"*
- *"Check my GitHub authentication status and list my recent repositories"*
- *"Create a new branch `feature/login`, commit my changes, and open a pull request on GitHub"*
- *"Clone `owner/repo` into my workspace and summarize its architecture"*

---

## Repository Structure

```text
.
├── .gitignore
├── README.md
└── plugins/
    └── github/
        ├── plugin.json
        ├── README.md
        ├── assets/
        │   └── logo.svg
        ├── rules/
        │   └── AGENTS.md
        └── skills/
            └── github-manager/
                └── SKILL.md
```
