---
title: "PRD: mx-editor — Collaborative Document Editor"
description: "Product requirements for mx-editor, an Electron + web server application that wraps the mx-collaboration pipeline with local AI assistance and Claude Code integration"
author: Tom Cranstoun
created: 2026-02-10
modified: 2026-02-10
version: "1.0"
status: proposal
tags: [electron, editor, collaboration, ai, git, cross-platform]
---

# PRD: mx-editor — Collaborative Document Editor

**Author:** Tom Cranstoun
**Status:** Draft
**Date:** 2026-02-10

---

## Background

The mx-collaboration repository provides a structured document pipeline (incoming → proposals → accepted → published) for Cog-Nova-MX Ltd. Staff contribute documents with YAML frontmatter, following naming conventions and templates. Currently, this workflow requires git command-line knowledge, a text editor, and familiarity with the MX conventions.

This creates friction. Not every team member is comfortable with git, markdown, or terminal workflows. Documents end up in random locations, frontmatter is missing or malformed, branch discipline breaks down, and Tom must manually chase contributions.

mx-editor solves this by wrapping the entire workflow in a visual application that enforces the pipeline, handles git transparently, and provides local AI assistance for writing — all without requiring cloud services or API keys.

## Problem

1. **Git barrier** — Staff who aren't developers struggle with clone, branch, commit, push workflows
2. **Convention drift** — Frontmatter fields are missed, filenames use spaces, documents land in wrong directories
3. **No visibility** — Tom has no dashboard to see what's in the pipeline across all staff branches
4. **No assistance** — Writing proposals from templates is mechanical but staff get stuck on structure
5. **Platform fragmentation** — Team uses Windows, Mac, and Linux; some work from tablets
6. **No automation hook** — No API for Claude Code or other tools to interact with the repo programmatically

## Proposed Solution

### Overview

mx-editor is a cross-platform application that runs in two modes:

1. **Desktop mode** — Electron app with full UI (Windows, macOS, Linux)
2. **Server mode** — Express.js web server accessible from any browser (tablets, phones, remote machines)

Both modes share identical core logic. A local AI model (Qwen2.5-1.5B) provides writing assistance without cloud dependencies. A REST API allows Claude Code and other tools to drive the application programmatically.

### Architecture

```
┌─────────────────────────────────────────────────┐
│                   mx-editor                      │
│                                                  │
│  ┌──────────┐  ┌──────────┐  ┌──────────────┐  │
│  │ Electron │  │ Express  │  │  REST API    │  │
│  │   UI     │  │  Server  │  │  (port 3141) │  │
│  │ (HTML/   │  │ (serves  │  │  Claude Code │  │
│  │  CSS/JS) │  │  same UI)│  │  + external  │  │
│  └────┬─────┘  └────┬─────┘  └──────┬───────┘  │
│       │              │               │           │
│       └──────────────┴───────────────┘           │
│                      │                           │
│              ┌───────┴────────┐                  │
│              │   Core Engine  │                  │
│              │                │                  │
│              │  ┌──────────┐  │                  │
│              │  │ Git Ops  │  │  simple-git      │
│              │  └──────────┘  │                  │
│              │  ┌──────────┐  │                  │
│              │  │ Pipeline │  │  document CRUD   │
│              │  └──────────┘  │                  │
│              │  ┌──────────┐  │                  │
│              │  │ AI Model │  │  Qwen2.5-1.5B   │
│              │  └──────────┘  │                  │
│              │  ┌──────────┐  │                  │
│              │  │ Validate │  │  frontmatter     │
│              │  └──────────┘  │                  │
│              └────────────────┘                  │
└─────────────────────────────────────────────────┘
```

### Running Modes

```bash
# Desktop mode — Electron window + API server
npm start

# Server mode — headless, browser-only (for tablets/phones/remote)
npm run serve

# Server mode with custom port
PORT=8080 npm run serve
```

---

## User Personas

### Staff Member (Contributor)

- Downloads mx-editor, runs it
- App clones mx-collaboration repo on first launch
- Auto-creates branch `staff/<firstname-lastname>` from their git identity
- Sees the pipeline as a kanban board: incoming | proposals | accepted | published
- Creates documents from templates (proposals, chats, raw notes)
- App auto-generates YAML frontmatter, enforces kebab-case filenames
- Edits markdown with side-by-side preview
- AI assists with writing: summarise, expand, check structure, suggest improvements
- Commits and pushes with one button — app builds the commit message
- Never touches a terminal

### Tom (Reviewer / Owner)

