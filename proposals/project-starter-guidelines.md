---
title: "Project Starter Guidelines"
description: "Design system and coding standards for whitelabel web application projects"
author: Tom Cranstoun
created: 2025-12-15
modified: 2026-02-10
version: "1.0"
status: proposal
source: packages/notes/Starter.md
---

# Project Starter Guidelines

## Design System

### Whitelabel Requirements

- Maintain design rules that are whitelabelable
- Define button shapes and sizes
- Define responsive breakpoints
- Use EDS shim with plusplus additions

## Coding Standards

### JavaScript

- Use simple, plain JavaScript
- **Do not add third-party libraries without explicit user permission**
- Simplicity over complexity
- Maintainability over clever code

### CSS

- CSS3 with variables
- Use master styles
- Block-level additions and changes must be aware of master styles
- Block-level components must reference master styles

### Code Organization

- Configuration variables at top of code
- **No hard-coded text or constants**
- Look for opportunities to add shared functions to core library
- Core library is shareable between backend and frontend

### Commenting

- Commenting rules must be added (TODO: Define standard)

## Project Structure

### Required Folders

#### 1. Frontend

- Frontend application code
- Should not have knowledge of which connectors are in use
- Only knows that connectors are available and the expected schema

#### 2. Backend

- Backend services and APIs
- Should not know what the frontend will do with results
- Strictly follows the schema
- UI-agnostic implementation

#### 3. Message Queue (msg-q)

- Message queue implementation using GraphQL with action commands
- Frontend communicates through this layer
- Backend processes messages through this layer

#### 4. API

- API definitions and schema
- **The schema index is where developer and AI collaborate to build APIs**
- Message queue and connectors communicate through this layer

#### 5. Core Library

- Shared utilities and functions
- Shareable between backend and frontend
- Repository for common functionality

## Frontend-Backend Separation

### Communication Principles

- Frontend only knows connectors are available and expected schema
- Backend strictly follows schema without UI knowledge
- Schema index is the collaboration point between developer and AI

## Database Approach

- Use NoSQL database approach to avoid SQL migration problems
- Provides flexibility and easier schema evolution

## Development Practices

### Git Attribution

- Ensure proper attribution in all git commits
- Especially important for AI-assisted code

### Refactoring

- Consider implementing a refactoring command
- Regularly review opportunities to improve code structure
