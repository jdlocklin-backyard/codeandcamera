# AGENTS.md — website-codeandcamera

Repo-specific instructions for the website project. Repo root is `D:\codeandcamera`.

## Project Purpose

This site exists to show how AI improves real workflows, reduces time to delivery, and acts as a practical multiplier in day-to-day work.

The website should make it easy for a non-technical visitor, recruiter, hiring manager, or peer to understand:

- what problems are being solved,
- what tools are being used,
- how AI is being applied,
- what changed because of AI,
- and why the result matters.

The goal is not to look flashy or hype-driven. The goal is to show credible AI fluency through practical work.

## Audience

Primary audiences:

- Recruiters and hiring managers who may not be deeply technical.
- Technical peers who want enough specificity to judge whether the work is real.
- Anyone reviewing the site as evidence of practical AI capability.

Content should be easy to scan for non-technical readers, but still specific enough to prove real hands-on work.

## Positioning

The site should consistently reinforce these ideas:

- AI is a workflow multiplier, not a magic trick.
- The work combines leading-edge AI tools with traditional tools and operational discipline.
- The focus is practical leverage, orchestration, and applied fluency — not vague futurism.
- The site should make it obvious that the owner is AI fluent and actively learning how to use AI effectively in real environments.

## Content Strategy

When proposing, editing, or generating website content, optimize for:

- clarity over hype,
- specific outcomes over abstract claims,
- concrete workflows over generic AI enthusiasm,
- digestibility for non-technical readers,
- and credibility with technical readers.

Whenever possible, show:

- the problem,
- the workflow before AI,
- the workflow after AI,
- the tools used,
- the human role in the loop,
- and the business or operational impact.

## Project Types To Highlight

The site should emphasize projects that demonstrate:

- AI-assisted workflow improvement,
- automation that reduces manual effort or cycle time,
- agent and MCP orchestration,
- practical use of AI inside everyday tools,
- system design, operational thinking, and implementation discipline,
- and the ability to make advanced concepts understandable.

## AI-Written Document Rules

AI-written documents in this repo should be structured as practical, readable case studies or guides.

Each document should make clear:

1. What the problem or use case was.
2. What tools were involved.
3. What the workflow looked like before.
4. What changed after AI or automation was introduced.
5. What guardrails, review steps, or QA checks were used.
6. What the measurable or practical outcome was.
7. What someone else could learn or reuse from the work.

Avoid generic “AI can help with many things” filler. Every document should anchor to a real workflow, real toolchain, or real operating pattern.

## Recommended Document Layout

Use this default structure for AI-assisted portfolio or project writeups unless a local template says otherwise:

1. Title
2. Why this matters
3. Problem
4. Previous workflow
5. New workflow
6. Tools used
7. AI role
8. Human review / safeguards
9. Outcome
10. Key takeaway
11. Optional: next improvement

## Template Location Rules

- Prefer using templates stored in this repo when available.
- Canonical template folder: `docs/templates/`
- Current template files:
  - `docs/templates/ai-case-study.md`
  - `docs/templates/ai-how-i-work.md`
  - `docs/templates/project-overview.md`
- Seed reference content currently lives under:
  - `content/how-i-use-ai.md`
  - `content/projects/agent-harness-workflow.md`
  - `content/projects/proxmox-mcp-automation.md`
- When creating a new reusable template, place it in `docs/templates/` rather than burying it inside a random post or draft directory.
- If no template exists, create one in `docs/templates/` before generating multiple similar documents.

## Template Selection Rules

Choose templates by intent:

- Use `docs/templates/ai-case-study.md` when the request is about:
  - a specific project,
  - a workflow improvement,
  - an automation,
  - an AI implementation,
  - or a before/after process change.

- Use `docs/templates/ai-how-i-work.md` when the request is about:
  - overall working style,
  - AI philosophy,
  - how AI fits into everyday work,
  - how traditional tools and AI work together,
  - or a high-level explanation for recruiters or non-technical readers.

- Use `docs/templates/project-overview.md` when the request is about:
  - a short project summary,
  - a portfolio tile,
  - an overview page,
  - or a concise explanation of what a project is and what it proves.

## Template Fallback Rule

- If the request is clearly about one project or one workflow change, default to `docs/templates/ai-case-study.md`.
- If the request is broad and describes personal approach, default to `docs/templates/ai-how-i-work.md`.
- If the request is short and summary-oriented, default to `docs/templates/project-overview.md`.
- If a request could fit multiple templates, choose the simplest template that matches the user’s intent and state which template was used.

