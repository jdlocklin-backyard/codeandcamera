# CLAUDE.MD - AI Assistant Guide

## Project Overview

**Code and Camera** is a comprehensive documentation site built with MkDocs and the Material theme. It covers hands-on technical projects spanning Arduino, gardening automation, home lab infrastructure, DevOps automation, and AI/machine learning.

**Author**: JD Locklin
**Site URL**: https://codeandcamera.me/
**Theme**: Material for MkDocs with custom teal/cyan color scheme

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

## Writing Conventions

### Markdown Style

- Use **ATX-style headers** (`#` syntax) not Setext
- Code blocks should specify language for syntax highlighting
- Use admonitions for callouts (note, tip, warning, danger)
- Leverage content tabs for multi-option instructions
- Include Mermaid diagrams where helpful

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

### Code Blocks

- Always specify the language (```python, ```bash, ```yaml, etc.)
- Include comments for clarity
- Show complete, working examples
- Use line highlighting for important sections

Example:
````markdown
```python hl_lines="3 4"
import time

# Configuration
LED_PIN = 13
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
```

## Theme Customization

### Color Palette

- **Primary**: Teal (tech + nature vibe)
- **Accent (Dark)**: Amber
- **Accent (Light)**: Deep Orange
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
- Arduino Projects
- Gardening & Growing
- Home Lab
- Automation
- AI & Machine Learning
- Admin
```

Navigation is configured in `mkdocs.yml` under the `nav:` key.

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

- **Main branch**: `main` or `master` (auto-deploys to GitHub Pages)
- **Working branches**: Feature branches for new content
- Use clear, descriptive commit messages
- Reference related issues in commits

### Deployment

Automated via GitHub Actions (`.github/workflows/ci.yml`):
- Triggered on push to main/master
- Builds MkDocs site
- Deploys to GitHub Pages

## Common Tasks for AI Assistants

### Creating New Project Pages

1. **Use the template**: Reference `docs/templates/project-template.md`
2. **Add to navigation**: Update `mkdocs.yml` nav section
3. **Apply tags**: Add appropriate metadata tags
4. **Include code**: Provide complete, working examples
5. **Add images**: Place in `docs/assets/` if needed

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

## Important Guidelines

### DO:
- ✅ Follow the existing style and structure
- ✅ Include complete, tested code examples
- ✅ Add appropriate tags to all pages
- ✅ Use admonitions for important information
- ✅ Keep navigation logical and organized
- ✅ Test locally before committing
- ✅ Write clear, beginner-friendly instructions

### DON'T:
- ❌ Create orphan pages (not in navigation)
- ❌ Use absolute URLs for internal links
- ❌ Add untested or incomplete code
- ❌ Forget to specify language in code blocks
- ❌ Break existing navigation structure
- ❌ Add large binary files without good reason
- ❌ Use complex jargon without explanation

## File Naming Conventions

- **Lowercase with hyphens**: `auto-watering-system.md`
- **Descriptive names**: Clearly indicate content
- **No spaces**: Use hyphens for word separation
- **Index files**: Each section has `index.md`

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

*This file helps AI assistants understand the project structure and conventions. Last updated: 2025-12-20*