- Uses Claude Code or the mx-editor UI to review branches
- Sees all staff branches and their documents via the API or a review dashboard
- Merges branches via GitHub PR workflow (outside mx-editor) or via the API
- Can use Claude Code to automate: list all pending docs, validate frontmatter, merge ready branches

---

## Features

### F1: First-Run Setup

1. App opens to a setup screen
2. Staff enters their name (or app reads from git config)
3. App clones `https://github.com/ddttom/mx-collaboration.git` to a local directory
4. App creates and checks out branch `staff/<firstname-lastname>`
5. If branch already exists on remote, it pulls the latest
6. Setup state is saved to a local config file (`~/.mx-editor/config.json`)

### F2: Pipeline Dashboard (Main View)

A kanban-style board with 5 columns:

```
┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐
│ Incoming │ │Proposals │ │ Accepted │ │Published │ │  Chats   │
│          │ │          │ │          │ │          │ │          │
│ note-1   │ │ backend- │ │          │ │          │ │          │
│ note-2   │ │ arch.md  │ │          │ │          │ │          │
│ brain-   │ │ expense- │ │          │ │          │ │          │
│ storm.md │ │ track.md │ │          │ │          │ │          │
│          │ │          │ │          │ │          │ │          │
│ [+ New]  │ │ [+ New]  │ │          │ │          │ │ [+ New]  │
└──────────┘ └──────────┘ └──────────┘ └──────────┘ └──────────┘
```

- Each card shows: title (from frontmatter), author, modified date, status badge
- Click a card to open the editor
- Drag cards between columns to move documents through the pipeline
- Moving updates the `status` and `modified` fields in frontmatter automatically
- Bottom toolbar: Sync (pull/push), AI Chat, Settings

### F3: Document Editor

Split-pane markdown editor:

```
┌─────────────────────┬─────────────────────┐
│ YAML Frontmatter    │                     │
│ ┌─────────────────┐ │    Live Preview     │
│ │ title: ...      │ │                     │
│ │ author: ...     │ │    # Title          │
│ │ status: draft   │ │                     │
│ │ created: ...    │ │    ## Background    │
│ └─────────────────┘ │    Context text...  │
│                     │                     │
│ # Title             │    ## Problem       │
│                     │    Description...   │
│ ## Background       │                     │
│ Context text here   │                     │
│                     │                     │
│ ## Problem          │                     │
│ Description here    │                     │
│                     │                     │
└─────────────────────┴─────────────────────┘
│ [Save] [AI Assist] [Commit & Push] [Back] │
└───────────────────────────────────────────┘
```

- Frontmatter displayed as structured form fields (not raw YAML)
- Markdown body with syntax highlighting (use a lightweight highlighter, no CodeMirror — keep it simple)
- Live preview renders markdown to HTML
- Template sections shown as guide text when creating from template
- Auto-save to local file on every change

### F4: Document Creation

- "New" button in each column
- For proposals: pre-fills from TEMPLATE.md with guided sections
- For chats: pre-fills from chats/TEMPLATE.md
- For incoming: blank document with minimal frontmatter
- Filename auto-generated from title in kebab-case
- Frontmatter auto-populated: title, author (from config), created (today), modified (today), version ("1.0"), status (matches column)

### F5: Git Operations (Transparent)

All git operations happen behind the scenes:

| User action | Git operation |
|-------------|--------------|
| First launch | `git clone`, `git checkout -b staff/<name>` |
| Open app | `git pull origin main`, `git merge main` (keep branch current) |
| Save document | File written to disk (no git yet) |
| "Commit & Push" | `git add <file>`, `git commit -m "..."`, `git push origin staff/<name>` |
| Move document | `git mv`, `git commit -m "move: <file> to <stage>"`, auto-push |
| Sync button | `git pull`, `git push` |

Commit messages follow conventional format:

- `docs: add <title> to incoming`
- `docs: formalise <title> as proposal`
- `move: <title> from incoming to proposals`
- `edit: update <title>`

### F6: Local AI Assistant

**Model:** Qwen2.5-1.5B-Instruct (Apache 2.0 licence)
**Runtime:** WebLLM (WebGPU) in Electron/browser, with llama.cpp as server-mode fallback
**Size:** ~1.5 GB download on first use, cached locally

**Capabilities:**

| Command | What it does |
|---------|-------------|
| Summarise | Condense selected text to key points |
| Expand | Elaborate on a brief note with more detail |
| Structure | Reformat raw text to match the proposal template sections |
| Check | Validate document structure against template requirements |
| Suggest | Propose improvements to clarity and completeness |
| Frontmatter | Generate or fix YAML frontmatter from document content |

