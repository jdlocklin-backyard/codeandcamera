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

Most AI demos stop at the model. Real work starts one layer above, where tools, instructions, context, and repeatable workflows either make the model useful or turn it into noise. This project is about building a cleaner agent harness around day-to-day work so AI can be used as a practical multiplier instead of a novelty.

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

As the number of tools, repos, command patterns, and agent contexts grows, the working environment gets harder to manage.

Without structure, the same problems show up quickly:

- instructions get duplicated,
- command behavior drifts between tools,
- project context gets lost,
- and every update turns into manual cleanup.

That kind of sprawl slows everything down and makes the environment harder to trust.

## Problem

Before standardizing the setup, the environment relied more on scattered instructions, repeated updates, and tool-specific behavior.

That meant:

- the same guidance could exist in multiple places,
- common command patterns had to be maintained more than once,
- project-specific rules were mixed in with global rules,
- and the overall setup became harder to reason about over time.

The system still worked, but it required more memory, more cleanup, and more manual correction than it should have.

## Previous workflow

The previous approach worked but lacked structure. Instructions lived in ad-hoc locations. Command patterns had to be updated independently for each tool. Global and project-level rules shared the same space, making it hard to tell what applied where.

Every change meant checking multiple files, remembering what lived where, and hoping nothing was missed.

## New workflow

The updated workflow uses a layered instruction model designed to reduce duplication and make the environment easier to maintain.

The structure now separates concerns more cleanly:

- **global instructions** define broad operating rules,
- **repo-local `AGENTS.md` files** define project-specific behavior,
- **shared slash commands** live in one canonical location,
- **tool-specific command folders** point back to that shared source via symlinks.

That creates a more predictable setup across tools while keeping local context close to the code it belongs to.

## Tools used

- AGENTS.md
- Claude Code
- OpenCode
- PowerShell
- Markdown
- Symlinks

## AI role

AI helped accelerate the design and cleanup of the harness in several ways:

- turning rough goals into a cleaner instruction structure,
- identifying redundancy between global and repo-local guidance,
- helping rewrite bloated instruction files into more focused versions,
- generating reusable templates for project content,
- and translating implementation choices into clearer documentation.

The value was not just faster writing. The value was faster iteration on the surrounding workflow that makes the tools usable at scale.

## Human review and safeguards

This was not an autopilot exercise.

The final structure, naming, paths, and operating rules were reviewed manually before being adopted. Decisions about what belonged in global instructions versus repo-local instructions were made deliberately, not accepted blindly.

The goal was to use AI to accelerate the thinking and drafting, while keeping control of the actual operating model.

## Outcome

The result is a cleaner multi-tool setup with several practical improvements:

- less duplicated instruction content,
- shared command definitions across tools,
- better separation between global and project-local behavior,
- easier maintenance when updating prompts or conventions,
- and a stronger foundation for future project documentation and content generation.

Just as important, the environment is easier to explain. That matters because a workflow that cannot be explained clearly usually does not scale cleanly either.

## Key takeaway

AI fluency is not just about writing prompts. It is about designing the harness around the model so the output becomes repeatable, maintainable, and useful in real work.

## Next improvement

The next step is to build on this foundation by generating stronger project write-ups, reusable content patterns, and workflow-specific documentation that can feed directly into the website and future portfolio content.

!!! question "Ready to clean up your workflow?"

    If this approach to structuring AI tools resonates, check out the [Multi-Agent Instruction Files](multi-agent-instructions.md) post or explore the [Automation](../automation/index.md) section for more practical workflow improvements.
