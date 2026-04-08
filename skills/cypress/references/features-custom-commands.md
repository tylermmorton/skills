---
name: features-custom-commands
description: >
  Adding custom cy.* commands with Cypress.Commands.add and extending TypeScript types via
  declaration merging, enabling reusable test helpers across specs.

  Use this skill whenever: the user asks about custom commands, Cypress.Commands.add,
  Cypress.Commands.overwrite, extending the cy object, typing custom commands in TypeScript,
  or building shared test utilities like cy.login or cy.findByTestId.

  Do NOT use for: cy.task (Node-side tasks), built-in cy.* command usage, or test file
  organization without custom command authoring.
---

# Custom Commands

Add reusable commands to avoid repetition across specs.

## Defining Commands

```ts
// cypress/support/commands.ts

// Simple command
Cypress.Commands.add('login', (email: string, password: string) => {
  cy.visit('/login')
  cy.get('[data-cy="email"]').type(email)
  cy.get('[data-cy="password"]').type(password)
  cy.get('[data-cy="submit"]').click()
  cy.url().should('include', '/dashboard')
})

// Command that operates on a subject (child command)
Cypress.Commands.add('clickByText', { prevSubject: 'element' }, (subject, text: string) => {
  cy.wrap(subject).contains(text).click()
})

// Optional subject command
Cypress.Commands.add('findByTestId', { prevSubject: 'optional' }, (subject, testId: string) => {
  if (subject) {
    return cy.wrap(subject).find(`[data-cy="${testId}"]`)
  }
  return cy.get(`[data-cy="${testId}"]`)
})
```

## TypeScript Declarations

Add types in `cypress/support/index.d.ts` or a `.d.ts` file:

```ts
// cypress/support/index.d.ts
declare namespace Cypress {
  interface Chainable {
    login(email: string, password: string): Chainable<void>
    clickByText(text: string): Chainable<JQuery<HTMLElement>>
    findByTestId(testId: string): Chainable<JQuery<HTMLElement>>
  }
}
```

Include in `tsconfig.json`:

```json
{
  "include": ["cypress/**/*.ts"]
}
```

## Overwriting Built-in Commands

```ts
Cypress.Commands.overwrite('visit', (originalFn, url, options) => {
  // Add auth headers to every visit
  return originalFn(url, {
    ...options,
    headers: {
      ...options?.headers,
      authorization: `Bearer ${Cypress.env('token')}`,
    },
  })
})
```

## Using Commands

```ts
// cypress/support/e2e.ts
import './commands'

// cypress/e2e/dashboard.cy.ts
describe('Dashboard', () => {
  beforeEach(() => {
    cy.login('user@example.com', 'password')
  })

  it('shows dashboard', () => {
    cy.findByTestId('dashboard-title').should('be.visible')
  })
})
```

## Key Points

- Import commands file in support file (`cypress/support/e2e.ts`)
- Child commands (`prevSubject: 'element'`) chain off DOM elements
- Return a Cypress chain (not a Promise) from custom commands
- Prefer `cy.session()` for login commands to avoid repeating network calls

<!--
Source references:
- sources/cypress/cli/types/cypress.d.ts (Commands interface ~line 3600)
-->
