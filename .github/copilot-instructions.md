# Copilot Instructions for Code and Camera

This is a MkDocs Material documentation site at https://codeandcamera.me/

## Quick Reference

- **Full instructions**: See `CLAUDE.MD` in the project root for complete content creation guidelines, author preferences, Mermaid diagram rules, and post structure templates.
- **Post template**: See `docs/templates/project-template.md`
- **Theme**: Indigo primary, yellow/amber accent, auto dark/light mode

## Critical Rules

1. **Mermaid diagrams**: NEVER hardcode colors (let the theme handle it). Use `<br/>` for line breaks in node labels, NOT `\n`.
2. **New posts**: Always set `published: false` in frontmatter for author review.
3. **Navigation**: Always update `mkdocs.yml` nav AND the section's `index.md` when adding a post.
4. **Assets**: Store images in `docs/assets/<section>/<post-slug>/`
5. **Content voice**: AI as a capability multiplier, honest about challenges, before/after framing, practical over theoretical.
