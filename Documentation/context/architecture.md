---

# Architecture Context

## System Boundaries

The S.E.A System is composed of three independently runnable applications inside a single pnpm monorepo. The **Flutter mobile app** serves all roles except admin — users, helpers, ambulances, police, and firefighters. The **React web dashboard** serves admins only. The **Express server** is the single backend for both clients — it owns all business logic, dispatch decisions, and data. No client ever talks directly to the database. All communication between clients and server goes through either the REST API or the Socket.io layer. The shared package (`packages/shared`) is a compile-time dependency only — it contains constants, Zod schemas, and utilities consumed by the server and web dashboard, never by Flutter.

---

## Auth and Access Model

Authentication is handled entirely by the Express server using **JWT with role claims**. On login, the server issues a signed JWT containing the user's ID and role. Every subsequent REST request must include this token in the `Authorization: Bearer <token>` header. Every Socket.io connection must pass the token as a handshake query parameter. The server verifies and decodes the token in middleware before any route handler or socket handler executes. Role-based access is enforced at the middleware level — a request from a `helper` hitting a `police`-only route receives a `403` immediately, before any controller logic runs. Flutter stores the token in `flutter_secure_storage`. The React dashboard stores the token in memory only — never in `localStorage` or `sessionStorage`. There is no refresh token in v1 — session expiry forces re-login.

---

## Stack

| Layer | Technology |
|---|---|
| Mobile | Flutter + Dart |
| Web dashboard | React + Vite + TypeScript |
| Backend | Express.js + Node.js + TypeScript |
| Runtime | Bun |
| Database | MongoDB + Mongoose |
| Real-time | Socket.io |
| Auth | JWT + Role claims |
| Push notifications | FCM |
| State (web) | Redux Toolkit + RTK Query |
| State (mobile) | BLoC + Repository pattern |
| Validation | Zod (server + web), Dart types (mobile) |
| Package manager | pnpm workspaces |
| Maps | TBD |

---

## Invariants

1. **No business logic in controllers or route handlers** — controllers validate input, call a service, and return a response. All dispatch decisions, escalation timers, radius calculations, and role-restriction rules live exclusively in the service layer.

2. **No client ever writes to or reads from the database directly** — all data access goes through the Express server. Flutter and React have no database credentials and no direct MongoDB connection.

3. **No request handler runs long-lived background work** — escalation timers, timeout checks, and scheduled tasks are managed by a dedicated background service on the server. A controller that returns a response must have already handed off any async work before doing so.

4. **Socket event names are never hardcoded as strings** — all event names are defined as constants in `packages/shared/src/events.ts` and imported wherever used on both the server and the web dashboard. Flutter maintains its own mirrored constants file in `core/constants/`.

5. **Every incident action is logged before a response is sent** — no state change (alert dispatched, responder accepted, escalation triggered, incident resolved) is communicated to any client unless it has first been persisted to the database.

6. **Helpers are never dispatched to crime or fire incidents** — this rule is enforced in the dispatch service, not in the route or controller. No API call or socket event can bypass it.

7. **JWT role is the single source of truth for access** — no client-side role check is trusted. The server re-verifies role on every request and every socket event, regardless of what the client claims.

---

## System Boundaries

| Boundary | Rule |
|---|---|
| Flutter ↔ Server | REST + Socket.io only |
| React ↔ Server | REST + Socket.io only |
| Server ↔ MongoDB | Mongoose only, no raw driver queries |
| Flutter ↔ MongoDB | Never — no direct connection |
| React ↔ MongoDB | Never — no direct connection |
| `packages/shared` ↔ Flutter | Never imported — Flutter maintains its own mirrored constants |
| FCM ↔ Server | Server sends push notifications via FCM HTTP API only |
| Maps SDK ↔ Clients | Client-side only — server never calls a maps API directly |

---

