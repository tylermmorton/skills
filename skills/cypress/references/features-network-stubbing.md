---
name: features-network-stubbing
description: >
  cy.intercept for intercepting HTTP requests — stub responses, spy on real network calls,
  modify requests/responses in flight, and wait for requests with aliases.

  Use this skill whenever: the user asks about mocking API calls, stubbing responses, cy.intercept,
  waiting for network requests with cy.wait('@alias'), simulating errors or slow responses,
  modifying request headers/bodies, or anything related to network control in Cypress tests.

  Do NOT use for: cy.request (direct HTTP requests from tests), cy.fixture (loading fixture files
  without network stubbing), or general assertion patterns.
---

# Network Stubbing (`cy.intercept`)

`cy.intercept` intercepts HTTP requests at the network layer. Use it to stub responses, spy on real requests, or modify headers/bodies in flight.

## Basic Stubbing

```ts
// Stub with static response
cy.intercept('GET', '/api/users', { fixture: 'users.json' })
cy.intercept('POST', '/api/login', { statusCode: 200, body: { token: 'abc' } })

// Stub with inline body
cy.intercept('GET', '/api/profile', {
  statusCode: 200,
  body: { id: 1, name: 'Jane' },
  headers: { 'x-custom': 'value' },
  delay: 500,
})

// Stub all requests to a path regardless of method
cy.intercept('/api/health', { statusCode: 200 })
```

## Route Matching

```ts
// Exact string
cy.intercept('GET', '/api/users')

// Glob pattern
cy.intercept('GET', '/api/users/*')

// Regex
cy.intercept('GET', /\/api\/users\/\d+/)

// RouteMatcher object (most flexible)
cy.intercept({
  method: 'GET',
  url: '/api/users',
  query: { page: '1' },
  headers: { authorization: /Bearer .+/ },
})
```

## Aliasing and Waiting

```ts
cy.intercept('GET', '/api/users').as('getUsers')

cy.visit('/users')

// Wait for the request to complete
cy.wait('@getUsers')

// Assert on the interception
cy.wait('@getUsers').then((interception) => {
  expect(interception.response?.statusCode).to.eq(200)
  expect(interception.response?.body).to.have.length(3)
})

// Shorthand assertion on response
cy.wait('@getUsers').its('response.statusCode').should('eq', 200)
cy.wait('@getUsers').its('response.body.length').should('be.gt', 0)
```

## Request Handler (Modify in Flight)

```ts
// Inspect/modify request before it's sent
cy.intercept('POST', '/api/login', (req) => {
  req.body.timestamp = Date.now()  // mutate request body
  req.continue()                   // let it pass through to server
})

// Inspect/modify response
cy.intercept('GET', '/api/users', (req) => {
  req.continue((res) => {
    res.body = res.body.map((u: any) => ({ ...u, role: 'admin' }))
  })
})

// Reply immediately (no network request)
cy.intercept('GET', '/api/data', (req) => {
  req.reply({ statusCode: 200, body: { mocked: true } })
})

// Simulate network error
cy.intercept('GET', '/api/flaky', { forceNetworkError: true })
```

## Fixtures

```ts
// Load from cypress/fixtures/users.json
cy.intercept('GET', '/api/users', { fixture: 'users.json' })

// Fixture with status code
cy.intercept('GET', '/api/users', {
  statusCode: 200,
  fixture: 'users.json',
})
```

## Multiple Intercepts / Middleware

```ts
// Middleware intercepts (run in order) — use { middleware: true }
cy.intercept({ url: '/api/**', middleware: true }, (req) => {
  req.headers['authorization'] = `Bearer ${token}`
  // no req.continue() needed for middleware — it falls through automatically
})

cy.intercept('GET', '/api/users', { fixture: 'users.json' }).as('getUsers')
```

## Spy on Real Requests (No Stubbing)

```ts
cy.intercept('GET', '/api/users').as('getUsers')
cy.visit('/users')
cy.wait('@getUsers')  // just records the request — does not block or stub
```

## Key Points

- `cy.intercept` must be called **before** the request is made (usually before `cy.visit`)
- Aliases are reset before each test (test isolation)
- Later `cy.intercept` calls for the same route take precedence unless `middleware: true`
- Use `cy.wait('@alias')` instead of `cy.wait(ms)` — it's deterministic

<!--
Source references:
- sources/cypress/cli/types/tests/net-stubbing-tests.ts
- sources/cypress/packages/net-stubbing/AGENTS.md
-->
