---
title: "MX Collaboration"
description: "Central hub for team collaboration, tracking documents from initial ideas to published content"
author: Tom Cranstoun
created: 2026-02-06
modified: 2026-02-10
version: "1.0"
status: active
---

# MX Collaboration

This repository serves as a central hub for team collaboration, tracking documents from initial ideas to published content.

## Workflow Overview

We follow a staged approach to documentation:

1. **Incoming**: Unstructured ideas and drafts.
2. **Proposals**: Formalized ideas for review (using templates).
3. **Accepted**: Approved proposals.
4. **Published**: Final, authoritative content.

We also maintain **Chats** for historical context of discussions.

## Getting Started

Please read [CONTRIBUTING.md](CONTRIBUTING.md) for detailed instructions on how to add and move documents through these stages.

## Directory Structure

- [`incoming/`](incoming/)
- [`proposals/`](proposals/)
- [`accepted/`](accepted/)
- [`published/`](published/)
- [`chats/`](chats/)

## MX Compliance

This repository follows MX (Machine Experience) principles for machine-readable metadata.

### Folder Metadata

Every directory contains a `.mx.yaml.md` file describing:

- **Purpose**: What the folder is for
- **Provenance**: Who created it and when
- **Relationships**: How it relates to other folders
- **AI Guidance**: Instructions for AI agents

### Available Commands

```bash
npm run mx:generate        # Generate .mx.yaml.md for new folders
npm run mx:validate        # Validate all metadata
npm run mx:hooks:install   # Install validation hooks
```

### Learn More

- [MX Principles](../../PRINCIPLES.md)
- [MX YAML Guide](../../docs/guides/for-humans/mx-yaml-md-guide.md)
