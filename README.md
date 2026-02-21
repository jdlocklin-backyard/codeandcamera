# Code and Camera

A portfolio and knowledge-sharing site built with MkDocs Material, covering home lab infrastructure, AI-assisted development, gardening automation, and more.

**Live site**: [codeandcamera.me](https://codeandcamera.me/)

## Current Content

- **Home Lab** — Infrastructure, servers, and self-hosted solutions
  - [AI-Powered Proxmox MCP](https://codeandcamera.me/homelab/mcp-proxmox-infrastructure/) — Universal MCP infrastructure for managing Proxmox via natural language
- **AI & Machine Learning** — AI tools, workflows, and experiments
  - [Multi-Agent Instruction Files](https://codeandcamera.me/ai/multi-agent-instructions/) — Building instruction files so every AI assistant follows the same standards

## Project Structure

```
codeandcamera/
├── mkdocs.yml                  # Site configuration
├── requirements.txt            # Pinned Python dependencies
├── CLAUDE.MD                   # AI assistant instructions (Claude Code, OpenCode)
├── .github/
│   ├── copilot-instructions.md # AI assistant instructions (GitHub Copilot)
│   └── workflows/ci.yml       # CI/CD: strict build + deploy to GitHub Pages
├── docs/
│   ├── index.md                # Homepage
│   ├── stylesheets/extra.css   # Custom CSS (indigo/yellow theme)
│   ├── homelab/                # Home lab posts
│   ├── ai/                     # AI & ML posts
│   ├── gardening/              # Gardening automation (planned)
│   ├── automation/             # DevOps & scripting (planned)
│   ├── admin/                  # Site admin docs & tags
│   ├── templates/              # Post templates
│   └── assets/                 # Images organized by section
└── readme.md                   # This file
```

## Features

- **Material for MkDocs** (v9.6.14) with custom indigo/yellow color scheme
- **Dark/Light Mode** with automatic system preference toggle
- **Full-text search** with highlighting and suggestions
- **Mermaid diagrams** for architecture and workflow visualizations
- **Content tabs** for multi-platform instructions
- **Code blocks** with syntax highlighting, line numbers, and copy button
- **Tag system** for browsing content by topic
- **CI/CD** via GitHub Actions with `--strict` build validation

## Local Development

```bash
# Create virtual environment
python -m venv venv
venv\Scripts\activate  # Windows
# source venv/bin/activate  # macOS/Linux

# Install dependencies
pip install -r requirements.txt

# Serve locally
mkdocs serve

# Build (with strict mode to catch warnings)
mkdocs build --strict
```

## Deployment

Pushes to `main` trigger GitHub Actions, which runs a strict build and deploys to GitHub Pages. See `.github/workflows/ci.yml`.

## AI Assistant Instructions

This repo includes instruction files that teach AI coding assistants the project's conventions:

- **`CLAUDE.MD`** — Comprehensive guide for Claude Code / OpenCode (writing voice, post structure, Mermaid rules, color reference)
- **`.github/copilot-instructions.md`** — Compact critical rules for GitHub Copilot

## Author

**JD Locklin** — [GitHub](https://github.com/jdlocklin-backyard)

## License

Copyright &copy; 2026 Code and Camera | JD Locklin
