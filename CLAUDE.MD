# CLAUDE.MD - AI Assistant Guide

## Project Overview

**Code and Camera** is a comprehensive documentation site built with MkDocs and the Material theme. It covers hands-on technical projects spanning Arduino, gardening automation, home lab infrastructure, DevOps automation, and AI/machine learning.

**Author**: JD Locklin
**Site URL**: https://codeandcamera.me/
**Theme**: Material for MkDocs with custom indigo/yellow color scheme

## Project Purpose

This documentation site serves as a knowledge base and project portfolio for:
- Arduino electronics projects
- Gardening and growing automation systems
- Home lab infrastructure and server management
- DevOps automation and scripting
- AI and machine learning experiments

## Technology Stack

- **MkDocs**: Static site generator
- **Material Theme**: Modern, responsive documentation theme
- **Python**: MkDocs runtime
- **Markdown**: Content format with PyMdown extensions
- **GitHub Actions**: CI/CD automation
- **GitHub Pages**: Hosting platform

## Repository Structure

```
codeandcamera/
├── mkdocs.yml              # MkDocs configuration
├── README.md               # Developer documentation
├── CLAUDE.MD               # This file - AI assistant guide
├── DEPLOYMENT.md           # Deployment instructions
├── .github/workflows/      # CI/CD workflows
├── docs/                   # Documentation content
│   ├── index.md            # Homepage
│   ├── stylesheets/        # Custom CSS
│   ├── arduino/            # Arduino projects
│   ├── gardening/          # Gardening automation
│   ├── homelab/            # Home lab infrastructure
│   ├── automation/         # DevOps and scripts
│   ├── ai/                 # AI/ML projects
│   ├── admin/              # Site administration docs
│   ├── templates/          # Reusable templates
│   └── assets/             # Images and media
└── .gitignore              # Git ignore rules
```

## Content Organization

### Main Content Sections

1. **Arduino Projects** (`docs/arduino/`)
   - Hardware tutorials and code examples
   - LED control, sensors, actuators

2. **Gardening & Growing** (`docs/gardening/`)
   - Automated watering systems
   - Environmental monitoring
   - IoT gardening projects

3. **Home Lab** (`docs/homelab/`)
   - Proxmox VE setup and configuration
   - Server infrastructure
   - Networking and storage

4. **Automation** (`docs/automation/`)
   - Backup scripts
   - Ansible playbooks
   - DevOps workflows

5. **AI & Machine Learning** (`docs/ai/`)
   - Local chatbot implementations
   - Ollama and LLM projects
   - ML experiments

6. **Admin** (`docs/admin/`)
   - MkDocs getting started guide
   - Markdown examples
   - Git commit workflow
   - Tags reference

---

## Author Preferences & Content Voice

### Site Purpose & Audience

This site serves **two audiences simultaneously**:

1. **Recruiters & fellow professionals** — Should be able to quickly scan a post and understand what JD is working on, the technologies involved, and the level of complexity. They skim headings, diagrams, and summary boxes.
2. **Home lab enthusiasts & developers** — Want the full step-by-step walkthrough so they can replicate the project or learn from it. They read the details, code blocks, and troubleshooting sections.

**Structure every post to serve both**: scannable high-level overview at the top, detailed walkthrough below.

### Core Narrative: AI as a Capability Multiplier

The overarching message across all posts is: **AI is a tremendous boost to your skillset. It doesn't just make you faster — it expands what you're capable of building.**

When writing about projects that involved AI assistance:
- Use the narrative: "I couldn't have built this easily without AI" — honest, not hyperbolic
- Acknowledge that manual approaches exist but highlight the friction, trial-and-error, and time cost
- Show specific examples of what AI contributed (discovery, architecture design, debugging)
- Frame AI as a learning accelerator: "I learned X *while* building Y"
- Never position AI as doing all the work — the author drives decisions, AI assists

### Writing Voice

- **Conversational but technical** — Write like you're explaining to a smart friend, not writing a textbook
- **Honest about challenges** — Don't make things sound easy. Show the walls you hit and how you worked through them
- **Before/After framing** — Show what things looked like before the project and what they look like now. This is the most powerful way to demonstrate value
- **Practical over theoretical** — Always ground concepts in real, working examples from the author's actual setup
- **No fluff** — Every paragraph should teach something, show something, or move the narrative forward

### Post Structure (Standard Pattern)

Every project post should follow this structure:

