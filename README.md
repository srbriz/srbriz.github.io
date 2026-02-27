# srbriz.github.io

GitHub Pages Portfolio Website

## Technology Stack

- **Spec Kit (Speckit)** for spec-driven feature workflow
- **GitHub Pages** for static site hosting
- **Cloudflare Domain** for DNS and domain management
- **Claude Code** for AI-assisted development workflows
- **GitHub MCP** for GitHub-integrated tooling and automation

## Setup

This project uses [Task](https://taskfile.dev) (`go-task`) as its task runner. Install it first,
then install all other dependencies:

```bash
brew install go-task
task install
```

### Launching Claude Code

```bash
task claude
```

### GitHub MCP

This project uses the [GitHub MCP server](https://github.com/github/github-mcp-server)
(configured in `.mcp.json`) to give Claude Code access to GitHub APIs.
It reads authentication from the `GITHUB_PERSONAL_ACCESS_TOKEN` environment variable.

If you're using `claude` outside of `task`, set the environment variable using
your active `gh` CLI session:

```bash
export GITHUB_PERSONAL_ACCESS_TOKEN=$(gh auth token)
```
