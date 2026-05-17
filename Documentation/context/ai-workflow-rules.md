# AI Workflow Rules

## Approach

Build this project incrementally using a spec-driven workflow. Context files define what to build, how to build it, and what the current state of progress is. Always implement against these specs — do not infer or invent behavior from scratch. Suggestions are acceptable if they don't deviate from the original original workflow stated in project-overview.md and geniuinely improve the project.


## Scoping Rules

Since Flutter and MERN are confirmed, here are the code standards. Rust and Python are left open for now:

---

### JavaScript — Shared (React & Node.js/Express)

- Use **ES6+** syntax throughout — arrow functions, destructuring, template literals, optional chaining
- **camelCase** for all variables and functions — `getUserById`, `gateLogEntry`
- **PascalCase** for React components and class names — `GateScanCard`, `AdminDashboard`
- **SCREAMING_SNAKE_CASE** for constants — `MAX_QR_EXPIRY_TIME`, `PANIC_BROADCAST_CHANNEL`
- All files named in **kebab-case** — `gate-log.controller.js`, `visitor-card.jsx`
- No `var` — only `const` and `let`, prefer `const` by default
- Async operations handled with `async/await` only — no raw `.then()` chains
- Every async function wrapped in `try/catch` — no unhandled promise rejections
- No magic numbers or hardcoded strings — all go into a constants or config file
- Maximum function length of **30 lines** — if longer, break it into smaller functions
- Single responsibility per function — one function does one thing only
- ESLint enforced across the entire codebase with a shared `.eslintrc` config
- Prettier enforced for formatting — no manual style debates in code review

---

### React — Frontend Specific

- **Functional components only** — no class components
- Every component in its own file — one component per file, no exceptions
- Props validated with **PropTypes** or migrated to **TypeScript interfaces** if the team agrees
- Global state managed with **Context API** or **Redux Toolkit** — no prop drilling beyond two levels
- API calls isolated in a dedicated `/services` folder — no `fetch` or `axios` calls inside components directly
- Custom hooks prefixed with `use` — `useGateLog`, `usePanicAlert`
- No inline styles — all styling via Tailwind utility classes
- Components organized by feature not by type — `features/gate-logs/` not `components/tables/`
- Lazy loading applied to all route-level components to keep initial bundle size minimal
- No direct DOM manipulation — everything through React state and refs

---

###  Node.js / Express — Backend Specific

- Project follows **MVC pattern** strictly — Models, Controllers, Routes separated with no crossover
- All routes defined in `/routes`, logic lives in `/controllers`, DB operations in `/services`
- Every route protected by appropriate **auth middleware** — no unguarded endpoints
- Input validation on every request using **express-validator** or **Joi** before it touches a controller
- Errors handled by a single centralized **error handling middleware** — no scattered `res.status(500)` calls
- HTTP status codes used correctly and consistently — `200`, `201`, `400`, `401`, `403`, `404`, `500`
- Environment variables accessed only through a single `config.js` file — never `process.env` scattered inline
- All database operations in the service layer — controllers never touch Mongoose directly
- Logging handled by **Morgan** (HTTP) and **Winston** (application) — no `console.log` in production code
- API versioned from day one — all routes under `/api/v1/`

---

###  MongoDB / Mongoose

- Every collection has a dedicated **Schema file** in `/models` — no inline schema definitions
- Schema fields always explicitly typed with validation — `required`, `minlength`, `maxlength`, `enum` where applicable
- **Timestamps** enabled on every schema — `createdAt` and `updatedAt` auto-managed by Mongoose
- No storing sensitive data in plain text — passwords always hashed with **bcrypt** before save
- Indexes defined on frequently queried fields — `userId`, `timestamp`, `gateId` at minimum
- Populate used sparingly — avoid deeply nested populates that kill query performance
- Collection names always **plural and lowercase** — `gatelogs`, `visitors`, `announcements`
- No raw MongoDB queries bypassing Mongoose — all DB operations go through defined models

---

###  Dart / Flutter — Mobile Specific

- **snake_case** for file names — `gate_scan_screen.dart`, `visitor_card_widget.dart`
- **PascalCase** for all classes and widgets — `GateScanScreen`, `PanicButton`
- **camelCase** for variables and method names — `scanResult`, `fetchGateLogs()`
- State management via **Provider** or **Riverpod** — no raw `setState` beyond simple local UI state
- Every screen in its own file under `/screens`, reusable widgets under `/widgets`
- API calls isolated in a `/services` layer — no `http` calls inside widget build methods
- All hardcoded strings moved to a centralized `constants.dart` or `strings.dart` file
- No business logic inside `build()` methods — keep them purely declarative
- Models defined as **immutable data classes** with `fromJson` and `toJson` methods for all API responses
- Navigation handled through a centralized router — **GoRouter** preferred for named route consistency
- All assets (icons, images) declared in `pubspec.yaml` and accessed via constants, never raw string paths
- Linting enforced via `flutter_lints` package with a shared `analysis_options.yaml` across the team

---

### Universal Standards (All Languages)

- No commented-out dead code committed to the repository — delete it or it doesn't get merged
- Every function and module has a brief comment explaining **why** it exists, not what it does
- Branch naming convention — `feature/`, `fix/`, `hotfix/` prefixes required on all branches
- No direct commits to `main` — all changes go through pull requests with at least one review
- `.env` files never committed — always in `.gitignore`, a `.env.example` provided instead
- Commit messages follow **Conventional Commits** — `feat:`, `fix:`, `chore:`, `docs:`, `refactor:`
- No feature ships without basic **error handling** — every user-facing failure has a readable message

## When to Split Work

Split an implementation step if it combines:

- [Concern one — e.g. UI changes and background task changes]
- [Concern two — e.g. Multiple unrelated API routes]
- [Concern three — e.g. Behavior not clearly defined in
  the context files]

If a change cannot be verified end to end quickly,
the scope is too broad — split it.

## Handling Missing Requirements

- Do not invent product behavior not defined in the
  context files
- If a requirement is ambiguous, resolve it in the
  relevant context file before implementing
- If a requirement is missing, add it as an open question
  in `progress-tracker.md` before continuing

## Protected Files

Do not modify the following unless explicitly instructed:

- [e.g. components/ui/* — generated UI library components]
- [e.g. Any third-party library internals]

## Keeping Docs in Sync

Update the relevant context file whenever implementation
changes:

- System architecture or boundaries
- Storage model decisions
- Code conventions or standards
- Feature scope

## Before Moving to the Next Unit

1. The current unit works end to end within its defined scope
2. No invariant defined in `architecture.md` was violated
3. `progress-tracker.md` reflects the completed work
4. `npm run build` passes
