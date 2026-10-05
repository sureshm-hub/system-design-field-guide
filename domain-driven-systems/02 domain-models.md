**Rich Domain models:**
- Rich Domain Objects (RDO) encapsulate both data and behavior within entities
to enforce business rules directly
- Rich models improve encapsulation, testability, and maintainability for complex
systems
- Rich objects (DDD Entities) contain business logic
-  Rich entities protect their own state, often using private setters and only 
allowing modifications through methods that uphold business invariants 
- Rich models reduce the duplication of business logic and improve 
"discoverability" of domain rules
- Rich models are ideal for complex business logic (e.g., banking)

**Why Choose Rich Objects (RDO):**
- Encapsulation: Ensures an entity never enters an invalid state.
- Testability: Business logic embedded in the entity is easier to unit test.
- Expressiveness: Models reflect real-world behaviors and the "ubiquitous language".
- Choose Rich Objects if you are practicing Domain-Driven Design (DDD) for a 
  complex system. It prevents the system from becoming a "spaghetti" mess of 
  services and ensures your business rules are always enforced.

**Implementation:**
- Entities are implemented using Value Objects
- prefer value objects over entities (immutable + light weight + business logic)
- identity equality vs referential equality vs value/structure equality

---

**Anemic models:**
- Anemic Domain Models separate data (entities) from behavior (services). 
- Anemic models are better suited for simple, CRUD-heavy applications.
- Anemic entities are simple containers (getters/setters) with logic in services
- Anemic models can lead to logic scattering across services
- Anemic is often preferred for simple CRUD applications

**When to Use Entities (Anemic):**
- Simple CRUD-driven applications where logic is minimal.
- Frameworks that prefer dumb data transfer objects (DTOs).
- Choose Anemic Entities if you are building a **simple data-entry tool or 
  an MVP** where speed is more important than **"long-term maintainability"**.

https://enterprisecraftsmanship.com/posts/entity-vs-value-object-the-ultimate-list-of-differences/
https://medium.com/unil-ci-software-engineering/on-rich-domain-entities-in-ddd-and-use-cases-in-clean-architecture-e8fa695124b3