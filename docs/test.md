# MkDocs Configuration Example

```yaml
# Project information
site_name: Complete MkDocs Example
site_url: https://example.com/
site_author: Your Name
site_description: A comprehensive example of MkDocs functionality

# Repository
repo_name: username/repo
repo_url: https://github.com/username/repo
edit_uri: edit/main/docs/

# Theme configuration
theme:
       name: material
       features:
              - navigation.tabs
              - navigation.sections
              - navigation.top
              - search.highlight
       palette:
              - scheme: default
                     primary: indigo
                     accent: indigo
                     toggle:
                            icon: material/brightness-7
                            name: Switch to dark mode
              - scheme: slate
                     primary: indigo
                     accent: indigo
                     toggle:
                            icon: material/brightness-4
                            name: Switch to light mode

# Extensions
markdown_extensions:
       - admonition
       - codehilite
       - footnotes
       - toc:
                     permalink: true
       - pymdownx.highlight
       - pymdownx.superfences
       - pymdownx.tasklist:
                     custom_checkbox: true

# Navigation
nav:
       - Home: index.md
       - User Guide:
              - Installation: guide/installation.md
              - Configuration: guide/configuration.md
       - API Reference:
              - Authentication: api/auth.md
              - Endpoints: api/endpoints.md
       - About: about.md

# Extra settings
extra:
       social:
              - icon: fontawesome/brands/github
                     link: https://github.com/username
              - icon: fontawesome/brands/twitter
                     link: https://twitter.com/username

# Plugins
plugins:
       - search
       - minify:
                     minify_html: true
```