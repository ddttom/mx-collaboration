---
title: "AI assistant configuration for mx-collaboration"
description: "Guidance for Claude Code when working with the MX collaboration repository."
author: Tom Cranstoun
created: 2026-02-06
modified: 2026-02-10
version: "1.1"
status: active
---

# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

mx-collaboration is the team workspace for MX Technologies Ltd. It tracks documents through a structured pipeline:

1. **incoming/** — Raw ideas, brain dumps, unstructured drafts
2. **proposals/** — Formalised proposals using TEMPLATE.md, ready for review
3. **accepted/** — Approved proposals, decisions to proceed
4. **published/** — Final authoritative content
5. **chats/** — Discussion logs and meeting notes preserving decision context

## MX Conventions

- **SOUL.md** — Read SOUL.md first. It defines voice, scope, and constraints for this repo.
- **Frontmatter** — Every document must have YAML frontmatter with at minimum: title, description, author, created, modified, version, status.
- **Templates** — Use `proposals/TEMPLATE.md` for proposals and `chats/TEMPLATE.md` for chat logs.
- **Move, do not copy** — Documents progress through the pipeline by moving between directories, not by duplicating.

## Important Context

- This repo is a git submodule of the main MX-The-Books hub repository
- Nothing here is Canon — content becomes canonical only when promoted to MX-Canon in the parent repo
- Follow the staged workflow described in CONTRIBUTING.md

## Architecture

```text
mx-collaboration/
├── SOUL.md              # Identity and voice
├── CLAUDE.md            # This file — AI guidance
├── README.md            # Human overview
├── CONTRIBUTING.md      # Workflow guide
├── incoming/            # Raw ideas
├── proposals/           # Formalised proposals
│   └── TEMPLATE.md
├── accepted/            # Approved proposals
├── published/           # Final content
└── chats/               # Discussion logs
    └── TEMPLATE.md
```
