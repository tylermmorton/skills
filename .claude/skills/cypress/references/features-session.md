---
name: features-session
description: >
  cy.session for caching browser session state (cookies, localStorage, sessionStorage) so login
  flows run once and are restored on subsequent tests, dramatically speeding up auth-heavy suites.

  Use this skill whenever: the user asks about cy.session, avoiding repeated login in tests,
  caching authentication state, session validation, cross-spec session persistence, or patterns
  for fast login setup in Cypress. Trigger also for "how do I stay logged in between tests".

  Do NOT use for: cy.setCookie or cy.clearCookies (individual cookie management), custom login
  command logic without session caching, or component testing authentication.
---

# Sessions (`cy.session`)

`cy.session` caches browser session state (cookies, localStorage, sessionStorage) after a setup function runs, then restores that state on subsequent calls. Eliminates repeated login flows.

## Basic Usage

```ts
Cypress.Commands.add('login', (username: string, password: string) => {
  cy.session([username, password], () => {
    // Setup: runs only once per unique session ID
    cy.visit('/login')
    cy.get('[name="username"]').type(username)
    cy.get('[name="password"]').type(password)
    cy.get('[type="submit"]').click()
    cy.url().should('include', '/dashboard')
  })
})
```

The session is identified by the first argument (string or serializable object). Same ID = restore cached state. Different ID = new session.

## With Validation

```ts
Cypress.Commands.add('login', (username: string, password: string) => {
  cy.session(
    [username, password],
    () => {
      cy.visit('/login')
      cy.get('[name="username"]').type(username)
      cy.get('[name="password"]').type(password)
      cy.get('[type="submit"]').click()
    },
    {
      validate() {
        // Runs before restoring cached session.
        // If this fails, the setup function re-runs.
        cy.getCookie('session_token').should('exist')
      },
    },
  )
})
```

## Cross-spec Persistence

```ts
cy.session('user', setupFn, {
  cacheAcrossSpecs: true,  // persist session across spec files
})
```

Without `cacheAcrossSpecs`, the session is cleared between spec files.

## In Tests

```ts
describe('Protected pages', () => {
  beforeEach(() => {
    cy.login('admin@example.com', 'password')
    cy.visit('/admin')  // navigate after restoring session
  })

  it('shows admin panel', () => {
    cy.get('[data-cy="admin-panel"]').should('be.visible')
  })
})
```

## Programmatic Login (Faster)

Skip the UI by calling the API directly in the session setup:

```ts
Cypress.Commands.add('loginViaApi', (username: string, password: string) => {
  cy.session([username, password], () => {
    cy.request('POST', '/api/login', { username, password })
      .its('body.token')
      .then((token) => {
        localStorage.setItem('auth_token', token)
      })
  })
})
```

## Key Points

- Session state stored: cookies, localStorage, sessionStorage (all origins)
- With `testIsolation: true` (default), page is cleared before each test; navigate after `cy.session()`
- Session is reset if validation fails — triggers setup again
- Different session IDs (e.g., different users) are cached independently
- Works only in E2E testing (not component testing)

<!--
Source references:
- sources/cypress/cli/types/cypress.d.ts (session method ~line 1349)
-->
