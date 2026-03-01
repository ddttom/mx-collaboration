---
title: "Git Workflow: Step Commit Process"
description: "Systematic commit workflow with linting, testing, and documentation checks"
author: Tom Cranstoun
created: 2025-12-15
modified: 2026-02-10
version: "1.0"
status: proposal
source: packages/notes/Vibe coding commit step.md
---

# Git Workflow - Step Commit Process

The "step commit" workflow ensures systematic commits with proper documentation and quality checks.

## Step-by-Step Process

### 1. Initial Commit

- Commit all current code changes
- Use descriptive commit message

### 2. Linting

- Run lint on all changed files
- Fix all linting errors
- Commit lint fixes separately

### 3. Documentation Review

- Check last modification dates of:
  - README.md
  - CLAUDE.md
  - CHANGELOG.md
- Review if these files need updates based on recent changes
- Use CHANGELOG.md to understand past changes if needed
- Review all project documents mentioned in CLAUDE.md

### 4. Documentation Updates

- Commit any documentation changes

### 5. Learning Documentation

- Update or create LEARNINGS.md
- Document everything the AI assistant struggled to understand in this session
- This helps improve future AI interactions

### 6. Project State

- Update or create PROJECTSTATE.md
- Document current state only (not historical)
- Keep this as a snapshot of current implementation status

### 7. Changelog

- Update CHANGELOG.md with all changes made
- Follow chronological order

### 8. Final Commit and Push

- Commit changelog updates
- Push all commits to remote

## Attribution

Ensure proper attribution in all git commits, especially when code is AI-assisted.