**UI:** A chat sidebar or floating panel. Staff types a request or selects a command. AI processes the current document context and returns suggestions. Staff accepts, rejects, or edits the suggestion before it's applied.

**Model download:** On first use, the app downloads Qwen2.5-1.5B-Instruct (~1.5 GB) to `~/.mx-editor/models/`. Progress bar shown. Model cached permanently. No network required after first download.

**Privacy:** All inference runs locally. No data leaves the device. No API keys required.

### F7: REST API (Claude Code Integration)

Express server on port 3141 (configurable). Available in both desktop and server modes.

**Authentication:** Bearer token stored in `~/.mx-editor/config.json`. Generated on first launch. Simple but sufficient for local use.

**Endpoints:**

```
GET    /api/status                    App status, current branch, repo path
GET    /api/pipeline                  All documents grouped by stage
GET    /api/pipeline/:stage           Documents in a specific stage
GET    /api/document/:stage/:filename Full document (frontmatter + body)
POST   /api/document/:stage           Create document (JSON body: title, content, template)
PUT    /api/document/:stage/:filename Update document content
DELETE /api/document/:stage/:filename Delete document
POST   /api/document/move             Move document between stages (JSON: from, to, filename)

GET    /api/git/status                Git status (branch, clean/dirty, ahead/behind)
POST   /api/git/commit                Commit staged changes (JSON: message)
POST   /api/git/push                  Push current branch
POST   /api/git/pull                  Pull from remote
POST   /api/git/sync                  Pull + push

GET    /api/branches                  List all staff branches
GET    /api/branches/:name/documents  Documents on a specific branch

POST   /api/ai/summarise              AI summarise (JSON: text)
POST   /api/ai/expand                 AI expand (JSON: text)
POST   /api/ai/structure              AI restructure to template (JSON: text)
POST   /api/ai/check                  AI validate structure (JSON: text)
POST   /api/ai/suggest                AI suggest improvements (JSON: text)
POST   /api/ai/frontmatter            AI generate frontmatter (JSON: text)
POST   /api/ai/chat                   Freeform AI chat (JSON: prompt, context)

GET    /api/validate                  Validate all documents (frontmatter, naming, structure)
GET    /api/validate/:stage/:filename Validate single document
```

**Example Claude Code usage:**

```bash
# List all pending proposals
curl http://localhost:3141/api/pipeline/proposals

# Create a new incoming note
curl -X POST http://localhost:3141/api/document/incoming \
  -H "Content-Type: application/json" \
  -d '{"title": "API redesign notes", "content": "We need to rethink the auth flow..."}'

# Move a document to proposals
curl -X POST http://localhost:3141/api/document/move \
  -d '{"from": "incoming", "to": "proposals", "filename": "api-redesign-notes.md"}'

# Ask AI to structure raw notes
curl -X POST http://localhost:3141/api/ai/structure \
  -d '{"text": "raw notes content here..."}'

# Validate everything
curl http://localhost:3141/api/validate
```

### F8: Frontmatter Validation

Automatic validation on every save and commit:

| Rule | Severity |
|------|----------|
| title field present | Error |
| description field present | Error |
| author field present | Error |
| created date valid (YYYY-MM-DD) | Error |
| modified date valid (YYYY-MM-DD) | Error |
| version field present | Warning |
| status matches directory | Error |
| filename is kebab-case | Error |
| filename has .md extension | Error |
| no spaces in filename | Error |

Validation errors block commits. Warnings are shown but don't block.

### F9: Settings

Stored in `~/.mx-editor/config.json`:

```json
{
  "user": {
    "name": "Jane Smith",
    "branch": "staff/jane-smith"
  },
  "repo": {
    "remote": "https://github.com/ddttom/mx-collaboration.git",
    "localPath": "~/mx-collaboration"
  },
  "server": {
    "port": 3141,
    "token": "generated-uuid"
  },
  "ai": {
    "model": "Qwen2.5-1.5B-Instruct",
    "modelPath": "~/.mx-editor/models/",
    "enabled": true
  },
  "editor": {
    "fontSize": 14,
    "theme": "dark",
    "autoSave": true
  }
}
```

---

## Technical Specification

### Tech Stack

