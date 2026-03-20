2) Core API patterns you should know

These are the big ones.

1. Request/Response CRUD APIs

Classic synchronous API.

Examples:

get customer

create order

update account config

Good for:

simple reads/writes

low latency

immediate confirmation

Watch for:

timeouts

partial failure

coupling between client and server

2. Resource-oriented REST APIs

Treat domain objects as resources.

Examples:

GET /accounts/{id}

POST /orders

PATCH /positions/{id}

Good for:

public/internal business APIs

discoverability

standard semantics

Watch for:

overly chatty APIs

poor modeling

forcing everything into CRUD even when workflow is better

3. Action/Command APIs

Sometimes the operation is not simple CRUD.

Examples:

POST /trades/{id}:simulate

POST /payments/{id}:authorize

POST /jobs/{id}:cancel

Good for:

workflows

business operations

state transitions

Strong point to mention:
“Not every operation should be modeled as a resource update. Some actions are better expressed explicitly as commands.”

4. Asynchronous API pattern

Client submits work, gets a job ID, polls or receives callback.

Examples:

report generation

large file ingestion

risk simulations

bulk recomputation

Flow:

POST /jobs

returns 202 Accepted + jobId

GET /jobs/{id} for status

optional webhook/event when complete

Good for:

long-running work

heavy compute

smoothing load

Watch for:

status model

retries

duplicate submissions

cancellation semantics

5. Idempotent write APIs

Very important in distributed systems.

Example:

client sends Idempotency-Key

repeated retries do not create duplicate orders/payments/jobs

Use when:

network retries

at-least-once delivery

payment/order/job creation

Strong interview phrase:
“For any externally triggered create operation that may be retried, I would support idempotency keys.”

6. Pagination patterns

For large result sets.

Common types:

offset pagination

cursor/token pagination

keyset pagination

Best practice:

prefer cursor/keyset for large mutable datasets

Tradeoff:

offset is simpler

cursor scales better and avoids drift issues

7. Filtering, sorting, searching APIs

Examples:

GET /orders?status=OPEN&from=...&to=...

GET /trades?book=FX&sort=tradeDate

Good answer includes:

indexed fields

bounded filters

avoid arbitrary queries unless using search service

consistent query syntax

8. Bulk APIs / Batch APIs

Examples:

bulk create/update

batch fetch

batch delete

Useful when:

client would otherwise make 1000 calls

network overhead matters

ETL/data platform integrations

Watch for:

partial success reporting

request size limits

atomic vs best-effort semantics

9. Event-driven API / Webhook pattern

System emits events instead of forcing polling.

Examples:

order created

trade booked

report completed

account status changed

Good for:

decoupling

near real-time integrations

fan-out

Risks:

delivery guarantees

duplicate events

schema evolution

consumer lag

10. API Gateway / Aggregation pattern

Gateway fronts multiple backend services.

Use for:

auth

routing

rate limiting

response aggregation

protocol translation

Watch for:

gateway becoming business-logic monolith

latency amplification

too much coupling at edge

11. BFF pattern (Backend for Frontend)

Separate backend per client experience.

Examples:

mobile BFF

web BFF

internal ops UI BFF

Use when:

different clients need different payloads

UI-specific aggregation needed

Tradeoff:

better client optimization

more services to maintain

12. CQRS-style API separation

Separate read and write paths.

Use when:

reads and writes scale differently

complex reporting/search

event-driven architecture

Example:

write API updates operational store

read API serves denormalized projections

Good L6 angle:
“This improves read scalability and autonomy, but introduces eventual consistency that clients must understand.”

13. Versioning pattern

How APIs evolve safely.

Options:

URI versioning /v1/orders

header/media-type versioning

additive evolution without version bump

Best answer:

prefer backward-compatible additive changes

use explicit deprecation policy

version only when contract truly breaks

14. Contract-first APIs

Design schema first using:

OpenAPI

protobuf/gRPC

AsyncAPI for events

Benefits:

consistency

code generation

reviewability

governance

Very strong L6 point:
“At scale, contract-first design reduces drift between teams and improves platform consistency.”

15. gRPC vs REST pattern

gRPC:

strong contracts

efficient binary protocol

good service-to-service

streaming support

REST:

easier external/client integration

human-friendly

broadly interoperable

Great answer:
“I usually prefer REST at external boundaries and gRPC for internal low-latency service-to-service paths, unless ecosystem constraints suggest otherwise.”

https://chatgpt.com/g/g-p-698f44b7ee788191823229d54bda6877/c/69b38a22-38e0-832e-8ef2-9c510fd26c20