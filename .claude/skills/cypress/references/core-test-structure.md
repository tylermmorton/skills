---
name: core-test-structure
description: >
  Cypress test file structure using Mocha's describe/it, lifecycle hooks, support files, and
  spec naming conventions.

  Use this skill whenever: the user asks how to structure a Cypress spec, use beforeEach/afterEach,
  set up a support file, skip or focus tests with .skip/.only, organize suites, or where to put
  global setup code.

  Do NOT use for: specific cy.* command usage, configuration options, or network stubbing.
---

# Test Structure

Cypress uses Mocha as its test runner. Tests are organized in spec files using `describe` / `it` blocks.

## Spec File

```ts
// cypress/e2e/login.cy.ts
describe('Login', () => {
  beforeEach(() => {
    cy.visit('/login')
  })

  it('logs in with valid credentials', () => {
    cy.get('[data-cy="email"]').type('user@example.com')
    cy.get('[data-cy="password"]').type('password123')
    cy.get('[data-cy="submit"]').click()
    cy.url().should('include', '/dashboard')
  })

  it('shows error for invalid credentials', () => {
    cy.get('[data-cy="email"]').type('bad@example.com')
    cy.get('[data-cy="password"]').type('wrong')
    cy.get('[data-cy="submit"]').click()
    cy.contains('Invalid credentials').should('be.visible')
  })
})
```

## Hooks

```ts
describe('Suite', () => {
  before(() => { /* runs once before all tests in this block */ })
  beforeEach(() => { /* runs before each test */ })
  after(() => { /* runs once after all tests */ })
  afterEach(() => { /* runs after each test */ })
})
```

Hooks can be placed in `cypress/support/e2e.ts` to run globally across all specs.

## Support File

`cypress/support/e2e.ts` is loaded before every E2E spec. Use it for global setup:

```ts
// cypress/support/e2e.ts
import './commands'

beforeEach(() => {
  // reset app state, clear cookies, etc.
})

Cypress.on('uncaught:exception', (err) => {
  // return false to prevent test failure on uncaught exceptions from the app
  return false
})
```

## Spec File Naming

By default Cypress discovers: `cypress/e2e/**/*.cy.{js,jsx,ts,tsx}`

Component test specs: `src/**/*.cy.{js,jsx,ts,tsx}` (configurable via `specPattern`)

## Skipping and Focusing

```ts
it.skip('not ready yet', () => { /* skipped */ })
it.only('run only this test', () => { /* runs exclusively */ })
describe.skip('entire suite', () => { ... })
```

Avoid committing `.only` — Cypress lint rules flag it.

## Context Alias

`context` is an alias for `describe` — both work identically.

<!--
Source references:
- sources/cypress/cli/types/cypress.d.ts
- sources/cypress/AGENTS.md
-->
