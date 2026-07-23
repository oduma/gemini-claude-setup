# Best Practices for an Angular + Angular Material SPA with a .NET API Backend

## 1. Purpose

This document outlines practical architecture guidelines for building a modern web application using **Angular** (with **Angular Material**) for the client-side SPA and **ASP.NET Core Web API** (C# / .NET) for the backend. 

The goal is to establish a solution that is maintainable, scalable, secure, testable, observable, and friendly to long-term team development.

---

## 2. Recommended High-Level Architecture

Adopt a **SPA + API** architecture with a strict separation of concerns:
*   **Angular SPA:** Handles presentation, local UI state, routing, and user interaction.
*   **.NET API:** Owns authentication, authorization, business logic orchestration, data access, integration with external systems, and cross-cutting server concerns.
*   **Zero Direct Access:** The browser communicates *only* with the API contract. It must never directly query databases or sensitive third-party services.

### Recommended Runtime Topology

```text
Browser
  ↓
Angular SPA (Angular + Angular Material)
  ↓ HTTPS / JSON / OpenAPI-backed contract
ASP.NET Core API
  ↓
Application / Domain / Infrastructure
  ↓
Databases, caches, external providers, queues, search, etc.
```

> **Design Rule:** Keep the Angular app focused on UX, view composition, and client-side validation. Keep the API focused on domain rules, security, persistence, and server-side validation.

---

## 3. Frontend Best Practices (Angular)

### 3.1 Prefer Standalone Architecture
For modern Angular applications, use **standalone components** instead of `NgModules`.
*   **Why:** Simpler mental model, less boilerplate, and easier feature-level composition.
*   **Guidance:** Use standalone components and route definitions by default. Keep `main.ts` bootstrapping minimal.

### 3.2 Organize by Feature Area, Not Technical Type
Avoid top-level folders like `components/`, `services/`, and `models/`. Instead, group by **feature/domain area**.

```text
src/app/
  core/         # App-wide singleton concerns (auth, config)
  shared/       # Reusable UI primitives and helpers
  features/     # Business-facing vertical slices
    search/
    playlists/
    account/
```

### 3.3 Keep Components Small and Presentation-Focused
Components should primarily handle rendering, interactions, and local state. Avoid heavy data-fetching or orchestration directly in large page components.
*   **Pattern:** Split into **Smart/Container Components** (load data, coordinate actions) and **Dumb/Presentational Components** (receive inputs, emit outputs).

### 3.4 Keep Templates Simple
Avoid complex conditional logic, large inline expressions, and data transformation chains in HTML.
*   **Prefer:** Derived view models, computed state/signals, and small reusable child components.
*   **Avoid:** Business logic in HTML and deeply nested structural directives.

### 3.5 Use a Tiered State Strategy
State management should be intentional, not accidental.
1.  **Local Component State:** UI toggles, dialog state, current tab.
2.  **Feature State (Services/Store):** Search results, active filters, scoped data.
3.  **Global App State:** Authenticated user profile, theme preference, global notifications.

> **Rule:** Global state should be the exception, not the default.

### 3.6 Use Typed API Clients
Avoid scattering ad-hoc `HttpClient` calls across components.
*   **Guidance:** Create **typed API client services** per backend domain (e.g., `account-api.service.ts`).
*   **Benefits:** Centralizes URLs and DTO typing, ensures consistent error handling, and improves testability.

### 3.7 Centralize Cross-Cutting Concerns
Use Angular’s interception and routing infrastructure:
*   **Interceptors:** Auth token injection, request/correlation IDs, consistent error mapping, and retry logic for idempotent reads.
*   **Route Guards:** Authentication checks, role/permission gating, and feature flags.

### 3.8 Lazy Load Feature Areas
Lazy load feature routes to ensure a smaller initial bundle, faster first paint, and cleaner architectural boundaries.

### 3.9 Enforce Strict Typing
Use strict TypeScript settings and strictly avoid `any`. Define DTOs and view models explicitly, and avoid leaking raw backend contracts deep into UI presentation logic.

---

## 4. Angular Material Best Practices

### 4.1 Treat Material as a Foundation, Not the Entire UI
Wrap repeated Material patterns into app-specific UI primitives to prevent your app from becoming a disjointed pile of low-level components.
*   **Instead of** raw `mat-card` or `mat-table` everywhere, build `<app-page-shell>`, `<app-data-table>`, or `<app-confirm-dialog>`.

### 4.2 Build a Coherent Theming Strategy
Define a consistent theming layer for colors, typography, density, and dark/light modes early on.
> **Rule:** Use Material design tokens consistently. Avoid ad-hoc CSS overrides unless strictly encapsulated.

### 4.3 Prioritize Accessibility (a11y)
*   Use semantic HTML and native controls.
*   Verify keyboard navigation (focus traps in dialogs/menus).
*   Maintain WCAG-compliant color contrast and screen-reader-friendly labels.
> **Rule:** A component is not complete until it is fully keyboard-usable and accessible.

### 4.4 Standardize Complex Components
Standardize behaviors for tables, forms, validation messages, and empty/loading states so the application feels like a single cohesive product.

---

## 5. Backend Best Practices (.NET Core API)

### 5.1 Treat the API as an Application Boundary
The backend is not a thin pass-through to the database. It must own domain rules, authorization, input validation, and persistence boundaries.

### 5.2 Choose an HTTP API Style and Stick to It
*   **Controllers:** Best for teams preferring MVC conventions, filters, versioning, and explicit class structures.
*   **Minimal APIs:** Best for lighter endpoints with disciplined route grouping.
> **Rule:** Pick one style as the default for the solution and apply it consistently.

### 5.3 Enforce Logical Layering
Avoid putting business logic in controllers. Use layered boundaries:
1.  **API / Presentation:** HTTP contract, auth, request mapping.
2.  **Application:** Use-case orchestration.
3.  **Domain:** Core business rules.
4.  **Infrastructure:** Database, external services, messaging.

### 5.4 Keep Endpoints Thin
Controllers/endpoints should only: receive requests, validate input, call the application service, and return a result. They should **never** contain core business logic or EF Core queries.

### 5.5 Use Explicit Request/Response DTOs
*   **Guidance:** Never expose database entities directly over the wire. Create dedicated contracts for requests, responses, and errors.
*   **Benefits:** Prevents over-posting, shields UI from database schema changes.

### 5.6 Standardize Validation and Errors
*   Validate all inputs at the API boundary.
*   Use **Problem Details** (`application/problem+json`) for consistent error shapes.
> **Rule:** A client should never have to guess what an error payload looks like.

### 5.7 Generate OpenAPI Documentation
Generate OpenAPI (Swagger) specs to document security requirements, endpoints, and DTO schemas. Use it to generate client code or validate contracts, reducing frontend/backend drift.

### 5.8 Secure the API Rigorously
*   Enforce authorization per resource (not just the endpoint).
*   Never trust client-provided IDs without validating ownership.
*   Use short-lived tokens.
*   Separate admin and user flows explicitly.

### 5.9 Implement Rate Limiting Intentionally
Partition limits by meaningful identities (user, tenant, IP) to prevent abuse, ensure fairness, and protect expensive endpoints (e.g., login, complex searches, exports).

### 5.10 Expose Health Checks
Implement separate `/health/live` and `/health/ready` endpoints for container orchestrators and load balancers. Do not leak sensitive internals in these responses.

### 5.11 Use Structured Logging and Correlation IDs
Log major business events and emit structured logs. Ensure correlation/trace IDs are passed from the Angular client through the API and into downstream services.

---

## 6. Cross-Cutting Best Practices

### 6.1 Design the API Contract First
For major features, define the request/response shapes (e.g., pagination limits, search filters, login flow) collaboratively before writing code to prevent divergent assumptions.

### 6.2 Distinct Authorization Responsibilities
*   **Frontend:** Reacts to auth state by hiding/disabling inaccessible UI elements.
*   **Backend:** Physically enforces authorization. Never trusts the frontend's permission state.

### 6.3 Use Intentional Pagination and Filtering
Do not return unbounded collections. All list-heavy endpoints must define pagination limits, sort semantics, and max page sizes to protect backend resources and frontend memory.

---

## 7. Testing Strategy

### 7.1 Frontend
*   **Unit Tests:** Isolated logic, mappers, and services.
*   **Component Tests:** UI behavior and Material interactions.
*   **E2E Tests:** Critical business journeys and auth flows.

### 7.2 Backend
*   **Unit Tests:** Domain and application business rules.
*   **Integration Tests:** HTTP endpoints, persistence logic, and external provider adapters.

### 7.3 Contract Testing
Treat breaking API changes as critical events. Validate OpenAPI specification changes in CI to detect contract drift early.

---

## 8. DevOps and Delivery

*   **Independent Builds:** The SPA and API should build and test independently, but deploy in a coordinated manner to avoid contract mismatches.
*   **CI/CD Gates:** Enforce linting, tests, build success, and security scanning on every PR.
*   **Active Observability:** Monitor frontend errors, API latencies, HTTP 500s, and auth failures. *Do not wait for users to report outages.*

---

## 9. Reference Solution Structure

```text
repo/
  docs/                  # Architecture and contract definitions
  src/
    client/
      angular-app/
        src/app/
          core/          # Interceptors, auth services
          shared/        # App-specific Material wrappers
          features/      # Lazy-loaded vertical slices
          api-clients/   # Strongly typed HTTP wrappers
    server/
      web-api/           # Controllers/Minimal APIs, Program.cs
      application/       # Interfaces, orchestrators, CQRS handlers
      domain/            # Entities, core business rules
      infrastructure/    # EF Core DbContext, external integrations
  tests/
    client/
    server/
    e2e/
```

---

## 10. Executive Summary & Anti-Patterns to Avoid

For this architecture to scale gracefully, adhere to the following principles and avoid common pitfalls:

| **Layer** | **Do** | **Don't** |
| :--- | :--- | :--- |
| **Frontend** | Wrap Angular Material into app-specific components. Group by feature. | Scatter `HttpClient` calls. Put complex business logic in HTML templates. |
| **Backend** | Keep controllers thin. Validate inputs. Return `Problem Details`. | Return EF entities directly. Expose unbounded lists. |
| **System** | Design contracts first. Implement correlation IDs. Enforce rate limiting. | Treat the UI as a security boundary. Allow implicit contract drift. |

**The Bottom Line:** Keep the frontend focused on UX, the backend focused on rules and security, and the contract between them explicitly defined and rigorously maintained.