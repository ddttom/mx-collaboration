---
title: "MX Collaboration Changelog"
description: "Chronological record of notable changes to the mx-collaboration repository"
author: Tom Cranstoun
created: 2026-02-06
modified: 2026-02-10
version: "1.0"
status: active
---

# Changelog

All notable changes to this repository are documented in this file.

## 2026-02-10 — Brainstorm Migration

### Added

- **incoming/brainstorm.md** — The idea garden, moved from repo root. Simplified frontmatter (info cog, no registry entry).

---

## 2026-02-10 — MX OS Onboarding

### Added

- **SOUL.md** — Identity document defining voice, scope, and constraints
- **CHANGELOG.md** — This file
- **INSTALLME.md** — Machine-readable installation instructions
- **incoming/** directory with 9 notes migrated from packages/notes
- **accepted/** and **published/** directories (completing the workflow pipeline)
- Coding standards and architecture guidelines absorbed into CLAUDE.md

### Changed

- **CLAUDE.md** — Replaced placeholder with full MX-aware configuration (v2.0)
- **README.md** — Fixed stale link from mx-principles.md to PRINCIPLES.md

### Migrated from packages/notes

Content from the notes repository triaged into the collaboration pipeline:

**proposals/** (4 documents — mature, actionable):

- project-starter-guidelines.md
- backend-architecture-guidelines.md
- step-commit-workflow.md
- expense-tracking-requirements.md

**incoming/** (9 documents — raw notes, brainstorms):

- mx-card-scenarios.md
- book-ideas-registry-qr-cards.md
- urgent-action-items.md
- mx-contact-details.md
- mx-beyond-html.md
- restructuring-notes.md
- business-engagement-notes.md
- development-todo.md
- urgent-repo-improvements.md

All files renamed to kebab-case with `source:` field in frontmatter for provenance.

## 2026-02-09 — Frontmatter Audit

### Changed

- Added YAML frontmatter to all existing markdown files (CLAUDE.md, README.md, CONTRIBUTING.md, templates)

## 2026-02-06 — Initial Setup

### Added

- Repository created with contribution workflow
- CONTRIBUTING.md with staged document pipeline
- proposals/TEMPLATE.md for formalised proposals
- chats/TEMPLATE.md for discussion logs
- MX metadata system (.mx.yaml.md files)
