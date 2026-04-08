---
name: features-component-testing
description: >
  Cypress component testing — mounting React, Vue, Angular, or Svelte components in the browser
  with cy.mount, isolated from a full page, for fast and reliable component-level tests.

  Use this skill whenever: the user asks about Cypress component tests, cy.mount, mounting a
  component, component testing setup, devServer config, wrapping components with providers,
  or testing props/emits/slots in Cypress. Trigger for "@cypress/react", "@cypress/vue",
  "@cypress/angular", "@cypress/svelte".

  Do NOT use for: E2E Cypress tests (cy.visit), Vitest component tests, React Testing Library,
  or Vue Test Utils — those are different testing approaches.
---

# Component Testing

Cypress runs component tests directly in the browser using framework-specific mount adapters. No server or router required.

## Setup in cypress.config.ts

```ts
import { defineConfig } from 'cypress'

export default defineConfig({
  component: {
    devServer: {
      framework: 'react',   // 'vue' | 'angular' | 'svelte' | 'next' | 'nuxt'
      bundler: 'vite',      // 'vite' | 'webpack'
    },
    specPattern: 'src/**/*.cy.{js,jsx,ts,tsx}',
    supportFile: 'cypress/support/component.ts',
  },
})
```

## Support File

```ts
// cypress/support/component.ts
import { mount } from 'cypress/react'
// or: import { mount } from 'cypress/vue'

Cypress.Commands.add('mount', mount)

// TypeScript
declare global {
  namespace Cypress {
    interface Chainable {
      mount: typeof mount
    }
  }
}
```

## React

```tsx
// src/components/Button.cy.tsx
import { Button } from './Button'

describe('Button', () => {
  it('renders and handles click', () => {
    const onClick = cy.stub().as('onClick')

    cy.mount(<Button onClick={onClick} label="Submit" />)

    cy.get('button').should('have.text', 'Submit').click()
    cy.get('@onClick').should('have.been.calledOnce')
  })

  it('shows disabled state', () => {
    cy.mount(<Button disabled label="Submit" />)
    cy.get('button').should('be.disabled')
  })
})
```

## Vue

```ts
// src/components/UserCard.cy.ts
import UserCard from './UserCard.vue'

describe('UserCard', () => {
  it('renders user data', () => {
    cy.mount(UserCard, {
      props: { name: 'Jane', role: 'Admin' },
    })

    cy.get('[data-cy="name"]').should('contain', 'Jane')
    cy.get('[data-cy="role"]').should('contain', 'Admin')
  })

  it('emits logout event', () => {
    cy.mount(UserCard, { props: { name: 'Jane' } })
    cy.get('[data-cy="logout"]').click()
    cy.get('@vue').should('have.emitted', 'logout')
  })
})
```

## Angular

```ts
// src/app/counter/counter.component.cy.ts
import { CounterComponent } from './counter.component'

describe('CounterComponent', () => {
  it('increments counter', () => {
    cy.mount(CounterComponent)
    cy.get('[data-cy="count"]').should('contain', '0')
    cy.get('[data-cy="increment"]').click()
    cy.get('[data-cy="count"]').should('contain', '1')
  })
})
```

## Svelte

```ts
// src/lib/Toggle.cy.ts
import Toggle from './Toggle.svelte'

describe('Toggle', () => {
  it('toggles state', () => {
    cy.mount(Toggle, { props: { checked: false } })
    cy.get('input[type="checkbox"]').check()
    cy.get('input[type="checkbox"]').should('be.checked')
  })
})
```

## Mounting with Providers / Global Config

### React with providers

```tsx
// cypress/support/component.tsx
import { mount } from 'cypress/react'
import { QueryClient, QueryClientProvider } from '@tanstack/react-query'

Cypress.Commands.add('mount', (component, options = {}) => {
  const queryClient = new QueryClient()
  const wrapped = (
    <QueryClientProvider client={queryClient}>
      {component}
    </QueryClientProvider>
  )
  return mount(wrapped, options)
})
```

### Vue with global plugins

```ts
// cypress/support/component.ts
import { mount } from 'cypress/vue'
import router from '../../src/router'
import pinia from '../../src/stores'

Cypress.Commands.add('mount', (component, options = {}) => {
  return mount(component, {
    global: {
      plugins: [router, pinia],
    },
    ...options,
  })
})
```

## Key Points

- Component tests run in a real browser but without visiting a full page
- `cy.mount` replaces `cy.visit` — returns a chainable reference to the mounted component
- Use `[data-cy="..."]` selectors for robustness
- Intercept API calls with `cy.intercept` just like in E2E tests
- Test file lives next to the component source file

<!--
Source references:
- sources/cypress/cli/AGENTS.md (CLI Workspace — Component-Testing Adapters)
- sources/cypress/cli/react/
- sources/cypress/cli/vue/
-->
