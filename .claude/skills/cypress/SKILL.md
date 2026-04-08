---
name: cypress
description: Cypress is an end-to-end and component testing framework for the web. Use when writing E2E specs, component tests, network stubs, custom commands, or configuring cypress.config.ts.
metadata:
  author: Anthony Fu
  version: "2026.04.08"
  source: Generated from https://github.com/cypress-io/cypress, scripts located at https://github.com/antfu/skills
---

> The skill is based on Cypress v15.13.0, generated at 2026-04-08.

Cypress runs tests directly in the browser. It supports two testing modes: **E2E** (navigating real pages via `cy.visit`) and **Component** (mounting individual components in isolation via `cy.mount`). Tests use Mocha's `describe`/`it` structure. All `cy.*` commands are asynchronous and automatically retry until assertions pass or time out.

## Core References

| Topic | Description | Reference |
|-------|-------------|-----------|
| Configuration | `defineConfig`, key options, E2E vs component setup, env vars | [core-config](references/core-config.md) |
| Test Structure | `describe`/`it` blocks, Mocha hooks, support file, spec naming | [core-test-structure](references/core-test-structure.md) |
| Commands | `cy.get`, `cy.contains`, `cy.click`, `cy.type`, `cy.visit`, traversal, assertions, aliases | [core-commands](references/core-commands.md) |

## Features

### Testing

| Topic | Description | Reference |
|-------|-------------|-----------|
| Component Testing | `cy.mount` for React, Vue, Angular, Svelte; devServer config; providers | [features-component-testing](references/features-component-testing.md) |
| Sessions | `cy.session` for caching login state across tests | [features-session](references/features-session.md) |

### Network & Data

| Topic | Description | Reference |
|-------|-------------|-----------|
| Network Stubbing | `cy.intercept` — stub responses, spy on requests, modify in-flight, wait with aliases | [features-network-stubbing](references/features-network-stubbing.md) |
| Fixtures | `cy.fixture` for loading JSON/text/binary test data | [features-fixtures](references/features-fixtures.md) |

### Extensibility

| Topic | Description | Reference |
|-------|-------------|-----------|
| Custom Commands | `Cypress.Commands.add`, child commands, TypeScript declarations | [features-custom-commands](references/features-custom-commands.md) |

## Advanced

| Topic | Description | Reference |
|-------|-------------|-----------|
| Tasks | `cy.task` for running Node.js code (DB seeding, file ops, email) from tests | [advanced-tasks](references/advanced-tasks.md) |
| Spies, Stubs & Clocks | `cy.spy`, `cy.stub`, `cy.clock`, `cy.tick` — Sinon.js integration | [advanced-spies-stubs-clocks](references/advanced-spies-stubs-clocks.md) |