1. **Overview** (2-3 sentences) — What it does, why it matters. Compelling enough for a skimmer to understand the project in 10 seconds
2. **Transformation callout** — A visible before/after box (use `!!! success` admonition) showing the concrete improvement
3. **Project Details table** — Difficulty, time required, category, last updated
4. **What You'll Learn** — Bullet list of learning outcomes
5. **The "Before" Picture** — Paint the pain point. Make it relatable
6. **How AI Helped** — The capability multiplier narrative. Be specific about what AI contributed at each phase
7. **Architecture/System Design** — Diagrams showing how pieces fit together
8. **Prerequisites** — What the reader needs before starting
9. **Step-by-Step Guide** — Full walkthrough with code snippets. Use content tabs (`=== "Option A"`) for multi-platform instructions
10. **Results & Real-World Usage** — Show actual examples from the working system. Conversation examples, command outputs, screenshots
11. **Troubleshooting** — Real problems encountered and their solutions. These are extremely valuable to readers
12. **What's Next** — Tease future possibilities without committing to specifics
13. **Resources** — Links to relevant tools, repos, documentation

### Future Post Teasers

When teasing future content at the end of a post:
- **Keep it vague but compelling** — describe possibilities, not promises
- Use phrasing like "I'm exploring..." or "I'm actively experimenting with..."
- List 3-4 concrete-sounding ideas to spark curiosity
- End with a question that invites the reader to think about their own use case
- Never commit to a timeline or specific follow-up post title
- Make them want to come back without feeling like a sales pitch

Example pattern:
```markdown
## What's Next

[Brief paragraph about the foundation this project creates]

- Possibility 1 — described enticingly but not as a commitment
- Possibility 2
- Possibility 3

[Sentence about actively exploring these ideas]

!!! question "Engagement Question"
    A genuine question inviting the reader to think about their own setup
```

### Post Length

- **Target: Medium dive (~1500-2200 words)** — enough depth for a walkthrough, not so long it loses people
- If a post grows beyond 2500 words, consider splitting into a series

### Visual Content

- **Include as many visuals as possible** — diagrams, screenshots, architecture overviews
- **Mermaid diagrams** for all architecture and workflow visualizations (rendered automatically by the site)
- **Screenshots** for terminal output, UI interactions, and conversation examples
- Leave `<!-- TODO: Add screenshot -->` comments with image paths as placeholders when screenshots aren't available yet
- Store all images in `docs/assets/<section>/<post-slug>/` (e.g., `docs/assets/homelab/mcp-proxmox/`)

---

## Mermaid Diagram Rules

### CRITICAL: Do NOT Hardcode Colors

Material for MkDocs **automatically themes Mermaid diagrams** using the site's CSS variables (indigo primary, amber/yellow accent). Diagrams adapt correctly for both dark and light mode.

**NEVER** add `style` directives with hardcoded fill/stroke colors to Mermaid diagrams. They will look wrong in one or both color modes.

```markdown
<!-- BAD - hardcoded colors break in light/dark mode -->
style myNode fill:#1a1a2e,stroke:#e94560,color:#eee

<!-- GOOD - let the theme handle it -->
(no style directive needed)
```

If you absolutely must add emphasis to a specific node, use Mermaid's built-in class syntax or `:::` notation rather than inline styles.

### CRITICAL: Line Breaks in Node Labels

In Mermaid flowchart nodes, `\n` renders as **literal text** (the characters backslash-n), not a line break.

**Always use `<br/>` for line breaks inside node labels:**

```markdown
<!-- BAD - renders as literal "\n" text -->
A["First line\nSecond line"]

<!-- GOOD - renders as actual line break -->
A["First line<br/>Second line"]
```

This applies to all node types: `["..."]`, `("...")`, `{"..."}`, etc.

### Diagram Best Practices

- Use emoji in subgraph labels for visual hierarchy (e.g., `subgraph tools["🤖 AI Tools"]`)
- Keep edge labels short (e.g., "SSE" not "SSE Transport Protocol")
- Keep participant names short in sequence diagrams to avoid overflow on mobile
- Use `flowchart TB` (top-to-bottom) for architecture diagrams
- Use `flowchart LR` (left-to-right) for comparison/workflow diagrams
- Use `sequenceDiagram` for time-based flows (boot sequences, request chains)
- Test that diagrams render correctly in both dark and light mode

### Site Color Reference (for any manual styling)

If custom colors are absolutely needed somewhere:

