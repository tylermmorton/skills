---
name: core-config
description: >
  Cypress configuration via cypress.config.ts — defineConfig, E2E and component testing setup,
  timeouts, viewport, retries, env vars, and per-testing-type overrides.

  Use this skill whenever: the user asks about cypress.config, configuring Cypress, setting a
  baseUrl, changing timeouts or viewport, adding env variables, configuring retries, setting up
  setupNodeEvents, or any cypress.config.ts/js option.

  Do NOT use for: writing test assertions, cy.* command usage, or non-config Cypress questions.
---

# Configuration

Cypress uses `cypress.config.ts` (or `.js`) at the project root. Use `defineConfig` for TypeScript autocomplete.

## Basic Setup

```ts
import { defineConfig } from 'cypress'

export default defineConfig({
  e2e: {
    baseUrl: 'http://localhost:3000',
    specPattern: 'cypress/e2e/**/*.cy.{js,jsx,ts,tsx}',
    supportFile: 'cypress/support/e2e.ts',
    setupNodeEvents(on, config) {
      // register plugins here
      return config
    },
  },
})
```

## Component Testing

```ts
import { defineConfig } from 'cypress'

export default defineConfig({
  component: {
    devServer: {
      framework: 'react', // 'vue' | 'angular' | 'svelte' | 'next' | 'nuxt' | 'vite'
      bundler: 'vite',    // 'vite' | 'webpack'
    },
    specPattern: 'src/**/*.cy.{js,jsx,ts,tsx}',
    supportFile: 'cypress/support/component.ts',
  },
})
```

## Key Options

| Option | Default | Description |
|--------|---------|-------------|
| `baseUrl` | `null` | Prefix for `cy.visit()` and `cy.request()` URLs |
| `defaultCommandTimeout` | `4000` | ms to retry commands before failing |
| `requestTimeout` | `5000` | ms to wait for XHR requests in `cy.wait()` |
| `responseTimeout` | `30000` | ms to wait for a response |
| `pageLoadTimeout` | `60000` | ms to wait for `cy.visit()` to load |
| `viewportWidth` | `1000` | Default viewport width in pixels |
| `viewportHeight` | `660` | Default viewport height in pixels |
| `testIsolation` | `true` | Clear browser state between tests |
| `retries` | `0` | Retry failed tests (set `{ runMode: 2, openMode: 0 }`) |
| `video` | `false` | Record video in `cypress run` |
| `screenshotOnRunFailure` | `true` | Auto-screenshot on test failure |
| `fixturesFolder` | `cypress/fixtures` | Path to fixture files |
| `supportFile` | `cypress/support/e2e.js` | File loaded before each spec |

## Environment Variables

```ts
export default defineConfig({
  env: {
    apiUrl: 'http://localhost:4000',
    auth_token: 'abc123',
  },
})
```

Access in tests via `Cypress.env('apiUrl')` or `cy.env(['apiUrl', 'auth_token'])`.

Override at runtime: `cypress run --env apiUrl=http://staging.example.com`

## Retries

```ts
export default defineConfig({
  e2e: {
    retries: {
      runMode: 2,  // retry twice in CI
      openMode: 0, // no retries in interactive mode
    },
  },
})
```

## Per-spec Overrides

Use `Cypress.config()` inside tests, or frontmatter in spec files:

```ts
// cypress/e2e/slow-spec.cy.ts
Cypress.config('defaultCommandTimeout', 10000)
```

<!--
Source references:
- sources/cypress/cli/types/cypress.d.ts (ConfigOptions interface ~line 3040)
- sources/cypress/packages/config/AGENTS.md
-->
