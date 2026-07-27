---
title: "AI Agent Harness Workflow"
description: "How a layered instruction model and shared command source reduce duplication across AI tools and make the environment easier to maintain and explain"
tags:
  - ai
  - workflow
  - automation
  - intermediate
published: false
author: "JD Locklin"
date_created: "2026-05-26"
date_updated: "2026-05-26"
difficulty: "Intermediate"
estimated_time: "1-2 hours"
---

# AI Agent Harness Workflow

Most AI demos stop at the model. Real value shows up one layer above it: the instructions, tool wiring, and operating rules that make output repeatable. This project focused on building that harness so AI supports daily work instead of creating process noise.

!!! success "The Transformation"

    **Before:** Scattered instructions, duplicated command patterns, tool-specific drift, manual cleanup on every update.

    **After:** Layered instruction model, shared canonical command source, clear separation of global vs. repo-local rules, and a maintainable multi-tool setup.

| Detail | Info |
|--------|------|
| **Difficulty** | Intermediate |
| **Time** | 1-2 hours |
| **Category** | AI Workflow |
| **Updated** | 2026-05-26 |

## Why this matters

As the number of tools, repos, and command patterns grows, the environment gets harder to maintain.

Without structure, the same problems show up quickly:

- instructions get duplicated,
- command behavior drifts between tools,
- project context gets lost,
- and every update turns into manual cleanup.

That sprawl slows delivery and makes the workflow harder to trust.

## Problem

Before standardization, the setup relied on scattered instruction files, repeated updates, and tool-specific drift.

That meant:

- the same guidance could exist in multiple places,
- common command patterns had to be maintained more than once,
- project-specific rules were mixed in with global rules,
- and the overall setup became harder to reason about over time.

The system worked, but it required too much manual correction to stay reliable.

## Previous workflow

The previous workflow looked like this:

1. Add or update guidance in one tool.
2. Repeat the same update in other tool-specific files.
3. Re-check global and repo-local rules manually.
4. Fix drift after behavior diverged.

Every update depended on memory and manual reconciliation.

## New workflow

The new workflow uses a layered instruction model to separate concerns and reduce duplication.

The structure now separates concerns more cleanly:

- **global instructions** define broad operating rules,
- **repo-local `AGENTS.md` files** define project-specific behavior,
- **shared slash commands** live in one canonical location,
- **tool-specific command folders** point back to that shared source via symlinks.

This creates a predictable model across tools while keeping project context close to the repo.

## Tools used

- `AGENTS.md` instruction files (global + repo-local)
- Claude Code
- OpenCode
- PowerShell
- Markdown
- Symlinks for shared slash-command routing

## AI role

AI accelerated the harness work in specific places:

- turning rough goals into a cleaner instruction structure,
- flagging redundancy between global and repo-local guidance,
- helping rewrite bloated instruction files into focused versions,
- generating reusable templates for project content,
- translating implementation choices into clearer documentation.

The gain was not just faster writing. It was faster iteration on the operating model around the tools.

## Human review and safeguards

This was not autopilot.

Final structure, naming, paths, and operating rules were reviewed manually before adoption. Scope decisions (global vs repo-local), command ownership, and naming conventions were explicitly validated.

AI accelerated drafting and analysis. Human review controlled the final operating model.

## Outcome

The result is a cleaner multi-tool setup with practical improvements:

- less duplicated instruction content,
- shared command definitions across tools,
- better separation between global and project-local behavior,
- easier maintenance when updating prompts or conventions,
- and a stronger foundation for future project documentation and content generation.

The environment is also easier to explain, which makes it easier to scale.

## Key takeaway

AI fluency is not just prompting. It is designing the harness so output is repeatable, maintainable, and useful in real work.

## Next improvement

Build on this foundation by standardizing more project write-ups, expanding reusable content patterns, and tightening workflow-specific documentation for faster publishing.

!!! question "Ready to clean up your workflow?"

    If this approach to structuring AI tools resonates, check out the [Multi-Agent Instruction Files](multi-agent-instructions.md) post or explore the [Automation](../automation/index.md) section for more practical workflow improvements.
