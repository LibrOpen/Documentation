# Testing Policy

This document describes the testing strategy and policies applied across the LibrOpen project. It covers unit testing, end-to-end testing, component testing, continuous integration, and production monitoring.

---

## Overview

The LibrOpen testing strategy is organized into multiple layers to ensure reliability at every stage of the development lifecycle:

- **Backend Unit Tests**: API endpoint testing using Japa, the built-in test runner for AdonisJS.
- **Frontend E2E Tests**: Full page and user flow testing using Cypress.
- **Component Tests**: Design system component testing using Storybook.
- **CI/CD Pipeline**: Automated test execution via GitHub Actions on every push.
- **Production Monitoring**: Runtime validation and observability using Jenkins, Grafana, Sentry, and Uptime Kuma.

---

## Backend Testing

### Framework

The backend uses **Japa**, the test runner integrated directly into AdonisJS. Japa provides a simple and fast way to write and execute tests without requiring external test frameworks.

You can run the backend tests from the `apps/backend` folder:

```bash
node ace test
```

To run a specific test file:

```bash
node ace test --files="tests/functional/project/create.spec.ts"
```

### Test Structure

Tests are organized by controller, with one folder per controller and one file per endpoint. This convention is enforced to keep tests focused and easy to locate.

```
tests/
└── functional/
    ├── project/
    │   ├── create.spec.ts
    │   ├── update.spec.ts
    │   ├── delete.spec.ts
    │   └── list.spec.ts
    ├── user/
    │   ├── register.spec.ts
    │   ├── login.spec.ts
    │   └── me.spec.ts
    ├── review/
    │   ├── create.spec.ts
    │   └── update.spec.ts
    └── ...
```

### What We Test

- **HTTP status codes**: Verify that each endpoint returns the correct status code for success and error scenarios (e.g., `200`, `201`, `400`, `401`, `403`, `404`).
- **Response body**: Assert that the response payload matches the expected structure and values.
- **Validation**: Ensure that invalid or missing request data is properly rejected by validators.
- **Authentication and authorization**: Confirm that protected endpoints reject unauthenticated requests and that role-based access control is enforced.
- **Edge cases**: Test boundary conditions such as duplicate entries, empty payloads, and exceeded limits.

### Database Handling

Each test run uses a dedicated test database configured via the `.env.test` file. Japa provides transaction-based rollback through the `@japa/preset-adonis` package, ensuring that each test starts with a clean state and does not affect other tests.

### Writing a Test

Example of a Japa functional test for the `POST /projects` endpoint:

```typescript
import { test } from "@japa/runner";

test.group("Project - Create", (group) => {
  group.each.setup(async () => {
    // Setup runs before each test
  });

  test("should create a project successfully", async ({ client }) => {
    const response = await client
      .post("/api/projects")
      .loginAs(user)
      .json({
        name: "My Project",
        description: "A test project",
        gitLink: "https://github.com/user/project",
      });

    response.assertStatus(201);
    response.assertBodyContains({ name: "My Project" });
  });

  test("should return 401 when not authenticated", async ({ client }) => {
    const response = await client.post("/api/projects").json({
      name: "My Project",
    });

    response.assertStatus(401);
  });

  test("should return 400 when name is missing", async ({ client }) => {
    const response = await client
      .post("/api/projects")
      .loginAs(user)
      .json({ description: "Missing name" });

    response.assertStatus(422);
  });
});
```

---

## Frontend Testing

### Framework

The frontend uses **Cypress** for end-to-end testing. Cypress runs in a real browser and tests the application as a user would interact with it.

You can run the frontend tests from the `apps/frontend` folder:

```bash
pnpm run cypress:open
```

To run tests in headless mode (used in CI):

```bash
npx cypress run
```

### Test Structure

Tests are organized by page or feature, with one file per page or flow:

```
cypress/
├── e2e/
│   ├── login.cy.ts
│   ├── register.cy.ts
│   ├── projects.cy.ts
│   ├── about.cy.ts
│   ├── privacy-terms.cy.ts
│   ├── error-page.cy.ts
│   ├── settings.cy.ts
│   ├── navigation.cy.ts
│   └── admin.cy.ts
├── fixtures/
│   ├── projects.json
│   ├── project-detail.json
│   ├── trendings.json
│   └── search-results.json
└── support/
    ├── commands.ts
    └── e2e.ts
```

### What We Test

- **Page rendering**: Verify that all expected elements (headings, forms, buttons, images) are visible on each page.
- **Form validation**: Test client-side validation rules (required fields, email format, password constraints, field matching).
- **Form submission**: Assert that forms send the correct payload to the API.
- **Navigation**: Verify that links, buttons, and redirects navigate to the correct routes.
- **Protected routes**: Ensure that unauthenticated users are redirected to `/login` when accessing protected pages (settings, project creation).
- **Locale routing**: Confirm that `/en/` and `/fr/` routes render correctly.
- **Error handling**: Verify that API errors display appropriate toast notifications.

### Backend Independence

All API calls are intercepted using `cy.intercept()` and return mocked responses from fixture files. This means:

- Tests do not require a running backend.
- Tests are deterministic and do not depend on database state.
- Fixture files in `cypress/fixtures/` provide consistent mock data.

### Turnstile (CAPTCHA) Handling

Cloudflare Turnstile is used on login and registration pages. Since the Turnstile widget requires a network connection and a valid site key, we stub it in tests:

- The `cy.stubTurnstile()` custom command injects a fake `window.turnstile` object before page load.
- The fake widget immediately provides a token, enabling the submit button without the real Cloudflare service.
- The real Turnstile script is blocked via `cy.intercept()`.

### Custom Commands

