---
title: Backend Architecture Guidelines
description: Core principles for modular, thread-safe, multi-tenant backend architecture
author: Tom Cranstoun
created: 2025-12-15
modified: 2026-02-11
version: 1.0
status: proposal
source: packages/notes/Vibe coding backend.md
---

# Backend Architecture Guidelines

## Core Principles

### Architecture Design

The backend must be highly modular and communicate via a simple message queue with a structured message API. **Design the message API before implementing any backend code.**
easy

### Code Requirements

- All backend code must be **thread-safe**
- All backend code must be **injection-proof**
- Backend may share the core library with the frontend
- Backend should be **multi-tenant** and **UI-agnostic**
- Communication via message API with structured JSON

## Multi-Tenancy

### Database Isolation

- Each tenant has a separate database (prevents data leakage)
- Each tenant has a global Config JSON object for:
  - Configuration settings
  - Whitelabel overrides

### Access Control

- Multiple owners possible per tenant
- One admin owner can delegate access to secondary owners
- Admin functions restricted to tenant owners only
- Developer admin can be promoted to super admin on any tenant
- All super admin actions require full audit logging

## Prototype Development Strategy

### Initial Implementation

1. Create dummy backend functions returning "to be implemented"
2. Define full set of actions in schema (the index IS the schema)
3. Mark each action with #TODO
4. Initial implementation returns "to be implemented" message

### Iterative Development

- Developer focuses on one backend function at a time
- Separate instructions provided for each function implementation

## Logging Architecture

### Central Error Log

- All API/connector/agent errors logged centrally

### Tenant Log

- All action messages logged to tenant-specific log
- Logs cycled daily at midnight
- Logs retained for one week

### Admin Log

- Separate action channel for tenant and developer administration
- Dashboard and administration commands
- Full audit trail for admin operations

## Connector System

### Connector Rules

- Connectors used by backend only (not frontend)
- Connectors can only execute if tenant has subscribed
- Return "not subscribed" error if tenant lacks subscription
- Error should be passed back to caller
- All connectors described in the index

### Data Transformation

- Connectors must use data transformation objects
- Enables tracing and debugging capabilities

### Error Handling

- Developer implements route for handling "not subscribed" errors
- Produces user-friendly messaging to end users

## Security

### Input Validation

- All actions must be checked for SQL injections
- All actions must be checked for bad actor intents
- Never trust user input

## Function State Tracking

### State Management

Maintain an index of all backend functions with their current state:

- **dummy** - Placeholder implementation
- **erroring** - Has known errors
- **under dev** - Actively being developed
- **potential release** - Ready for testing consideration
- **under user test** - Being tested by users
- **release** - Deployed to production
- **reverted** - Rolled back from production

## Build and Deployment

### Environment Structure

Create separate deployment environments:

- Local Dev
- Local Test
- Local QA
- Hosted Test
- Hosted Live

### Build Process

- Maintain folder of potential backend code
- Build process allows developer to select functions for release
- Track and release functions according to user requests
- Selective deployment based on function state
