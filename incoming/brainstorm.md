---
title: "The Idea Garden"
description: "Unbuilt features, unwritten content, and unexplored opportunities in the MX recursive loop"
author: Tom Cranstoun and Maxine
created: 2026-02-10
modified: 2026-02-10
version: "1.0"
status: draft
source: BRAINSTORM.md (repo root)
tags: [brainstorm, ideas, roadmap, recursive-loop, seed, unbuilt]
---

# The Idea Garden

MX OS improves The Gathering. The Gathering improves the book. The book improves the company. The company improves MX OS. It is a recursive loop — and every idea seeds the next revolution.

This file captures what has not been built yet. Not what-comes-next (that cog tracks priorities and action plans). This is the garden where ideas grow before they become cogs, chapters, demos, or products.

---

## The Recursive Loop

```text
    ┌─────────────┐
    │   MX OS     │ ← Company needs drive new OS features
    └──────┬──────┘
           │ builds standards
           ▼
    ┌─────────────┐
    │ The Gathering│ ← OS innovations become open specs
    └──────┬──────┘
           │ provides content
           ▼
    ┌─────────────┐
    │  The Book   │ ← Specs become chapters and patterns
    └──────┬──────┘
           │ creates authority
           ▼
    ┌─────────────┐
    │ The Company │ ← Authority drives partnerships and revenue
    └──────┬──────┘
           │ funds and pressures
           └──────────────────→ back to MX OS
```

**Right now:** The strongest energy flows from Company → MX OS. Business pressures — the CMS Summit demo, partner onboarding, client delivery, and the investor pitch — are generating real features faster than any theoretical planning could.

---

## Status Key

- **seed** — Raw idea. Not explored. Just captured.
- **sprouting** — Being explored. Some thinking done. Not yet built.
- **ready** — Explored enough to promote. Next step: create a cog, write the chapter, build the feature.

---

## MX OS Ideas

Ideas that feed: **MX OS → The Gathering** (building the OS generates new standards)

### The `mx` CLI Tool

**Status:** seed
**Feeds:** MX OS → Gathering

Every action-cog references `mx` commands (`mx install`, `mx cog registry`, `mx uber`). None of these commands exist as executable software yet. The action-cogs are runbooks — AI agents execute them by reading the instructions. But a native `mx` CLI could make the same commands work without an AI agent.

Question: should `mx` be a shell tool, a Node.js tool, or should the runbook model be the only runtime? Building a CLI tool might violate "the AI agent IS the runtime."

---

### Cog Signing and COG Trust

**Status:** seed
**Feeds:** MX OS → Gathering

The COG (Certificate of Genuineness / Contract of Governance) is documented but not implemented. Cogs have no cryptographic signatures. There is no way to verify that a cog has not been tampered with, who published it, or whether it meets a compliance level.

The spec has compliance levels 1-5 and trust fields. None are enforced.

---

### Hosted Cog Registry

**Status:** seed
**Feeds:** MX OS → Gathering → Company

The cog registry is a local file (`cog-registry.cog.md` + `mx-reginald/index.json`). There is no web-accessible registry where anyone can discover, search, or contribute cogs. A hosted registry would be the npm of cogs.

Revenue potential: free for public cogs, paid for private/enterprise cog hosting.

---

### Cog Visibility Implementation

**Status:** seed
**Feeds:** MX OS

The spec defines visibility levels (local, private, shared, hosted) but they are not enforced. All cogs are effectively local to whoever has the repo. Shared and hosted modes need infrastructure.

---

### IPC Between Action-Cogs

**Status:** seed
**Feeds:** MX OS

Action-cogs can invoke other action-cogs (the `invokes` field). But there is no standard protocol for passing data between them. How does the output of one action-cog become the input of another? The asking-for-help cog documents agent-to-agent delegation, but the plumbing does not exist.

---

### MX Boot Implementation

**Status:** sprouting
**Feeds:** MX OS

