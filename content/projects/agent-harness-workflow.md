\# AI Agent Harness Workflow



\## Why this matters



Most people talk about AI in abstract terms. This project focuses on the operational side: how to make AI useful inside a real working environment.



\## Problem



Working across multiple AI harnesses, commands, and repo contexts can become messy fast. Without structure, instructions drift, commands fork, and every tool starts behaving differently.



\## Previous workflow



Before standardizing the setup, instructions and workflows were more likely to live in multiple places and require manual updates.



That creates friction, duplication, and unnecessary rework.



\## New workflow



The current setup uses a shared command source for common slash commands and a layered AGENTS model:



\- one global instruction file for universal rules,

\- repo-local AGENTS files for project-specific behavior,

\- and shared command files symlinked into tool-native command locations.



\## Tools used



\- AGENTS.md

\- Claude Code

\- OpenCode

\- PowerShell

\- Symlinks

\- Markdown



\## AI role



AI helped structure the instruction model, reduce duplication, define the repo-local boundaries, and turn a messy setup problem into a simpler operating pattern.



\## Human review and safeguards



The final structure, paths, and operating rules were reviewed manually before being adopted.



The goal was not just to make something work, but to make it understandable and maintainable.



\## Outcome



The result is a cleaner multi-tool workflow with:



\- less duplication,

\- clearer project context,

\- shared slash commands,

\- and a simpler model for future updates.



\## Key takeaway



AI fluency is not just using models well. It is designing the surrounding workflow so the tools stay useful instead of turning into configuration sprawl.



\## Next improvement



The next step is to use this structure to generate better project templates, website content, and repeatable documentation workflows.

