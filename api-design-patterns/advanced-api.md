## 4) What better answers sound like at L6

At L6, add one more layer to every answer:

A. Standardization

“I’d create a reusable pattern, not solve it one-off.”

B. Governance

“How do teams adopt this? How do we review contracts?”

C. Operability

“What happens under failure, retry storms, or partial outage?”

D. Evolution

“How does this change safely over 3 years?”

E. Consumer empathy

“How easy is this for clients to use correctly?”

A typical L6-style sentence:

“I’d not only design the API itself, but also define standard conventions for idempotency, error modeling, and versioning so teams can build consistently and consumers get predictable behavior across the platform.”

That sounds strong.

## interviewers love hearing

These phrases usually land well:

“I’d make the consistency model explicit.”

“I’d design retries to be safe, not just possible.”

“I’d standardize this pattern across services.”

“I’d optimize for client correctness.”

“I’d separate contract semantics from transport choice.”

“I’d define failure states up front.”

“I’d prefer additive evolution over breaking version churn.”

“I’d treat observability as part of the API design.”


## cheat-sheet

5) Cheat sheet you can memorize
### API design principles

model the contract around consumer use cases

make common paths simple

be explicit about consistency and failure

design retries safely

evolve additively when possible

optimize for observability and operability

### Patterns to mention

REST/resource APIs

command/action APIs

async job APIs

batch APIs

webhook/event APIs

BFF

gateway

CQRS

idempotent writes

cursor pagination

contract-first design

### Common tradeoffs

REST vs gRPC

sync vs async

atomic vs partial success

offset vs cursor pagination

gateway aggregation vs backend coupling

rich payload vs chatty calls

strong consistency vs scalability

### Production concerns

authn/authz

rate limiting

retries and backoff

idempotency

schema evolution

observability

SLOs

deprecation strategy

# mock questions

Design an API for submitting a risk simulation that may run for 20 minutes.

How would you handle duplicate client retries for trade submission?

When would you choose an event over a direct API call?

Design pagination for a trades endpoint with 500 million records.

How would you evolve an API used by 40 internal teams?

What belongs in an API gateway and what should not?

How would you design a bulk ingestion API with partial failures?

How would you expose job status and cancellation?

REST vs gRPC for internal platform services?

How do you make APIs observable and debuggable in production?

https://chatgpt.com/g/g-p-698f44b7ee788191823229d54bda6877/c/69b38a22-38e0-832e-8ef2-9c510fd26c20