## Template Examples

Examples:

- “Write a case study about setting up shared slash commands across Claude and OpenCode.”
  - Use `docs/templates/ai-case-study.md`

- “Write a page explaining how I use AI as a multiplier in my work.”
  - Use `docs/templates/ai-how-i-work.md`

- “Write a short project summary for my Proxmox MCP automation work.”
  - Use `docs/templates/project-overview.md`

## Content Structure Rules

- `docs/templates/` contains reusable writing templates.
- `content/` contains seed content, reference write-ups, and future draft source material.
- `content/projects/` contains example project or case-study write-ups that AI can use as references for tone, structure, and depth.
- Treat the current files under `content/` and `content/projects/` as seed examples unless explicitly marked or promoted for publication.
- Do not assume every file in `content/` is a published page.
- Templates are blank reusable patterns.
- Content files are filled-in examples or draft source material generated from those patterns.

## Path Handling

- Prefer logical names over absolute local filesystem paths when writing content, docs, or summaries.
- Use repo identity, canonical asset names, and repo-relative paths unless an exact path is operationally required.
- Only surface exact local paths when needed to:
  - create a file,
  - open a file,
  - verify a file,
  - or resolve ambiguity between two locations.
- Do not include unnecessary local machine paths in website content, portfolio writeups, screenshots, or user-facing copy.
- When documenting structure, prefer repo-relative examples such as:
  - `docs/templates/`
  - `content/`
  - `content/projects/`
  instead of repeating full Windows paths.

## Tone and Writing Rules

- Write in plain language first.
- Assume the first reader may be non-technical.
- Avoid unexplained jargon unless the surrounding text makes it obvious.
- Do not oversell.
- Do not use empty AI buzzwords.
- Prefer “here is how the workflow changed” over “this is revolutionary”.
- The writing should sound credible, practical, and modern.

## Website Workflow Rules

- Keep content changes separate from layout, config, or build changes when possible.
- Prefer small, focused changes with clear diffs.
- Treat content files as the primary editing surface unless a template or build change is explicitly needed.
- When creating new content, provide:
  - the exact path,
  - the exact PowerShell command to create and open it,
  - the full paste-ready content,
  - and the verification command.

## Branch and Publish Rules

- Do not commit directly to the main or production branch unless explicitly instructed.
- Before proposing a publish or deploy, surface:
  - current branch,
  - working tree status,
  - and the local preview or build command if one exists.
- Do not assume deployment details; read local project files first.

## Safety

- Never introduce secrets, tokens, or private data into
-
-
- ## Draft Review and Git Workflow

- Treat draft completion and publication as separate steps.
- Do not assume a finished draft should be published immediately.
- When a requested draft is complete, first provide a review path before proposing publish actions.

### Review First Rule

Before suggesting commit, push, or publish, surface:

1. which files changed,
2. whether the changes are draft content, template changes, config changes, or layout changes,
3. whether any unrelated files are also modified or untracked,
4. and the safest review option available.

### Preferred Review Order

Use this order unless the user explicitly says otherwise:

1. local preview or local dev server,
2. Git diff or file review in the editor,
3. commit and push to a branch for GitHub review,
4. publish or production deploy.

### Commit Scope Rule

- Never assume all untracked or modified files belong in the same commit.
- When draft content is complete, propose the smallest sensible commit scope.
- If unrelated files are present, ask whether to:
  - commit only the draft-related files, or
  - include the additional files.

### Branch and Push Rule

- Do not commit directly to main or production unless explicitly instructed.
- Prefer committing draft work to the current working branch or a draft branch.
- After commit, provide the exact branch name and confirm whether the user wants the branch pushed to GitHub.

### GitHub Review Rule

If the user says they want to review the draft in GitHub:

- stage only the intended files,
- commit with a clear content-focused message,
- push to the current branch or a draft branch,
- and report back with:
  - files included,
  - commit message,
  - branch name,
  - and next step for review.

### Publish Rule

- A GitHub push is not the same as publishing the site.
- Treat GitHub review and production publish as separate approvals.
- Never deploy draft content to production without an explicit publish instruction.

### Default Prompt

When draft work is complete, use this prompt:

`Draft is ready. Review locally, commit and push to GitHub for review, or publish?`