The five-layer boot model is documented (bootloader → init → routing → execution → IPC). `$MX_HOME` and `CLAUDE.md` implement the first two layers. Routing, execution, and IPC are conceptual. Making `mx-boot/` a real init system would bring MX OS from "conventions" to "operational system."

---

### INSTALLME.md as Gathering Proposal

**Status:** seed
**Feeds:** MX OS → Gathering

INSTALLME.md is currently an MX convention. It could become a Gathering standard — a formal spec for machine-readable installation documents that any project can adopt. The convention-to-standard pipeline: MX invents it, proves it works, The Gathering standardises it.

---

### `$MX_HOME` Multi-Machine Sync

**Status:** seed
**Feeds:** MX OS

`$MX_HOME` describes one machine. But Tom works across multiple machines. How does machine.yaml, repos.yaml, and user.yaml stay in sync? Git-backed `$MX_HOME`? Cloud sync? The personal cog concept suggests the answer, but the implementation does not exist.

---

## The Gathering Ideas

Ideas that feed: **Gathering → Book** (standards work generates book content)

### The Gathering Website

**Status:** seed
**Feeds:** Gathering → Book → Company

The Gathering has no online presence. No website, no GitHub org, no community forum. For an independent standards body, visibility is everything. The W3C has a website. The Gathering needs one.

---

### First External Cog Adopter

**Status:** seed
**Feeds:** Gathering → Company

No one outside Cog-Nova-MX has created a cog. The format is documented, the spec is MIT licensed, but adoption is zero. The first external adopter validates the entire model. Who could it be? A Boye & Company member? A CMS vendor? An AI agent framework?

---

### Gathering Governance Documents

**Status:** seed
**Feeds:** Gathering

The Gathering is described but not constituted. No charter, no membership model, no contribution guidelines, no decision-making process. For it to be credible as an independent standards body, these must exist before external members join.

---

### Cog Metadata in HTML Meta Tags

**Status:** sprouting
**Feeds:** Gathering → Book

The companion web concept embeds cog metadata in HTML head. The exact format is not standardised. Which meta tags? What namespace? How does an AI agent discover a cog from an HTML page? This is a Gathering spec waiting to be written — and a book chapter waiting to be updated.

---

## Book Ideas

Ideas that feed: **Book → Company** (book content creates authority)

### Explaining MX OS Blog Post

**Status:** sprouting
**Feeds:** Book → Company

A standalone blog post that explains MX OS to someone who has never heard of it. Not the book. Not the cogs. A single page that answers: what is MX OS, why should I care, and how do I start? Published on allabout.network.

---

### INSTALLME.md Blog Post

**Status:** seed
**Feeds:** Book → Company → Gathering

The INSTALLME.md concept is compelling enough for a standalone blog post. "Your AI agent is hallucinating through your README. Here's the fix." Developer audience. Practical. Shareable.

---

### Phrasebook as Brand Voice Guide

**Status:** seed
**Feeds:** Book → Company

The MX phrasebook is not just internal culture — it is brand voice. "Design for both," "Stop guessing. Start reading," "The companion web for machines" — these are marketing messages. The phrasebook could become the voice section of a brand guide.

---

### CMS Summit Talk Content

**Status:** sprouting
**Feeds:** Book → Company

The CMS Summit (12 May 2026) needs a talk. The book provides the content. But a talk is not a book chapter — it needs a narrative arc, demos, and a call to action. What is the story? What do we show? What do we ask the audience to do?

---

### Case Study from Client Work

**Status:** seed
**Feeds:** Book → Company

Audit reports and proposals for real clients generate real-world evidence. A sanitised case study — "we audited this site, found these patterns, improved these metrics" — is the most credible content the book can contain.

---

## Company Ideas

Ideas that feed: **Company → MX OS** (business needs drive features)

### CMS Summit Live Demo

**Status:** sprouting
**Feeds:** Company → MX OS

