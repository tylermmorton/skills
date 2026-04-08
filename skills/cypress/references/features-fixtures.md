---
name: features-fixtures
description: >
  cy.fixture for loading static test data (JSON, text, binary) from the fixtures folder and
  using it in tests or as cy.intercept stub responses.

  Use this skill whenever: the user asks about cy.fixture, loading test data from files,
  fixture files, using fixtures with intercept stubs, or organizing test data in
  cypress/fixtures/.

  Do NOT use for: cy.readFile (reading arbitrary files outside fixtures), cy.task (Node-side
  file operations), or general network stubbing without fixture data.
---

# Fixtures

Fixtures are static test data files stored in `cypress/fixtures/`. Use `cy.fixture()` to load them.

## Loading Fixtures

```ts
// Load JSON (auto-parsed)
cy.fixture('users.json').then((users) => {
  expect(users).to.have.length(3)
})

// Load as alias for reuse
cy.fixture('users.json').as('users')
cy.get('@users').then((users) => { /* ... */ })

// Load in beforeEach
beforeEach(function () {
  cy.fixture('product.json').as('product')
})

it('uses fixture', function () {
  // use function() (not arrow) to access this.product
  cy.wrap(this.product.name).should('eq', 'Widget')
})

// Load text file
cy.fixture('data.csv', 'utf8').then((csvString) => { /* ... */ })

// Load binary (base64)
cy.fixture('logo.png', 'base64').then((base64) => { /* ... */ })
```

## File Structure

```
cypress/
  fixtures/
    users.json
    product.json
    images/
      logo.png
    data/
      seed.csv
```

## With cy.intercept

```ts
// Use fixture as stub response
cy.intercept('GET', '/api/users', { fixture: 'users.json' })

// Dynamic fixture selection
cy.intercept('GET', '/api/users/*', (req) => {
  const id = req.url.split('/').pop()
  req.reply({ fixture: `users/user-${id}.json` })
})
```

## Modifying Fixture Data

```ts
cy.fixture('users.json').then((users) => {
  // Modify before use
  users[0].name = 'Modified User'
  cy.intercept('GET', '/api/users', users)
})
```

## Key Points

- Default fixtures folder: `cypress/fixtures` (configurable via `fixturesFolder`)
- JSON files are automatically parsed — no `JSON.parse` needed
- Use `this.alias` (function syntax) or `.then()` callback to access fixture data
- Fixtures are read fresh each time — modifications do not persist between tests

<!--
Source references:
- sources/cypress/cli/types/cypress.d.ts (fixture method ~line 1444)
-->