| Layer | Technology | Rationale |
|-------|-----------|-----------|
| Desktop shell | Electron | Cross-platform, plain JS, no framework needed |
| Web server | Express.js | Lightweight, runs as child process or standalone |
| Git operations | simple-git | Mature, reliable, wraps native git binary |
| AI inference | WebLLM (@mlc-ai/web-llm) | WebGPU in browser/Electron, no native deps |
| AI fallback | node-llama-cpp | For server mode without WebGPU |
| Markdown rendering | marked | Lightweight, zero-dependency markdown parser |
| Syntax highlighting | highlight.js (markdown/yaml only) | Minimal, just two language packs |
| UI framework | None — plain HTML/CSS/JS | Per project requirements |
| CSS | CSS3 with custom properties | Dark/light themes via CSS variables |
| Build | electron-builder | Cross-platform packaging |

### Dependencies (Minimal)

**Production:**

- electron
- express
- simple-git
- @mlc-ai/web-llm
- marked

**Dev only:**

- electron-builder

No React. No TypeScript. No webpack. No bundler. Plain JavaScript modules.

### Project Structure

```
mx-editor/
├── package.json
├── main.js                    # Electron main process
├── preload.js                 # Security bridge (contextBridge)
├── server.js                  # Express server (runs standalone or as child)
│
├── core/                      # Shared engine (used by both Electron and Express)
│   ├── git-ops.js             # Git operations (clone, branch, commit, push)
│   ├── pipeline.js            # Document CRUD, stage management, validation
│   ├── frontmatter.js         # YAML frontmatter parse/validate/generate
│   ├── ai-engine.js           # AI model loading, inference, prompt templates
│   └── config.js              # Settings management
│
├── api/                       # REST API route handlers
│   ├── routes.js              # Express route definitions
│   ├── git-routes.js          # /api/git/* endpoints
│   ├── pipeline-routes.js     # /api/pipeline/* endpoints
│   ├── document-routes.js     # /api/document/* endpoints
│   └── ai-routes.js           # /api/ai/* endpoints
│
├── ui/                        # Frontend (served by both Electron and Express)
│   ├── index.html             # Main shell
│   ├── styles.css             # All styles (CSS custom properties for theming)
│   ├── app.js                 # App initialisation, routing
│   ├── dashboard.js           # Pipeline kanban view
│   ├── editor.js              # Document editor + preview
│   ├── ai-panel.js            # AI assistant sidebar
│   ├── settings.js            # Settings view
│   └── components/            # Reusable UI pieces
│       ├── card.js            # Document card for kanban
│       ├── modal.js           # Dialog/modal
│       ├── toast.js           # Notification toasts
│       └── toolbar.js         # Bottom toolbar
│
├── templates/                 # Document templates (copied from mx-collaboration)
│   ├── proposal.md
│   └── chat.md
│
└── CLAUDE.md                  # AI guidance for this project
```

### IPC Architecture (Electron)

```
Renderer (ui/*.js)
    │
    │ window.api.methodName(args)
    │
    ▼
Preload (preload.js)
    │
    │ ipcRenderer.invoke('channel', args)
    │
    ▼
Main Process (main.js)
    │
    │ ipcMain.handle('channel', handler)
    │
    ▼
Core Engine (core/*.js)
```

All renderer-to-main communication goes through the preload bridge. The renderer never has direct access to Node.js APIs.

### Express Server Architecture

```
Client (browser / curl / Claude Code)
    │
    │ HTTP request
    │
    ▼
Express (server.js)
    │
    │ Route handler
    │
    ▼
Core Engine (core/*.js)
```

Same core engine, different transport. The Express server imports the same `core/` modules that the Electron main process uses.

---

## Cross-Platform Support

| Platform | Mode | How |
|----------|------|-----|
| macOS | Desktop | Electron .dmg |
| Windows | Desktop | Electron .exe (NSIS installer) |
| Linux | Desktop | Electron .AppImage or .deb |
| iPad/tablet | Web | Connect to server mode via browser |
| Phone | Web | Connect to server mode via browser |
| Remote machine | Web | Connect to server mode via browser |

**Server mode for mobile/tablet:**

A team member runs `npm run serve` on any machine on the local network (or a shared server). Other team members access mx-editor from their browser at `http://<host>:3141`. Each user authenticates with the bearer token and operates on their own branch.

---

## Security

- **No cloud services** — All AI inference is local. No data leaves the device.
- **Git authentication** — Uses the system's existing git credential manager (SSH keys or HTTPS tokens). mx-editor does not store git passwords.
- **API token** — Simple bearer token for the REST API. Generated locally, never transmitted externally.
- **Context isolation** — Electron renderer process has no direct access to Node.js. All operations go through the preload bridge.
- **Input validation** — All filenames sanitised (kebab-case enforced). Frontmatter values escaped. No shell injection vectors in git operations (simple-git handles escaping).
- **No arbitrary code execution** — The AI model generates text only. No code execution from AI output.