The CMS Summit needs a live demo, not just slides. What do we show? Options: a cog being read by an AI agent in real time, the companion web scanning a QR code, a personal cog matching user preferences, INSTALLME.md in action. Each demo forces a feature to actually work.

---

### MX App (Product)

**Status:** seed
**Feeds:** Company → MX OS

MX-App is listed as a Canon initiative but has no content. What is the MX App? A web dashboard for cog management? A mobile companion for QR scanning? A developer tool for cog creation? The business model needs a product. The product needs definition.

---

### Partner SDK / Onboarding Kit

**Status:** seed
**Feeds:** Company → Gathering → MX OS

Partners need a way to adopt MX without reading the entire book. An SDK or onboarding kit — "here's how to add cog metadata to your CMS in 30 minutes" — would accelerate partnerships and prove the standard is practical.

---

### Pricing Model for MX OS Services

**Status:** seed
**Feeds:** Company

Tom's MX salary is £0. The company needs revenue. What is the service model? Consulting (audits + implementation)? Licensing (hosted registry)? Training (certification)? A pricing model forces clarity on what Cog-Nova-MX actually sells.

---

### Investment Deck as Cog

**Status:** seed
**Feeds:** Company → MX OS

The pitch deck is a PowerPoint. What if the investment narrative were a cog? YAML frontmatter with financials, team, market size. Markdown with the story. A cog that pitches itself. Meta, but it would demonstrate the format to investors in the act of pitching.

---

### allabout.network MX Section

**Status:** sprouting
**Feeds:** Company → Book

allabout.network is Tom's publishing platform. It needs a dedicated MX section — landing page, blog posts, the explaining-MX-OS article, case studies. The section IS the public face of Cog-Nova-MX. It must exist before the CMS Summit.

---

## Cross-Loop Ideas

Ideas that feed multiple segments simultaneously.

### Action-Cogs as CMS Summit Demo Material

**Status:** sprouting
**Feeds:** MX OS → Company → Book

The action-cogs we have already built (installme-runner, script-helper, cog-registry) are live demo material. An AI agent reading an action-cog and executing it on stage demonstrates the entire MX OS model in sixty seconds.

---

### Template Repo as Onboarding Tool

**Status:** seed
**Feeds:** MX OS → Gathering → Company

`packages/mx-template-repo/` exists but is minimal. A fully-featured template repo — with INSTALLME.md, SOUL.md, CLAUDE.md, example cogs, and the phrasebook — would let anyone start an MX-aware project in minutes. The Gathering's adoption tool.

---

### Personal Cog as Product Demo

**Status:** seed
**Feeds:** MX OS → Company → Book

The personal cog concept (accessibility, interests, dietary, health) is the most emotionally compelling part of MX. A demo where someone scans a QR code and a restaurant menu adapts to their dietary cog — that sells the vision faster than any slide deck.

---

### The Recursive Loop Itself as Content

**Status:** seed
**Feeds:** Book → Company → MX OS

This brainstorm document reveals the recursive loop. The loop itself is a story worth telling — how building an OS generates standards that generate a book that generates a company that funds the OS. A blog post, a talk section, or a cog that explains the loop.

---

## Rules

1. **Anyone can add ideas.** Tom, Maxine, future team members, future partners. This is a garden, not a gate.
2. **Ideas are cheap. Cogs are expensive.** Capture everything. Promote carefully. A seed costs nothing. A cog costs maintenance.
3. **Tag the loop.** Every idea should show which segment of the recursive loop it feeds. If it feeds multiple, that is a cross-loop idea — the most valuable kind.
4. **Promote to cog when ready.** When an idea reaches "ready" status, it graduates. Create the cog, write the chapter, build the feature. Then remove it from this file.
5. **This is not a roadmap.** what-comes-next.cog.md tracks priorities and timelines. This file tracks possibilities. No deadlines. No promises. Just seeds.

---

*Cogs all the way down. Ideas all the way around.*
