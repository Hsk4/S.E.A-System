# Code Standards

---

## Code Standards Per Language & Framework

---

### TypeScript (Server + Web — Shared Rules)

- Strict mode enabled in every `tsconfig.json` — no exceptions
- No `any` — ever. Use `unknown` and narrow it, or define a proper type
- No type assertions (`as SomeType`) unless absolutely unavoidable — add a comment explaining why
- Prefer `type` over `interface` for data shapes, `interface` only for things that get extended or implemented
- All function parameters and return types explicitly typed — no implicit inference on public functions
- Use `enum` only for fixed, known sets (roles, alert types, incident status) — not for magic strings
- Zod schemas are the single source of truth for validation — infer TypeScript types from them, never define separately:
```ts
const incidentSchema = z.object({ ... })
type Incident = z.infer<typeof incidentSchema>
```
- No barrel files (`index.ts` that re-exports everything) — they cause circular dependency nightmares in monorepos
- Utility types (`Partial`, `Pick`, `Omit`, `Required`) preferred over duplicating type definitions

---

### Node.js / Express (Server)

- Every route handler is `async` — no sync handlers
- All async errors wrapped or use an `asyncHandler` utility — never unhandled promise rejections:
```ts
const asyncHandler = (fn: RequestHandler) => (req, res, next) =>
  Promise.resolve(fn(req, res, next)).catch(next)
```
- Controllers are thin — validate input, call service, send response. Nothing else
- Services contain all business logic — testable in isolation, no Express types inside services
- Middleware functions are named, never anonymous arrow functions in route files
- All incoming request bodies validated against a Zod schema before reaching the controller
- HTTP status codes used correctly and consistently:

| Situation | Code |
|---|---|
| Success with data | 200 |
| Resource created | 201 |
| Bad request / validation fail | 400 |
| Unauthenticated | 401 |
| Wrong role / forbidden | 403 |
| Not found | 404 |
| Server error | 500 |

- Environment variables never accessed directly in code — always via a validated `config` object:
```ts
// config/env.ts
export const config = {
  jwtSecret: process.env.JWT_SECRET!,
  mongoUri: process.env.MONGO_URI!,
  port: Number(process.env.PORT) || 3000,
}
```
- No `console.log` in production code — use a structured logger (pino recommended)

---

### MongoDB / Mongoose

- One schema per file — filename matches the model name (`incident.model.ts`)
- Schema fields always have explicit types, `required`, and `default` where applicable
- No `.save()` in controllers — always in services
- Indexes defined in the schema, not applied manually:
```ts
incidentSchema.index({ location: '2dsphere' })
incidentSchema.index({ status: 1, createdAt: -1 })
```
- Use `lean()` on read-only queries for performance — returns plain JS objects, not Mongoose documents
- Timestamps (`createdAt`, `updatedAt`) enabled on every schema via `{ timestamps: true }`
- Never expose `__v` or raw `_id` in API responses — transform to `id` string in a response serializer
- Transactions used for any operation that writes to more than one collection

---

### Socket.io (Server-side)

- All socket event names follow `domain:action` convention — defined as constants in `packages/shared`, never hardcoded as strings
- Socket handlers live in `/sockets` — one file per domain (`incident.socket.ts`, `location.socket.ts`)
- JWT verified on every connection in the `connection` middleware — unauthenticated sockets disconnected immediately
- Room names follow a consistent pattern and are built from constants:
```ts
const incidentRoom = (id: string) => `incident:${id}`
const zoneRoom = (city: string) => `zone:${city}`
```
- Never emit to `socket.broadcast` — always use named rooms for predictable targeting
- All socket handlers are async and wrapped in try/catch — errors emitted back to the client as `error` events, never swallowed

---

### React (Web Dashboard)

- Functional components only — no class components
- Every component in its own file — filename matches component name (`IncidentCard.tsx`)
- Props always explicitly typed with a `type` definition directly above the component
- No prop drilling beyond 2 levels — anything deeper goes into Redux
- `useEffect` dependencies array always complete — ESLint `exhaustive-deps` rule enforced
- No inline styles — Tailwind utility classes only
- No logic inside JSX — extract to a variable or function above the return statement
- Component files structure:
```tsx
// 1. Imports
// 2. Type definitions
// 3. Component function
// 4. Export
```

---

### Redux Toolkit (Web Dashboard — State Management)

- One slice per feature domain (`incidentSlice`, `responderSlice`, `announcementSlice`)
- All slices live in `store/slices/` — one file per slice
- `createAsyncThunk` for all async operations (API calls) — never fetch inside components
- Selectors defined at the bottom of each slice file and memoized with `createSelector` where derived data is involved
- Components never access `state` directly — always via selectors:
```ts
// slice file
export const selectActiveIncidents = (state: RootState) =>
  state.incidents.active

// component
const incidents = useAppSelector(selectActiveIncidents)
```
- No business logic in reducers — reducers only update state
- `RTK Query` used for server state (API calls, caching) — Redux slices only for client/UI state that doesn't come from the server
- Typed hooks (`useAppDispatch`, `useAppSelector`) always used — never raw `useDispatch` or `useSelector`

---

### React Query (Web Dashboard — Server State)

- Used alongside Redux — React Query owns server state, Redux owns UI state
- Query keys are defined as constants in a `queryKeys.ts` file — never inline strings
- All mutations invalidate relevant queries on success — no manual cache updates unless necessary
- Loading and error states always handled — no query result used without checking `isLoading` and `isError`

---

### Flutter / Dart

