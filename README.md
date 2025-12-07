# Code and Camera

A documentation site built with MkDocs and the Material theme, containing guides for code commits, markdown examples, and various technical documentation topics.

## Project Structure

```
codeandcamera/
├── mkdocs.yml              # MkDocs configuration file
├── .github/
│   └── workflows/
│       └── ci.yml          # GitHub Actions CI/CD workflow for automated deployment
├── .gitignore              # Git ignore rules
├── docs/                   # Documentation source directory
│   ├── index.md            # Homepage
│   ├── CommittoGithub.md   # Git commit and push guide
│   ├── mkdownexample.md    # Markdown formatting examples
│   ├── tags.md             # Tags page
│   ├── test.md             # MkDocs configuration examples
│   ├── ansible/            # Ansible-related documentation
│   ├── gardening/          # Gardening-related documentation
│   ├── assets/             # Media and asset files
│   └── templates/          # Reusable documentation templates
└── README.md               # This file

## Features

- **Material Theme**: Modern, responsive documentation theme
- **Dark/Light Mode**: Automatic theme toggle
- **Search**: Full-text search functionality
- **Tag System**: Hierarchical tag organization
- **CI/CD**: Automated deployment to GitHub Pages
- **Code Highlighting**: Syntax highlighting for multiple languages

## Setup

1. Install Python and required dependencies:
   ```bash
   pip install mkdocs-material
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

Copyright © 2025 codeandcamera
