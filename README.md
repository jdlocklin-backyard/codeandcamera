# Code and Camera

A documentation site built with MkDocs and the Material theme, covering Arduino projects, gardening automation, home lab infrastructure, DevOps automation, and AI/machine learning.

## Project Structure

```
codeandcamera/
├── mkdocs.yml                      # MkDocs configuration file
├── .github/
│   └── workflows/
│       └── ci.yml                  # GitHub Actions CI/CD workflow
├── .gitignore                      # Git ignore rules
├── docs/                           # Documentation source directory
│   ├── index.md                    # Homepage
│   ├── stylesheets/
│   │   └── extra.css               # Custom CSS styling
│   ├── arduino/                    # Arduino projects
│   │   ├── index.md                # Arduino landing page
│   │   └── led-button-control.md   # Sample project
│   ├── gardening/                  # Gardening & growing
│   │   ├── index.md                # Gardening landing page
│   │   └── auto-watering-system.md # Sample project
│   ├── homelab/                    # Home lab infrastructure
│   │   ├── index.md                # Home lab landing page
│   │   └── proxmox-setup.md        # Sample project
│   ├── automation/                 # DevOps & automation
│   │   ├── index.md                # Automation landing page
│   │   ├── backup-script.md        # Sample project
│   │   └── ansible/                # Ansible resources
│   ├── ai/                         # AI & machine learning
│   │   ├── index.md                # AI landing page
│   │   └── local-chatbot-ollama.md # Sample project
│   ├── admin/                      # Site administration
│   │   ├── getting-started.md      # MkDocs basics
│   │   ├── markdown-examples.md    # Markdown reference
│   │   ├── commit-to-github.md     # Git workflow guide
│   │   └── tags.md                 # Tags page
│   ├── templates/                  # Reusable templates
│   │   └── project-template.md     # Project page template
│   └── assets/                     # Media and asset files
└── README.md                       # This file
```

## What Are Projects?

**Projects** are the heart of Code and Camera. Each project is a comprehensive, step-by-step guide that teaches practical skills across five main categories:

- **Arduino**: Electronics and microcontroller projects for IoT and embedded systems
- **Gardening**: Technology-enhanced growing and automation solutions
- **Home Lab**: Infrastructure setup, virtualization, and self-hosting guides
- **Automation**: DevOps workflows, scripting, and task automation
- **AI & Machine Learning**: Practical AI implementations and machine learning projects

Projects follow a consistent template structure with prerequisites, materials lists, detailed instructions, troubleshooting tips, and resources for further learning. They're designed to be educational, practical, and adaptable to your specific needs.

## Features

- **Material Theme**: Modern, responsive documentation with teal color scheme
- **Dark/Light Mode**: Automatic theme toggle based on system preference
- **Search**: Full-text search with suggestions
- **Tag System**: Hierarchical tag organization
- **CI/CD**: Automated deployment to GitHub Pages
- **Code Highlighting**: Syntax highlighting with copy button
- **Mermaid Diagrams**: Flowcharts and diagrams in markdown
- **Content Tabs**: Tabbed content sections

## Setup

1. Install Python and required dependencies:
   ```bash
   pip install mkdocs-material pymdown-extensions
   ```

2. Serve documentation locally:
   ```bash
   mkdocs serve
   ```

3. Build for production:
   ```bash
   mkdocs build
   ```

## Deployment

The project uses GitHub Actions to automatically build and deploy to GitHub Pages on push to main/master branches. See `.github/workflows/ci.yml` for details.

## Author

JD Locklin

## License

Copyright © 2025 Code and Camera