- All files in `snake_case`, all classes in `PascalCase`
- BLoC pattern for all state management — no `setState` outside of truly local, ephemeral UI state (e.g. a text field focus)
- One BLoC per feature — `IncidentBloc`, `AuthBloc`, `TrackingBloc`
- Events and states defined as sealed classes:
```dart
sealed class IncidentEvent {}
class SOSTriggered extends IncidentEvent { ... }
class SOSCancelled extends IncidentEvent {}

sealed class IncidentState {}
class IncidentIdle extends IncidentState {}
class IncidentDispatched extends IncidentState { ... }
```
- Repository pattern for all data access — BLoCs never call APIs directly, always through a repository
- All API calls in the data layer — never in presentation
- `flutter_secure_storage` for JWT — never `SharedPreferences` for sensitive data
- `freezed` for immutable data models — no manual `copyWith` or equality overrides
- `dio` for HTTP client — with interceptors for auth token injection and error handling
- `socket_io_client` for real-time connection — managed in a dedicated `SocketService` singleton
- No hardcoded strings in UI — all user-facing text in a constants file (future-ready for localization)
- Every screen is a `StatelessWidget` — state comes from BLoC, never from the widget itself

---

### Dart (General)

- `final` by default — `var` only when the value genuinely changes
- `const` constructors wherever possible — improves Flutter rendering performance
- Null safety enforced — no `!` force-unwrap without a null check above it and a comment
- Named parameters for any function with more than 2 parameters
- `async/await` always — no raw `.then()` chains

---

That covers every language and framework in the stack. Want to move on to the database schema next?
## File Organization

sea-system/
├── package.json               ← root scripts
├── pnpm-workspace.yaml        ← workspace config
├── .env.example
├── README.md
│
├── apps/
│   ├── server/                ← Express + Socket.io
│   │   └── src/
│   │       ├── config/        ← db, env, constants
│   │       ├── controllers/   ← route handlers
│   │       ├── middleware/    ← auth, role guards, error handler
│   │       ├── models/        ← Mongoose schemas
│   │       ├── routes/        ← Express routers
│   │       ├── services/      ← dispatch, escalation, radius logic
│   │       ├── sockets/       ← Socket.io event handlers
│   │       └── index.ts
│   │
│   ├── web/                   ← React admin dashboard (Vite)
│   │   └── src/
│   │       ├── components/
│   │       ├── pages/
│   │       ├── hooks/
│   │       ├── services/      ← API call functions
│   │       └── store/         ← global state
│   │
│   └── mobile/                ← Flutter app
│       ├── package.json       ← Flutter script mask
│       └── lib/
│           ├── core/          ← constants, theme, router
│           ├── data/          ← repositories, API clients
│           ├── domain/        ← entities, use cases
│           └── presentation/  ← screens, widgets, BLoCs
│
└── packages/
    └── shared/                ← shared between server + web only
        └── src/
            ├── roles.ts
            ├── errors.ts
            ├── schemas/       ← Zod schemas
            └── utils/


---

## Folder Structure

---

### Server (`apps/server/src/`)

- `config/` — Database connection, environment variables, app-wide constants
- `controllers/` — Request/response handlers, one file per feature domain
- `middleware/` — JWT verification, role guards, error handler, request logger
- `models/` — Mongoose schemas, one file per collection
- `routes/` — Express routers, one file per feature domain
- `services/` — All business logic, dispatch engine, escalation timers, radius search
- `sockets/` — Socket.io event registration and handlers, one file per domain
- `utils/` — Pure helper functions, reusable across the codebase
- `types/` — Shared TypeScript types and enums specific to the server
- `index.ts` — Entry point, Express + Socket.io server bootstrap

---

### Web Dashboard (`apps/web/src/`)

- `components/` — Reusable UI components not tied to any specific feature
- `pages/` — Route-level page components, one file per route
- `features/` — Self-contained feature folders (incidents, registrations, announcements), each containing its own components, hooks, and selectors
- `store/` — Redux store setup, one subfolder per slice
- `store/slices/` — One slice file per feature domain
- `hooks/` — Custom React hooks shared across features
- `services/` — RTK Query API definitions and React Query fetch functions
- `utils/` — Pure helper functions
- `constants/` — Query keys, route paths, role constants
- `types/` — TypeScript types specific to the web dashboard
- `assets/` — Static assets, icons, images
- `main.tsx` — Entry point

---

### Mobile (`apps/mobile/lib/`)

- `core/` — App-wide setup, theme, router, dependency injection, constants
- `core/constants/` — String constants, socket event names, route names
- `core/theme/` — Colors, text styles, spacing
- `core/router/` — GoRouter or Navigator 2.0 route definitions
- `data/` — Everything that touches external sources (API, sockets, local storage)
- `data/models/` — Raw data models, Freezed classes, JSON serialization
- `data/repositories/` — Concrete repository implementations
- `data/sources/` — API clients (Dio), Socket service, SecureStorage service
- `domain/` — Pure Dart, no Flutter dependency
- `domain/entities/` — Core business objects (Incident, Responder, Alert)
- `domain/repositories/` — Abstract repository interfaces
- `domain/usecases/` — One use case per user action (TriggerSOS, AcceptAlert, UpdateLocation)
- `presentation/` — Everything the user sees
- `presentation/screens/` — Full screen widgets, one folder per feature
- `presentation/widgets/` — Reusable UI widgets shared across screens
- `presentation/blocs/` — BLoC and Cubit files, one folder per feature
- `main.dart` — Entry point

---

### Shared Package (`packages/shared/src/`)

- `roles.ts` — Role constants and enums (`USER`, `ADMIN`, `AMBULANCE`, etc.)
- `errors.ts` — Error codes and standard error messages
- `events.ts` — Socket event name constants shared across server and web
- `schemas/` — Zod validation schemas consumed by both server and web
- `utils/` — Pure utility functions safe to use in both environments

---





