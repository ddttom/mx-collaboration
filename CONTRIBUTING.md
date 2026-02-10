---
title: "Contributing to MX Collaboration"
description: "Structured workflow guide for handling documents from initial ideas to final publication"
author: Tom Cranstoun
created: 2026-02-06
modified: 2026-02-09
version: "1.0"
status: active
---

# Contributing to MX Collaboration

This repository is designed to facilitate collaboration between team members. We use a structured workflow for handling documents from initial ideas to final publication.

## Directory Structure & Workflow

### 1. Incoming (`incoming/`)

- **Purpose**: This is the sandbox for new ideas, rough drafts, and brain dumps.
- **Action**: Create a new file here when you have an idea but strictly need to just get it down. No specific format is required.
- **Next Step**: When an idea matures, move it to `proposals/` and format it using the template.

### 2. Proposals (`proposals/`)

- **Purpose**: Formalized suggestions ready for team review.
- **Action**:
    1. Create a file here (or move from `incoming/`).
    2. **MUST** use the [Template](proposals/TEMPLATE.md).
    3. Open a Pull Request (or initiate a discussion) to get feedback.
- **Next Step**: Once reviewed and approved, the proposal moves to `accepted/`.

### 3. Accepted (`accepted/`)

- **Purpose**: Proposals that have been green-lit by the team.
- **Action**: Move the file here after approval. This represents a decision to proceed or a record of agreement.

### 4. Published (`published/`)

- **Purpose**: Final, "live" documentation, policies, or content.
- **Action**: When work is completed based on an accepted proposal, the final artifacts reside here.

### 5. Chats (`chats/`)

- **Purpose**: A record of important discussions, chat logs, and meeting notes.
- **Action**: Save logs here using the [Chat Template](chats/TEMPLATE.md) to keep a history of decision-making context.

## How to Contribute

1. **Fork & Branch**: Create a branch for your new content.
2. **Add Content**: Place your file in the appropriate directory (`incoming/` for new ideas, `proposals/` for formal RFCs).
3. **Pull Request**: Submit a PR to merge your changes.
4. **Review**: Team members will review and comment.