| Purpose | Light Mode | Dark Mode | CSS Variable |
|---------|-----------|-----------|-------------|
| Primary | `#3f51b5` (Indigo 500) | `#3f51b5` | `--md-primary-fg-color` |
| Primary light | `#7986cb` (Indigo 300) | `#7986cb` | `--md-primary-fg-color--light` |
| Primary dark | `#303f9f` (Indigo 700) | `#303f9f` | `--md-primary-fg-color--dark` |
| Accent | `#ffc107` (Amber 500) | `#ffeb3b` (Yellow 500) | `--md-accent-fg-color` |
| Background | `#ffffff` | `#0d1117` | `--md-default-bg-color` |
| Text | default | `#e3e3e3` | `--md-default-fg-color--light` |

---

## Writing Conventions

### Markdown Style

- Use **ATX-style headers** (`#` syntax) not Setext
- Code blocks should specify language for syntax highlighting
- Use admonitions for callouts (note, tip, warning, danger, success, question)
- Leverage content tabs for multi-option instructions (e.g., different OS, different AI tools)
- Include Mermaid diagrams where helpful (follow the Mermaid rules above)

### Page Structure

Every project page should include:

1. **Title** (H1) - Clear, descriptive
2. **Metadata** - Tags, status badges
3. **Overview** - What the project does
4. **Materials/Requirements** - What you need
5. **Instructions** - Step-by-step guide
6. **Code/Configuration** - Fully documented
7. **Testing** - How to verify it works
8. **Troubleshooting** - Common issues
9. **Next Steps** - Extensions or improvements

### Frontmatter Template

Every post must include this YAML frontmatter:

```yaml
---
title: "Post Title"
description: "One-sentence description for SEO and social sharing"
tags:
  - homelab       # Primary category
  - ai            # Secondary categories as relevant
  - automation
  - advanced      # Difficulty: beginner, intermediate, advanced
published: false  # Set to true when ready to go live
author: "JD Locklin"
date_created: "YYYY-MM-DD"
date_updated: "YYYY-MM-DD"
difficulty: "Intermediate"
estimated_time: "2-3 hours"
---
```

Always start with `published: false` so the author can review before going live.

### Code Blocks

