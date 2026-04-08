---
name: core-commands
description: >
  Core cy.* commands for querying DOM elements, performing actions, navigating, and asserting —
  the building blocks of every Cypress test.

  Use this skill whenever: the user asks about cy.get, cy.contains, cy.click, cy.type, cy.visit,
  cy.url, cy.should, cy.wait, cy.wrap, traversal (find, children, parent, siblings), or any
  general cy.* command usage. Trigger for questions like "how do I select", "how do I click",
  "how do I assert", "how do I navigate" in a Cypress context.

  Do NOT use for: cy.intercept (network stubbing), cy.fixture, cy.task, cy.session, cy.spy/stub,
  or component testing mount — those have dedicated references.
---

# Core Commands

All `cy.*` commands are asynchronous and chainable. Commands are automatically retried until they succeed or time out.

## Querying

```ts
cy.get('button')                    // CSS selector
cy.get('[data-cy="submit"]')        // attribute selector (preferred for tests)
cy.get('#id .class')                // compound selectors
cy.contains('Submit')               // by text content
cy.contains('button', 'Submit')     // element + text
cy.contains(/regex/)                // regex match

// DOM traversal
cy.get('ul').find('li')             // descendant
cy.get('li').first()
cy.get('li').last()
cy.get('li').eq(2)                  // 0-indexed
cy.get('li').filter('.active')
cy.get('input').parent()
cy.get('td').siblings()
cy.get('div').children('p')
cy.get('span').closest('form')
cy.get('input').next()
cy.get('input').prev()
```

## Actions

```ts
cy.get('button').click()
cy.get('button').click('topRight')      // position
cy.get('button').dblclick()
cy.get('button').rightclick()

cy.get('input').type('hello world')
cy.get('input').type('{enter}')          // special keys
cy.get('input').type('{selectall}{del}')
cy.get('input').clear()                  // alias for selectall+del
cy.get('input').clear().type('new value')

cy.get('select').select('Option Label')
cy.get('select').select('option-value')
cy.get('select').select(2)               // index

cy.get('[type="checkbox"]').check()
cy.get('[type="checkbox"]').uncheck()
cy.get('[type="radio"]').check('value')

cy.get('input[type=file]').selectFile('cypress/fixtures/image.png')
cy.get('form').submit()

cy.get('.item').trigger('mouseover')
cy.focused().blur()
cy.get('input').focus()
```

## Navigation

```ts
cy.visit('/')                            // uses baseUrl
cy.visit('http://localhost:3000/login')  // absolute URL
cy.visit('/page', {
  method: 'POST',
  body: { user: 'test' },
})

cy.go('back')
cy.go('forward')
cy.reload()
cy.reload(true)                          // force reload (no cache)
cy.url().should('include', '/dashboard')
cy.location('pathname').should('eq', '/dashboard')
cy.title().should('eq', 'My App')
```

## Assertions (should / and)

```ts
cy.get('button').should('be.visible')
cy.get('button').should('be.disabled')
cy.get('input').should('have.value', 'test')
cy.get('p').should('contain.text', 'Hello')
cy.get('img').should('have.attr', 'src', '/logo.png')
cy.get('li').should('have.length', 3)
cy.get('div').should('have.class', 'active')
cy.get('div').should('not.have.class', 'hidden')

// chain multiple assertions
cy.get('input')
  .should('be.visible')
  .and('have.value', '')
  .and('not.be.disabled')

// callback form for complex assertions
cy.get('ul').should(($ul) => {
  expect($ul).to.have.length(3)
  expect($ul.eq(0)).to.contain('First')
})
```

## Waiting

```ts
cy.wait(1000)                         // explicit wait (avoid — prefer assertions)
cy.wait('@apiAlias')                  // wait for intercepted request
cy.wait('@apiAlias').its('response.statusCode').should('eq', 200)
cy.wait(['@req1', '@req2'])           // wait for multiple
```

## Utility

```ts
cy.wrap({ name: 'Jane' }).its('name').should('eq', 'Jane')
cy.wrap([1, 2, 3]).each((num) => { /* iterate */ })
cy.then((subject) => { /* access previous subject */ })

cy.get('li').each(($el, index) => {
  cy.wrap($el).should('contain.text', `Item ${index + 1}`)
})

cy.window()                           // app's window object
cy.document()                         // app's document object
cy.exec('node scripts/seed.js')       // run shell command

cy.scrollTo('bottom')
cy.get('.list').scrollTo(0, 500)
cy.get('img').scrollIntoView()

cy.viewport(1280, 720)
cy.viewport('iphone-x')
cy.screenshot('before-click')
```

## Aliases

```ts
cy.get('[data-cy="user-name"]').as('userName')
cy.get('@userName').should('contain', 'Jane')

cy.intercept('GET', '/api/users').as('getUsers')
cy.wait('@getUsers')
```

<!--
Source references:
- sources/cypress/cli/types/cypress.d.ts (Chainable interface ~line 907)
-->
