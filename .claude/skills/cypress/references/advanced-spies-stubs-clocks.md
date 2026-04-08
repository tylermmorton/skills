---
name: advanced-spies-stubs-clocks
description: >
  cy.spy, cy.stub, and cy.clock for tracking function calls, replacing implementations, and
  controlling browser time (setTimeout, setInterval, Date) in Cypress tests via Sinon.js.

  Use this skill whenever: the user asks about spying on or stubbing a function in Cypress,
  cy.spy, cy.stub, cy.clock, cy.tick, mocking window methods (e.g. window.open), freezing or
  advancing time, or using Sinon assertions (calledOnce, calledWith, etc.) in Cypress.

  Do NOT use for: cy.intercept (HTTP network stubbing), Jest/Vitest mock functions (different
  API), or general assertion patterns without spy/stub/clock involvement.
---

# Spies, Stubs, and Clocks

Cypress exposes Sinon.js for spying on and stubbing functions, and a clock API for controlling time.

## Spies (`cy.spy`)

Spy wraps a function to record calls without altering behavior.

```ts
const app = { greet: (name: string) => `Hello, ${name}!` }

cy.spy(app, 'greet').as('greet')

app.greet('Alice')

cy.get('@greet').should('have.been.calledOnce')
cy.get('@greet').should('have.been.calledWith', 'Alice')
cy.get('@greet').should('have.been.calledWithExactly', 'Alice')

// Inspect call count manually
cy.get('@greet').then((spy: any) => {
  expect(spy.callCount).to.eq(1)
  expect(spy.returnValues[0]).to.eq('Hello, Alice!')
})
```

## Stubs (`cy.stub`)

Stub replaces a function with a no-op (or custom implementation) that records calls.

```ts
const app = { save: async (data: object) => { /* real API call */ } }

cy.stub(app, 'save').resolves({ id: 42 }).as('save')

await app.save({ name: 'test' })

cy.get('@save').should('have.been.calledOnce')
cy.get('@save').should('have.been.calledWith', { name: 'test' })
```

### Common Sinon stub behaviors

```ts
const stub = cy.stub()

stub.returns(42)                  // return value
stub.resolves({ data: 'ok' })     // return resolved promise
stub.rejects(new Error('fail'))   // return rejected promise
stub.callsFake((arg) => arg * 2)  // custom implementation
stub.onFirstCall().returns(1)     // different behavior on specific call
stub.onSecondCall().returns(2)
stub.throws(new Error('oops'))    // throw error
```

### Stubbing window methods

```ts
cy.window().then((win) => {
  cy.stub(win, 'open').as('windowOpen')
})

cy.get('[data-cy="external-link"]').click()
cy.get('@windowOpen').should('have.been.calledOnce')
cy.get('@windowOpen').should('have.been.calledWith', 'https://example.com')
```

## Clock (`cy.clock`)

Override `setTimeout`, `setInterval`, `Date`, and other time-related globals.

```ts
// Freeze time at a specific date
const now = new Date('2024-01-15T10:00:00').getTime()
cy.clock(now)

cy.visit('/')
cy.get('[data-cy="date"]').should('contain', 'Jan 15, 2024')

// Advance time manually
cy.clock()
cy.get('[data-cy="start"]').click()
cy.tick(5000)   // advance 5 seconds
cy.get('[data-cy="elapsed"]').should('contain', '5s')

// Restore real clock
cy.clock().then((clock) => clock.restore())
// or:
cy.clock().invoke('restore')
```

### Override specific functions only

```ts
cy.clock(null, ['setTimeout', 'clearTimeout'])
// Date is not overridden — only setTimeout/clearTimeout
```

## Assertions Reference

```ts
// Call count
.should('have.been.called')
.should('have.been.calledOnce')
.should('have.been.calledTwice')
.should('have.been.calledThrice')
.should('have.been.callCount', 4)
.should('not.have.been.called')

// Arguments
.should('have.been.calledWith', arg1, arg2)
.should('have.been.calledWithExactly', arg1)
.should('have.been.calledWithMatch', { partial: true })

// Return value
.should('have.returned', 42)
.should('have.always.returned', 42)

// Order
.should('have.been.calledBefore', otherSpy)
.should('have.been.calledAfter', otherSpy)
```

## Key Points

- Spies and stubs are automatically restored between tests (Cypress resets sinon sandbox)
- Use `.as('alias')` to reference spies/stubs with `cy.get('@alias')`
- `cy.clock()` must be called before `cy.visit()` to affect the page from load
- `cy.tick()` is synchronous — no awaiting needed

<!--
Source references:
- sources/cypress/cli/types/cypress.d.ts (spy, stub, clock methods ~line 2120)
-->
