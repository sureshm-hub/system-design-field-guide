# Decomposing a legacy/monolith using Domain-Driven Design (DDD)
- Decomposing is a process of transitioning from technical-based structuring 
  (e.g., packages named dao, servlet, util) to business-oriented modeling. 
- The goal is to identify Bounded Contexts—natural boundaries where specific domain models apply—and extract them 
  into independent, manageable services.

## Here is a step-by-step strategy to solve this decomposition.
- **Phase 1:** Strategic Analysis (Understand the Domain)
- Before changing code, analyze the existing system to understand business capabilities.
  1. **Event Storming/Domain Mapping:** Bring domain experts and developers together to map out business processes and 
    identify pivotal events (e.g., OrderPlaced, InvoiceGenerated).
  2. **Define Bounded Contexts:** Group related entities (e.g., Product, Inventory, Order) into contexts.
     * _**Tip:**_ Use the organizational structure (teams) to hint at boundaries.
  3. **Identify Core vs. Supporting Subdomains:**
     - **Core:** The "special sauce" (e.g., Pricing Engine).
     - **Supporting/Generic:** Necessary but not differentiator (e.g., Auth, Reporting).
  4. **Create Context Map:** Document how bounded contexts interact (e.g., Customer Service calls Order Service  
     via API).

- **Phase 2:** Refactoring Towards Modular Monolith
- Do not rewrite everything at once. Gradually make the legacy code more modular within the same application.
  1. **Stop Expanding the Monolith:** Any new functionality should be developed as a new service, not added to the  
    legacy code.
  2. **Introduce Package-by-Feature:** Refactor legacy code packages (com.app.dao) to be domain-oriented (com.app.
     order, com.app.customer).
  3. **Use Anti-Corruption Layers (ACL):** When new services need to interact with the legacy system, create a facade 
   (ACL) to prevent the old, messy data model from corrupting the new domain model.

- **Phase 3:** Extraction and Decomposition (Strangler Fig)
- Use the Strangler Fig pattern to replace legacy functionality incrementally.
  1. **Extract the First Service:** Pick a low-dependency module (e.g., a lookup service) or a high-value module (e.g., 
    Order Processing).
  2. **Database Decomposition:**
     - **Challenge:** Legacy J2EE apps often share a massive database.
     - **Solution:** Migrate tables related to the extracted context to a new, dedicated database. Use  
       database-level replication or Change Data Capture (CDC) to keep the old and new databases in sync during  the 
       transition.
  3. **Separate Frontend from Backend:** Split the UI from the back-end business logic. Use an API Gateway to route  
     traffic between the legacy backend and the new microservices.

- **Tactical DDD Patterns for Legacy Code**
  When refactoring the Java code, apply these patterns to turn "anemic" entities into rich domain models:
  - **Entities & Value Objects:** Look for persistent objects with setters and move the business logic from Services to 
  the Entities themselves (Rich Domain Model).
  - **Aggregates:** Identify the root entity that governs the lifecycle of a group of objects (e.g., Order is the root,
  OrderItem is a child).
  - **Repositories:** Define interfaces for data access in the domain layer, and implement them in the infrastructure  
  layer (JDBC/JPA).
  - **Domain Events:** Use asynchronous events (like RabbitMQ or Kafka) to communicate between bounded contexts 
  instead of direct synchronous calls, reducing coupling.

- **Key Challenges and Solutions**
  - **Tight Coupling:** If modules cannot be separated, start by introducing interfaces to loosen coupling before  
    extracting them.
  - **Shared State/Sessions:** Externalize HTTP sessions (e.g., using Redis) because new services must be stateless.
  - **Transaction Integrity:** Instead of distributed transactions (XA), use the Saga pattern (series of local  
    transactions with compensating actions).

https://docs.aws.amazon.com/prescriptive-guidance/latest/modernization-decomposing-monoliths/decomposing-patterns.html