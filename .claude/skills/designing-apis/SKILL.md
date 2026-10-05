---
name: designing-apis
description: Designs and reviews HTTP/REST, gRPC, GraphQL, and event APIs — resource modeling, contracts (OpenAPI/AsyncAPI/protobuf), versioning, pagination, errors, idempotency, and backward compatibility. Use when creating or changing an endpoint, schema, SDK, webhook, or message contract.
metadata:
  role: senior-developer
  version: "1.0"
---

# Designing APIs

An API is a promise you cannot easily take back. Design the contract first, then implement.

## Workflow

1. **Consumers and use cases.** List who calls it and the 3–5 concrete tasks they need. Design for those, not for the database schema.
2. **Pick the style.**
   - REST/HTTP+JSON: public or broad-audience APIs, resource CRUD.
   - gRPC/protobuf: internal service-to-service, low latency, streaming.
   - GraphQL: many client shapes over one graph, client-driven queries.
   - Events (AsyncAPI, CloudEvents): notify others of facts; decouple in time.
3. **Write the contract** (OpenAPI 3.1, `.proto`, GraphQL SDL, AsyncAPI) before code. Put it in the repo and review it like code.
4. **Apply the rules below.** Lint the contract (e.g. Spectral for OpenAPI, `buf lint` + `buf breaking` for protobuf).
5. **Generate** server stubs / clients / docs from the contract where tooling allows.

## REST rules

- Resources are plural nouns: `/orders`, `/orders/{orderId}/items`. Verbs only for true actions: `POST /orders/{id}:cancel` or `/orders/{id}/cancellation`.
- Methods carry semantics: `GET` safe, `PUT`/`DELETE` idempotent, `POST` create/act, `PATCH` partial update (JSON Merge Patch or JSON Patch — pick one).
- Status codes: 200/201/202/204; 400 validation, 401 unauthenticated, 403 forbidden, 404 not found, 409 conflict, 412 precondition failed, 422 semantic error, 429 rate limited; 5xx only for server faults.
- Errors use RFC 9457 Problem Details (`application/problem+json`) with a stable machine-readable `type`.
- Pagination: cursor-based (`?cursor=…&limit=…`, return `next_cursor`) for large or changing collections; offset only for small static sets.
- Idempotency: accept an `Idempotency-Key` header on non-idempotent `POST` that creates side effects (payments, orders).
- Concurrency: `ETag` + `If-Match` for safe updates.
- Consistent naming (`snake_case` *or* `camelCase`, never both), RFC 3339 timestamps in UTC, string IDs, ISO 4217 currency with integer minor units.
- Security: authenticate every endpoint (OAuth 2.0 / OIDC), authorize at the object level, rate limit, never put secrets or PII in URLs.

## Compatibility and versioning

Backward-compatible (safe): add optional field, add endpoint, add enum value *if clients were told to tolerate unknowns*.
Breaking: remove/rename field, change type or meaning, make optional → required, change error codes, tighten validation.

- Prefer evolving without versions; when you must break, version the API (`/v2`, or a media-type/header version) and run both with a published deprecation date (`Deprecation` and `Sunset` headers).
- protobuf: never reuse or change field numbers; `reserved` removed ones.
- Events: version the schema; consumers must ignore unknown fields.

## Review checklist

- [ ] Contract file exists and lints clean
- [ ] Every operation has auth, validation, documented errors, and examples
- [ ] Lists are paginated; writes that create side effects are idempotent
- [ ] No breaking change, or a versioning + deprecation plan exists
- [ ] Names consistent with the rest of the API
- [ ] Rate limits, payload size limits, and timeouts defined
