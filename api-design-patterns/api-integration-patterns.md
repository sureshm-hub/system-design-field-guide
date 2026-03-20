Q1. How do you design an API for a long-running operation?
Strong answer

“For long-running tasks, I would not keep the client connection open. I’d use an async job pattern. The client submits work through POST /jobs, receives 202 Accepted with a job ID and location, then queries GET /jobs/{id} for status. For completion, I’d support polling first and optionally webhooks or events for advanced clients. I’d define clear job states like submitted, running, succeeded, failed, and cancelled. I’d also make submission idempotent to avoid duplicate jobs from retries.”

What they want

async pattern

202 semantics

status resource

retry/idempotency

failure states

L6 add-on

“At platform scale, I’d standardize the job lifecycle model and error schema across teams so clients don’t relearn each service.”

Q2. REST vs gRPC — when would you choose one over the other?
Strong answer

“REST is usually my default for externally consumed APIs because it is easy to debug, widely supported, cache-friendly, and works well for resource-oriented contracts. gRPC is better for internal service-to-service communication where low latency, strong typed contracts, and streaming matter. My choice depends on client ecosystem, observability tooling, debugging needs, and performance requirements rather than ideology.”

L6 add-on

“I’d avoid letting every team choose arbitrarily. At org scale, standardizing where REST vs gRPC is preferred prevents integration sprawl.”

Q3. How do you make write APIs safe under retries?
Strong answer

“I’d make non-idempotent writes retry-safe using an idempotency key supplied by the client. The server stores the first successful result keyed by that value and returns the same outcome for duplicate retries. I’d scope the key carefully, define retention windows, and ensure the operation is atomic with respect to deduplication. This is especially important for create operations like orders, payments, and job submission.”

Follow-up they may ask

Where do you store idempotency state?

Good answer:

transactional DB for correctness-sensitive operations

Redis possible for short-lived dedup if business risk is acceptable

Q4. How would you design pagination for a large dataset?
Strong answer

“For small stable datasets, offset pagination can be acceptable, but for large or frequently changing datasets I prefer cursor or keyset pagination. Offset becomes expensive and can produce inconsistent pages when records are inserted or deleted during browsing. Cursor pagination scales better and provides more stable traversal. I’d return a continuation token and keep sort order deterministic.”

Nice detail

“Cursor should be opaque to the client.”

Q5. How do you handle partial success in bulk APIs?
Strong answer

“For bulk APIs, I’d define whether semantics are atomic or best-effort up front. In many data and platform use cases, best-effort is more practical. Then the response should include item-level results with success/failure reason per record. I’d also define batch size limits and support idempotency where possible. If the operation must be atomic, I’d keep the batch size small because rollback cost grows quickly.”

L6 angle

“Bulk contracts should be standardized, otherwise every service invents its own partial-failure model.”

Q6. How do you version APIs without breaking clients?
Strong answer

“My first choice is backward-compatible evolution: add fields, avoid changing semantics, and tolerate unknown fields. I only introduce a new version when the contract breaks in a meaningful way. I’d publish deprecation timelines, usage metrics, and migration guides. For internal APIs, I’d also track consumer inventory so we know who is still on old versions.”

Excellent L6 line

“Versioning is not just a technical problem, it is an organizational dependency-management problem.”

Q7. When is an event-driven API better than synchronous request/response?
Strong answer

“Event-driven APIs are better when we need decoupling, fan-out, and asynchronous processing, especially if multiple downstream systems react independently to the same business fact. They reduce direct runtime coupling and improve extensibility. But they also introduce eventual consistency, duplicate handling, and schema evolution complexity, so I use them when those tradeoffs are justified.”

Great example

trade booked event

multiple consumers: reporting, risk, settlements, audit

Q8. What makes a good API error contract?
Strong answer

“A good error contract is consistent, machine-readable, and actionable. I’d return stable error codes, a human-readable message, correlation/request ID, and details only where safe. I’d distinguish client errors from server errors clearly and avoid leaking internals. For validation failures, I’d return field-level detail. For retries, I’d tell the client whether the error is retryable.”

Strong structure

HTTP status

app error code

message

request ID

field violations

retryability hint

Q9. How do you secure an internal API?
Strong answer

“I’d use layered controls: strong service identity, TLS in transit, authentication via mTLS or OAuth/OIDC depending on environment, authorization based on service roles/scopes, rate limits where appropriate, audit logging, and secret rotation. I’d also minimize data exposure in payloads and logs. Security should be enforced at both gateway and service level, not assumed from network location.”

L6 angle

“At scale, security patterns should be paved roads, not team-by-team custom implementations.”

Q10. How do you avoid chatty APIs?
Strong answer

“I’d first understand access patterns. If the client needs to call five endpoints to render one screen or complete one workflow, I’d consider aggregation, denormalized read models, or a BFF. I’d avoid overfetching too, so the solution is not always one giant payload. The right design balances round trips, payload size, backend complexity, and caching.”

Q11. How would you design APIs for eventual consistency?
Strong answer

“I’d make the consistency model explicit in the contract. If writes are accepted before all projections are updated, I’d return acknowledgment that the command was accepted and expose status or version state where needed. I’d avoid misleading clients into assuming read-after-write consistency if it does not exist. For critical workflows, I may provide a strongly consistent read path or a status endpoint.”

Excellent phrase

“Eventual consistency is manageable when it is explicit, observable, and bounded.”

Q12. What is the difference between API gateway, service mesh, and BFF?
Strong answer

“An API gateway is primarily north-south traffic management at the edge: auth, routing, throttling, and sometimes aggregation. A service mesh is east-west infrastructure between services: traffic policy, mTLS, retries, telemetry. A BFF is application-layer logic tailored to a specific client experience, often aggregating backend calls for web or mobile. They solve different concerns and should not be conflated.”

This is a very strong senior answer.

Q13. How do you design a public API differently from an internal API?
Strong answer

“For public APIs, I optimize for clarity, backward compatibility, documentation quality, rate limiting, safe defaults, and long-term support because client diversity is high and change is expensive. Internal APIs can be more optimized for organizational needs and may use stronger assumptions around trust, tooling, and rollout control. That said, internal APIs still need discipline because they often become de facto public inside the company.”

Q14. What observability would you build into APIs?
Strong answer

“I’d capture request counts, latency, error rates, saturation indicators, and per-endpoint SLIs. I’d include request IDs for traceability, structured logs, and distributed tracing across service calls. For asynchronous APIs, I’d also track queue depth, age, completion latency, retry counts, and dead-letter volume. Observability should support both operational triage and product-level usage insights.”

Q15. How do you design for backward compatibility in event schemas?
Strong answer

“I’d use schema governance with compatibility rules, typically additive evolution only. Producers should not remove or repurpose fields casually. Consumers should tolerate unknown fields and avoid assuming field order or exhaustive enums. For high-scale event ecosystems, schema registry and compatibility checks in CI are very important.”