---

## Risks and Downsides

1. **Git binary dependency** — simple-git requires git to be installed. Staff on locked-down machines may not have it. Mitigation: bundle a portable git binary, or fall back to isomorphic-git (pure JS, slower).

2. **Model download size** — 1.5 GB on first launch is significant on slow connections. Mitigation: AI features are optional. App works fully without the model. Download happens in background with progress indication.

3. **WebGPU availability** — WebLLM requires WebGPU, which isn't available on all machines (especially older Windows). Mitigation: fall back to WebAssembly (WASM) inference, which is slower but universal.

4. **Branch conflicts** — If Tom edits main while staff branches are active, merges may conflict. Mitigation: app auto-merges main into staff branch on sync. Conflicts shown in UI with manual resolution options.

5. **Single model quality** — Qwen2.5-1.5B is good but not Claude-quality. Staff may find AI suggestions underwhelming. Mitigation: position AI as "assistant for structure and drafts" not "writer". Claude Code via the API provides higher-quality assistance when available.

## Alternatives Considered

1. **Web-only (no Electron)** — Simpler distribution but loses offline capability and native file access. Staff would need a server running somewhere. Rejected because offline-first is a requirement.

2. **VS Code extension** — Would leverage an existing editor but adds VS Code as a dependency, intimidates non-technical staff, and can't run on tablets. Rejected.

3. **Cloud AI (OpenAI/Claude API)** — Higher quality but requires API keys, costs money per request, needs internet, raises data privacy concerns. Rejected per requirement for local-only, free, open-source model.

4. **LLaMA 3.x models** — Good quality but Meta's licence has usage restrictions and requires agreement to terms that may conflict with EU/USA legal constraint requirements. Rejected.

5. **React/Vue frontend** — Better component model but adds build step, bundler complexity, and framework dependency. Rejected per plain JavaScript requirement.

## Open Questions

1. **Multi-user server mode** — When multiple staff connect to the same server instance, should each get their own branch, or share one? Current assumption: each user authenticates and gets their own branch based on their identity.

2. **Offline-first sync** — Should the app queue commits when offline and batch-push when connection returns? Or require connectivity for push?

3. **Document locking** — If two staff members somehow edit the same document on different branches, how should merge conflicts be presented in the UI?

4. **Model updates** — When Qwen releases a better model, how should mx-editor handle model upgrades? Auto-download? Prompt user?

5. **Review workflow** — Should mx-editor include a review UI for Tom, or is GitHub PR review sufficient? Adding review would increase scope significantly.

---

## Milestones

### M1: Skeleton (Week 1-2)

- Electron + Express dual-mode app that launches
- Plain HTML/CSS shell with kanban column layout
- Settings screen with user config
- REST API skeleton with health check endpoint
- No git, no AI yet

### M2: Git Integration (Week 3-4)

- First-run clone workflow
- Auto-branch creation (`staff/<name>`)
- Document CRUD (create, read, update, delete)
- Pipeline movement (drag between columns)
- Commit and push operations
- Sync (pull + push)
- All git operations via REST API

### M3: Editor (Week 5-6)

- Split-pane markdown editor with live preview
- Frontmatter form fields (structured, not raw YAML)
- Template-based document creation
- Frontmatter validation with error/warning display
- Auto-save

### M4: AI Assistant (Week 7-8)

- Qwen2.5-1.5B model download and caching
- WebLLM integration in Electron renderer
- AI command palette (summarise, expand, structure, check, suggest, frontmatter)
- Chat sidebar for freeform AI interaction
- AI endpoints in REST API

### M5: Polish & Distribution (Week 9-10)

- Dark/light theme toggle
- Cross-platform packaging (macOS, Windows, Linux)
- Server mode for tablet/phone access
- Documentation (README, CLAUDE.md for the project)
- Testing on all platforms

---

## Success Criteria

1. A non-technical staff member can install mx-editor, clone the repo, create a proposal, and push it — without opening a terminal
2. Tom can see all staff contributions via the REST API from Claude Code
3. The app works offline (except push/pull)
4. AI assists with writing without any cloud dependency or API key
5. The same codebase runs on Windows, macOS, and Linux as a desktop app, and serves a web UI for tablets
6. All documents created through mx-editor pass frontmatter validation automatically

---

*This PRD follows the mx-collaboration proposal template. Move to accepted/ when approved.*
