---
name: build-project-intelligence
description: Generate or refresh durable repository intelligence artifacts in .ai/context/. Trigger when the user wants to build or refresh project intelligence.
---

Follow the canonical workflow `.ai/workflows/build-project-intelligence.md`. Do not duplicate its analysis rules here.

Harness `INIT` may create or refresh runtime instruction files such as `AGENTS.md` or `CLAUDE.md`. This skill owns the durable, portable project knowledge layer under `.ai/context/` and should avoid duplicating runtime instructions.

Use evidence-based repository analysis, preserve valid existing findings, mark uncertain claims as `Unconfirmed`, and do not expose secrets or modify application source code.