- Always specify the language (```python, ```bash, ```yaml, etc.)
- Include comments for clarity
- Show complete, working examples
- Use line highlighting for important sections
- Use `title="filename"` to show which file the code belongs to

Example:
````markdown
```yaml title="docker-compose.yml" hl_lines="5 6"
services:
  my-service:
    build: .
    ports:
      - "8811:8811"   # REST API
      - "8812:8812"   # SSE endpoint
```
````

### Admonitions

Use appropriate admonition types:

```markdown
!!! note
    General information or context

!!! tip
    Helpful suggestions or best practices

!!! warning
    Important cautions or gotchas

!!! danger
    Critical warnings about potential issues

!!! success "Before → After"
    Transformation callouts showing concrete improvement

!!! question "Engagement Question"
    End-of-post questions to invite reader interaction
```

### Content Tabs

Use content tabs when providing instructions for multiple platforms or tools:

```markdown
=== "Claude Code"

    Instructions for Claude Code

=== "VS Code / Copilot"

    Instructions for VS Code

=== "Claude Desktop"

    Instructions for Claude Desktop
```

## Theme Customization

### Color Palette

- **Primary**: Indigo (bold, modern)
- **Accent (Dark Mode)**: Yellow
- **Accent (Light Mode)**: Amber
- **Automatic theme toggle** based on system preference

### Typography

- **Text**: Inter font family
- **Code**: JetBrains Mono

### Custom CSS

Located in `docs/stylesheets/extra.css` for brand-specific styling.

## Navigation Structure

The site uses **tabbed navigation** with the following top-level sections:

```yaml
- Home
- Gardening & Growing
- Home Lab
- Automation
- AI & Machine Learning
- Admin
```

Navigation is configured in `mkdocs.yml` under the `nav:` key.

When adding a new post, you must:
1. Add the page to `mkdocs.yml` under the correct section
2. Update the section's `index.md` to link to the new post

Example nav entry for a section with posts:
```yaml
- Home Lab:
    - homelab/index.md
    - AI-Powered Proxmox MCP: homelab/mcp-proxmox-infrastructure.md
```

## Tags System

Tags are hierarchical and configured in `mkdocs.yml`:

```yaml
plugins:
  - tags:
      tags_file: admin/tags.md
      tags_hierarchy: true
```

Common tag categories:
- `arduino:*` - Arduino-related projects
- `gardening:*` - Gardening projects
- `homelab:*` - Home lab infrastructure
- `automation:*` - Automation scripts
- `ai:*` - AI/ML projects
- `tutorial` - Step-by-step guides
- `reference` - Reference documentation
- `beginner`, `intermediate`, `advanced` - Difficulty levels

## Development Workflow

### Local Development

1. Install dependencies:
   ```bash
   pip install mkdocs-material pymdown-extensions
   ```

2. Serve locally:
   ```bash
   mkdocs serve
   ```
   Access at `http://127.0.0.1:8000`

3. Build for production:
   ```bash
   mkdocs build
   ```

### Git Workflow

- **Main branch**: `main` (auto-deploys to GitHub Pages)
- Use clear, descriptive commit messages
- Push directly to main for content additions

### Deployment

Automated via GitHub Actions (`.github/workflows/ci.yml`):
- Triggered on push to main
- Builds MkDocs site
- Deploys to GitHub Pages

## Common Tasks for AI Assistants

### Creating a New Post

Full checklist:

1. **Plan the post** — Identify the target audience, the core narrative, and the before/after transformation
2. **Create the asset directory**: `docs/assets/<section>/<post-slug>/`
3. **Create the post file**: `docs/<section>/<post-slug>.md` using the frontmatter template above
4. **Follow the standard post structure** outlined in "Post Structure (Standard Pattern)" above
5. **Add Mermaid diagrams** following the Mermaid rules (no hardcoded colors, `<br/>` for line breaks)
6. **Add screenshot placeholders** with `<!-- TODO: Add screenshot -->` comments
7. **Update navigation** in `mkdocs.yml`
8. **Update the section index** page to link to the new post
9. **Set `published: false`** so the author can review
10. **Commit and push** to trigger deployment

### Updating Existing Content

1. **Maintain style**: Follow existing formatting conventions
2. **Preserve links**: Don't break internal navigation
3. **Update index pages**: Keep section landing pages current
4. **Test locally**: Run `mkdocs serve` to verify changes

### Adding New Sections

1. **Create directory**: `docs/new-section/`
2. **Create index**: `docs/new-section/index.md`
3. **Update navigation**: Add to `mkdocs.yml` nav
4. **Update homepage**: Link from `docs/index.md`
5. **Create tag category**: Add new tag prefix if needed

### Troubleshooting Build Issues

Common issues:
- **Broken links**: Use relative paths from docs root
- **Missing extensions**: Check `markdown_extensions` in mkdocs.yml
- **YAML syntax**: Validate mkdocs.yml indentation
- **Plugin errors**: Ensure plugins are installed
- **LSP YAML errors on `!!python/name:`**: These are expected MkDocs-specific YAML tags — ignore them

## Important Guidelines

### DO:
- ✅ Follow the existing style and structure
- ✅ Include complete, tested code examples
- ✅ Add appropriate tags to all pages
- ✅ Use admonitions for important information
- ✅ Keep navigation logical and organized
- ✅ Write clear, beginner-friendly instructions
- ✅ Include Mermaid diagrams for architecture and workflows
- ✅ Use content tabs for multi-platform instructions
- ✅ Include a before/after transformation callout in every project post
- ✅ Add a "What's Next" section with vague but compelling future teasers
- ✅ Set `published: false` on new posts for author review
- ✅ Create asset directories for screenshots even if images aren't ready yet

### DON'T:
- ❌ Create orphan pages (not in navigation)
- ❌ Use absolute URLs for internal links
- ❌ Add untested or incomplete code
- ❌ Forget to specify language in code blocks
- ❌ Break existing navigation structure
- ❌ Add large binary files without good reason
- ❌ Use complex jargon without explanation
- ❌ Hardcode colors in Mermaid diagrams (let the theme handle it)
- ❌ Use `\n` for line breaks in Mermaid nodes (use `<br/>`)
- ❌ Commit posts with `published: true` without author approval
- ❌ Make future post teasers too specific or commit to timelines

## File Naming Conventions

- **Lowercase with hyphens**: `auto-watering-system.md`
- **Descriptive names**: Clearly indicate content
- **No spaces**: Use hyphens for word separation
- **Index files**: Each section has `index.md`
- **Asset directories**: Match the post slug (e.g., `mcp-proxmox-infrastructure.md` → `assets/homelab/mcp-proxmox/`)

## Resources

- [MkDocs Documentation](https://www.mkdocs.org/)
- [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/)
- [PyMdown Extensions](https://facelessuser.github.io/pymdown-extensions/)
- [Mermaid Diagrams](https://mermaid.js.org/)

## Contact

**Author**: JD Locklin
**Project**: Code and Camera
**Website**: https://codeandcamera.me/

---

*This file helps AI assistants (Claude Code, OpenCode, GitHub Copilot) understand the project structure, content conventions, and author preferences. Last updated: 2026-02-21*
