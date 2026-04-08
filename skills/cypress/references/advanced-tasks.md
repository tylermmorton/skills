---
name: advanced-tasks
description: >
  cy.task for running arbitrary Node.js code (database access, file I/O, external services) from
  within browser-side Cypress tests, via tasks registered in setupNodeEvents.

  Use this skill whenever: the user asks about cy.task, running Node.js code in Cypress, database
  seeding or cleanup in tests, file operations during tests, email testing, or anything requiring
  Node.js APIs that aren't available in the browser. Trigger for "how do I access the database
  from a Cypress test" or "how do I seed test data".

  Do NOT use for: cy.exec (running shell commands), cy.readFile/cy.writeFile (simple file reads),
  or cy.intercept (network stubbing without Node involvement).
---

# Tasks (`cy.task`)

`cy.task` runs a function in the Cypress Node.js process (the server side), enabling access to databases, the filesystem, external services, and other Node-only APIs from within browser-side test code.

## Registration

```ts
// cypress.config.ts
import { defineConfig } from 'cypress'
import { db } from './cypress/tasks/db'

export default defineConfig({
  e2e: {
    setupNodeEvents(on, config) {
      on('task', {
        // Seed database
        async seedUsers(users: User[]) {
          await db.user.createMany({ data: users })
          return null  // tasks must return a value (null is fine)
        },

        // Clear database
        async clearDb() {
          await db.user.deleteMany()
          return null
        },

        // Read file
        readFileMaybe(filename: string) {
          if (require('fs').existsSync(filename)) {
            return require('fs').readFileSync(filename, 'utf8')
          }
          return null
        },

        // Log to terminal (useful for debugging)
        log(message: string) {
          console.log(message)
          return null
        },
      })

      return config
    },
  },
})
```

## Calling from Tests

```ts
// Invoke a task
cy.task('seedUsers', [{ name: 'Alice', email: 'alice@example.com' }])

// Task with return value
cy.task('readFileMaybe', 'cypress/downloads/report.pdf')
  .then((content) => {
    expect(content).to.include('Report')
  })

// Typed return
cy.task<User[]>('getUsers').should('have.length', 3)

// Increase timeout for slow tasks
cy.task('runMigrations', null, { timeout: 30000 })
```

## Common Patterns

### Database seeding

```ts
// cypress/support/e2e.ts
beforeEach(() => {
  cy.task('clearDb')
})

// Individual test
it('shows users', () => {
  cy.task('seedUsers', [{ name: 'Jane', role: 'admin' }])
  cy.visit('/users')
  cy.contains('Jane').should('be.visible')
})
```

### Email testing (mailhog/mailpit)

```ts
on('task', {
  async getLastEmail(to: string) {
    const res = await fetch(`http://localhost:8025/api/v2/messages?limit=1`)
    const data = await res.json()
    return data.items[0] ?? null
  },
})

// In test
cy.task('getLastEmail', 'user@example.com').then((email) => {
  const resetLink = email.Content.Body.match(/https?:\/\/\S+/)[0]
  cy.visit(resetLink)
})
```

### File downloads

```ts
on('task', {
  deleteFile(filePath: string) {
    const fs = require('fs')
    if (fs.existsSync(filePath)) fs.unlinkSync(filePath)
    return null
  },
  fileExists(filePath: string) {
    return require('fs').existsSync(filePath)
  },
})

// In test
const downloadPath = 'cypress/downloads/report.csv'
cy.task('deleteFile', downloadPath)
cy.get('[data-cy="download-btn"]').click()
cy.task('fileExists', downloadPath).should('eq', true)
```

## Key Points

- Tasks run in Node.js — no DOM, `cy.*` commands, or browser APIs available
- Every task must return a value (or Promise resolving to a value); return `null` when there's nothing to return
- Default timeout: `taskTimeout` (60 seconds) — override per call with `{ timeout: ms }`
- Tasks cannot call other `cy.*` commands
- Organize tasks in separate files under `cypress/tasks/` and import them in `setupNodeEvents`

<!--
Source references:
- sources/cypress/cli/types/cypress.d.ts (task method ~line 2227)
-->
