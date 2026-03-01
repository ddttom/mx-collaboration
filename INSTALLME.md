---
title: "MX Collaboration — Installation Instructions"
description: "Machine-readable setup instructions for the mx-collaboration repository"
author: Tom Cranstoun
created: 2026-02-10
modified: 2026-02-10
version: "1.0"
status: published

prerequisites:
  required:
    - name: git
      check: "git --version"
      minimum: "2.0"
      why: "Version control and submodule operations"

  optional:
    - name: node
      check: "node --version"
      minimum: "20"
      why: "MX metadata validation scripts (if running from parent repo)"

install-steps:
  - step: 1
    name: Clone or init submodule
    command: "git submodule update --init packages/mx-collaboration"
    note: "Run from the parent hub repository root. If standalone, clone directly."
    fallback: "git clone https://github.com/ddttom/mx-collaboration"

  - step: 2
    name: Verify directory structure
    command: "ls incoming proposals accepted published chats"
    note: "All five pipeline directories should exist"

  - step: 3
    name: Read SOUL.md
    command: "cat SOUL.md"
    note: "Understand the voice, scope, and constraints before contributing"

verify:
  commands:
    - "test -f SOUL.md && echo 'SOUL.md exists'"
    - "test -f CLAUDE.md && echo 'CLAUDE.md exists'"
    - "test -f CONTRIBUTING.md && echo 'CONTRIBUTING.md exists'"
    - "test -f CHANGELOG.md && echo 'CHANGELOG.md exists'"
    - "test -d incoming && echo 'incoming/ exists'"
    - "test -d proposals && echo 'proposals/ exists'"
    - "test -d accepted && echo 'accepted/ exists'"
    - "test -d published && echo 'published/ exists'"
    - "test -d chats && echo 'chats/ exists'"
  success-criteria:
    - "All five pipeline directories exist"
    - "SOUL.md, CLAUDE.md, CONTRIBUTING.md, and CHANGELOG.md are present"

mx-environment:
  detection: "test -d ~/.mx && echo 'MX environment detected'"
  context-file: "~/.mx/repos.yaml"
  role: collaboration
---

# Installation

## For Humans

This repo is a submodule of the MX-The-Books hub. To get it:

```bash
# From the hub repo root:
git submodule update --init packages/mx-collaboration

# Or standalone:
git clone https://github.com/ddttom/mx-collaboration
```

Read SOUL.md first. Then CONTRIBUTING.md for the workflow.

## For AI Agents

Read the YAML frontmatter above. It contains structured prerequisites, install steps, and verification criteria. Follow the installme-runner action-cog procedure if available, or execute the steps in order.

Stop guessing. Start reading.