The following custom Cypress commands are defined in `cypress/support/commands.ts`:

- `cy.stubTurnstile()` - Stubs the Cloudflare Turnstile CAPTCHA widget.
- `cy.mockBackendAPIs()` - Intercepts all backend API calls with mocked responses.
- `cy.mockLoggedInUser()` - Simulates an authenticated user session.
- `cy.mockAdminUser()` - Simulates an authenticated admin session.

---

## Component Testing with Storybook

### Framework

The design system components in `libs/ui/design-system` are tested and documented using **Storybook**. Storybook provides an isolated environment to develop, visualize, and test UI components independently from the application.

### What We Test

- **Visual states**: Every component is rendered in all its possible variants (sizes, colors, disabled state, loading state, error state).
- **Interactions**: Interactive components (buttons, inputs, modals, dropdowns) include interaction tests using Storybook's play functions.
- **Props coverage**: Each Storybook story exercises a different combination of props to ensure all configurations render correctly.
- **Responsiveness**: Components are tested across multiple viewport sizes using Storybook's viewport addon.

### Story Structure

Each design system component has a corresponding `.storybook.tsx` file placed alongside its implementation:

```
libs/ui/design-system/components/
├── Button/
│   ├── Button.component.tsx
│   ├── Button.storybook.tsx
│   └── Button.style.tsx
├── Input/
│   ├── Input.component.tsx
│   ├── Input.storybook.tsx
│   └── Input.style.tsx
└── ...
```

### Running Storybook

```bash
pnpm run storybook
```

---

## CI/CD Pipeline

### GitHub Actions

The project uses GitHub Actions for continuous integration. Workflows are defined in `.github/workflows/` and are triggered on every push to the repository.

#### Backend Workflow (`backend.yml`)

Triggered on pushes affecting the `apps/backend` directory:

1. **Install dependencies** using pnpm.
2. **Build** the backend application.
3. **Lint** the codebase and push any auto-fixed changes.
4. **Run Japa tests** to validate all API endpoints.

#### Frontend Workflow (`frontend.yml`)

Triggered on pushes affecting the `apps/frontend` directory:

1. **Install dependencies** using pnpm.
2. **Build** the frontend application.
3. **Lint** the codebase and push any auto-fixed changes.
4. **Run Cypress tests** in headless mode to validate all pages.

### Pipeline Requirements

- All tests must pass before a pull request can be merged.
- Linting errors are auto-fixed and committed back to the branch when possible.
- Failing tests block the merge and must be resolved by the developer.

---

## Production Testing and Monitoring

### Jenkins

The Jenkins server ([http://jenkins.libropen.com](http://jenkins.libropen.com)) is used for production deployment and post-deployment validation:

- **Deployment Job**: Triggered on every push to the `main` branch. Builds Docker images for the backend and frontend, pushes them to the self-hosted Docker registry, and deploys to the production server.
- **Post-deployment Tests**: After deployment, Jenkins runs a suite of smoke tests against the production environment to verify that critical endpoints are responding and that the application is operational.
- **Build Artifacts**: Docker images are stored as artifacts and pushed to [https://registry.libropen.com](https://registry.libropen.com).

### Grafana

Grafana is used for runtime monitoring and performance tracking in production:

- **Dashboards**: Real-time dashboards display key application metrics including response times, request throughput, error rates, and resource usage (CPU, memory, disk).
- **Alerting**: Alert rules are configured to notify the team via Discord when critical thresholds are exceeded (e.g., error rate spikes, response time degradation, service downtime).
- **Data Sources**: Grafana collects metrics from the production infrastructure, including the backend API server, database, and Docker containers.

### Sentry

**Sentry** is integrated into both the frontend and backend for real-time error tracking:

- **Frontend**: The Next.js application reports unhandled exceptions, rendering errors, and network failures to Sentry with full stack traces and user context.
- **Backend**: The AdonisJS application captures uncaught exceptions, failed requests, and unhandled promise rejections.
- **Features Used**:
  - Automatic error grouping and deduplication.
  - Release tracking to correlate errors with specific deployments.
  - Performance monitoring to identify slow transactions and bottlenecks.
  - Alerts configured to notify the team on new or recurring issues.

### Uptime Kuma

**Uptime Kuma** monitors the availability of all LibrOpen services:

- **Monitored Services**:
  - Frontend application (`https://libropen.com`)
  - Backend API (`https://api.libropen.com`)
  - Status page (`https://status.libropen.com`)
  - Jenkins, MinIO, Mail server, and other infrastructure services.
- **Checks**: HTTP(s) health checks are performed at regular intervals (every 60 seconds).
- **Notifications**: Downtime alerts are sent to the team via Discord and email.
- **Status Page**: The public status page at [https://status.libropen.com](https://status.libropen.com) displays the current health and uptime history of all services.

---

## Summary

| Layer                  | Tool            | Scope                                    | Trigger              |
|------------------------|-----------------|------------------------------------------|----------------------|
| Backend Unit Tests     | Japa            | API endpoints, validation, auth          | Local + CI (GitHub)  |
| Frontend E2E Tests     | Cypress         | All pages, forms, navigation, auth flows | Local + CI (GitHub)  |
| Component Tests        | Storybook       | Design system components                 | Local + CI (GitHub)  |
| CI/CD                  | GitHub Actions  | Build, lint, test on every push          | Every push           |
| Deployment             | Jenkins         | Build, deploy, smoke tests               | Push to `main`       |
| Performance Monitoring | Grafana         | Metrics, dashboards, alerts              | Continuous           |
| Error Tracking         | Sentry          | Runtime exceptions, performance          | Continuous           |
| Uptime Monitoring      | Uptime Kuma     | Service availability, status page        | Every 60 seconds     |
