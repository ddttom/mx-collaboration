---
title: "AI Assistant Configuration for MX Collaboration"
description: "Guidance for AI agents working with the MX collaboration repository"
author: Tom Cranstoun
created: 2026-02-06
modified: 2026-02-10
version: "2.0"
status: active
---

# CLAUDE.md

This file provides guidance to AI assistants when working in this repository.

## First Steps

1. Read **SOUL.md** — it defines voice, scope, and constraints
2. Read **CONTRIBUTING.md** — it defines the document workflow
3. Check **CHANGELOG.md** — it shows what has changed recently

## Repository Purpose

MX Collaboration is the team workspace for Cog-Nova-MX Ltd. It tracks documents through a structured pipeline from raw ideas to published content. This is where humans work before content becomes canonical.

## Document Pipeline

```text
incoming/  →  proposals/  →  accepted/  →  published/
 (raw)        (formal)       (approved)    (final)
```

- **incoming/** — Raw ideas, brain dumps, unstructured drafts. No format required.
- **proposals/** — Formalised proposals using TEMPLATE.md. Ready for review.
- **accepted/** — Approved proposals. Decisions to proceed.
- **published/** — Final authoritative content.
- **chats/** — Discussion logs and meeting notes preserving decision context.

## MX Conventions

### Frontmatter

Every document must have YAML frontmatter with at minimum:

```yaml
---
title: "Document Title"
description: "What this document is about"
author: Tom Cranstoun
created: YYYY-MM-DD
modified: YYYY-MM-DD
version: "1.0"
status: draft | proposal | accepted | published
---
```

When migrating content from other sources, add a `source:` field for provenance.

### File Naming

- Use kebab-case: `backend-architecture-guidelines.md`
- No spaces, no special characters, no version numbers in filenames
- Descriptive names that a machine can parse

### Templates

- Use `proposals/TEMPLATE.md` for new proposals
- Use `chats/TEMPLATE.md` for chat logs and meeting notes

### Workflow Rules

- **Move, do not copy** — Documents progress through the pipeline by moving between directories
- **Frontmatter on everything** — Machine-readability is not optional
- **Nothing here is Canon** — Content becomes canonical only when promoted to mx-canon in the parent repo

## Coding Standards (from Project Notes)

These standards apply to any code developed through this collaboration:

### JavaScript

- Use simple, plain JavaScript
- Do not add third-party libraries without explicit permission
- Simplicity over complexity, maintainability over cleverness
- Configuration variables at top of code, no hard-coded constants

### Architecture

- Frontend-backend separation via message API
- Design the message API before implementing backend code
- Multi-tenant, UI-agnostic backend
- Thread-safe and injection-proof code
- NoSQL database approach to avoid migration problems

### Git Workflow

Use the step commit process:

1. Commit code changes
2. Run lint, fix errors, commit lint fixes separately
3. Review and update README.md, CLAUDE.md, CHANGELOG.md
4. Update LEARNINGS.md with session insights
5. Update CHANGELOG.md
6. Final commit and push with proper attribution

## Architecture

```text
mx-collaboration/
├── SOUL.md              # Identity and voice
├── CLAUDE.md            # This file — AI guidance
├── README.md            # Human overview
├── CONTRIBUTING.md      # Workflow guide
├── CHANGELOG.md         # Change history
├── INSTALLME.md         # Machine-readable setup
├── incoming/            # Raw ideas
├── proposals/           # Formalised proposals
│   └── TEMPLATE.md
├── accepted/            # Approved proposals
├── published/           # Final content
└── chats/               # Discussion logs
    └── TEMPLATE.md
```

## Important Context

- This repo is a git submodule of the MX-The-Books hub repository
- The parent repo contains mx-canon (the single source of truth)
- Follow the MX principles documented in the parent's PRINCIPLES.md
- All commits should include proper attribution for AI-assisted work

## For AI Agents

When working in this repo:

1. Check which directory you are in — this is a submodule with its own git history
2. Follow the document pipeline — do not skip stages
3. Add frontmatter to any new file you create
4. Use kebab-case for all new filenames
5. Read SOUL.md for voice and constraints before writing prose
