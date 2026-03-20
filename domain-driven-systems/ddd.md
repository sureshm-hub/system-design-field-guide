# Domain Driven Design
* Domain-driven architectures, based on Domain-Driven Design (DDD), structure software by focusing on the core 
business problem (domain) rather than technology

## **Key Characteristics and Components:**
* **Core Domain Focus:** The domain model is the heart of the system, designed first and kept independent of external 
factors like databases or APIs.
* **Ubiquitous Language:** A common language is shared between developers and domain experts, used in both conversation 
and code (e.g., classes and methods) to reduce miscommunication.
* **Bounded Contexts:** Large systems are broken into smaller, logical, and self-contained sub-domains, with each 
context having its own specific model.
  * Large, complex systems are divided into smaller, well-defined "bounded contexts," each with a  
   specific, cohesive domain model and a clear boundary
* **Domain mapping:** is the process of mapping the entities, aggregates, events, and other domain constructs to the 
  code that implements the system.
  * This helps ensure that the software design accurately reflects the business domain.
* **Aggregates:** These are sets of related objects that are treated as cohesive units within the domain. 
  * Each aggregate has a root entity, known as the aggregate root, which acts as the single entry point for the 
    aggregate and encapsulates its internal consistency and integrity.
* **Strategic Design:** High-level, long-term mapping of the domain, focusing on how different parts of the system
  interact and align with business needs.
* **Tactical Design:** Specific technical patterns used to implement the model, such as Entities, Value Objects,
  Repositories, and Services.

## Building Blocks
  * Entity: primary concept in DDD is the Entity. It is a domain object that has an ID which distinguishes it from  
    all other objects
  * Value Object: Value Objects can't be changed once made and don't have a unique ID. They describe certain details 
    of the business area.
  * Domain Event: An object that is used to record a discrete event related to model activity within the system.
  * Aggregate: A cluster of entities and value objects with defined boundaries around the group. Rather than 
    allowing every single entity or value object to perform all actions on its own, the collective aggregate of  
    items is assigned a singular aggregate root item
  * Service: Domain Services contain specific domain logic that doesn't naturally fit within an Entity or a Value 
    Object.
  * Repositories: Repositories handle the object's life cycle. They retrieve domain objects (Entities) by querying 
    the persistence layer (usually a database).
    * DDD meaning of a repository is a service that uses a global interface to provide access to all entities and 
      value objects that are within a particular aggregate collection. Methods should be defined to allow for 
      creation, modification, and deletion of objects within the aggregate. However, by using this repository 
      service to make data queries, the goal is to remove such data query capabilities from within the business 
      logic of object models.
  * Factories: DDD suggests the use of a factory, which encapsulates the logic of creating complex objects and  
    aggregates, ensuring that the client has no knowledge of the inner-workings of object manipulation.
  * Application Layer: This is where the use cases of the system are implemented. The application layer manages and 
    directs the main business logic to achieve its purpose.
    * Application Layer as the Orchestrator: The application layer in Layered Architecture aligns with the  "Use 
      Cases" layer in Clean Architecture. This layer orchestrates workflows, invoking domain logic without  
      implementing it directly.

## **Common Architectural Patterns:**
* DDD is often implemented using these structures:
**Layered Architecture:** Separates code into UI, Application, Domain, and Infrastructure layers.
**Hexagonal Architecture (Ports and Adapters):** Isolates the domain logic at the center, communicating with the 
outside world via ports and adapters.

* Benefits
Reduced Complexity: Manages complex business logic by breaking it down into smaller, manageable parts.
Better Communication: Bridges the gap between technical teams and business stakeholders.
Flexibility: Allows for easier updates or replacement of subsystems without affecting the core model

# Hexagonal Architecture

## What is Hexagonal Architecture
* A Software design pattern that decouples core business logic from external technologies like databases, UIs, or API 
  clients
  * Key Concepts and Components
    **The Inside (Core/Domain):** Contains the business logic and domain model. It is unaware of the outside world, 
    making it highly testable and technology-agnostic.
    **Ports (Interfaces):** Defined by the core, these are entry/exit points (interfaces) for communicating with the 
    outside world.
     * **Driving Ports (Primary):** Used by external actors to invoke business logic (e.g., REST API, CLI).
     * **Driven Ports (Secondary):** Used by the core to call external systems (e.g., Database, Mail service).
    **Adapters (Implementations):** Translate data from the outside format to the core's format and vice-versa.
     * POJO's with Domain logic, Nested
     * Adapters are the "glue" that connects your application to the outside world.
     * **Driving Adapters:** Convert external requests (e.g., HTTP request) into method calls on the port.
     * **Driven Adapters:** Implement the interfaces to talk to specific technologies (e.g., a SQL database adapter).

## What are the features?
* A Hexagonal Architecture divides the classes of a system into two main groups:
   **Domain classes**, which are directly related to the system’s business logic.
   **External classes**, such as those related to the user interface and integration with external systems (such as 
  databases)
  Importantly, **domain classes should not depend on classes related to infrastructure, user interface, or external 
  systems.**
  * In a Hexagonal Architecture, communication between the classes of these groups is mediated by adapters
  * Each face of the hexagon represents some **reason** the application is trying to talk with the outside world. 
    This is why it is concentric hexagons and not concentric circles.
  * **Driving/Driven Ports/Adapters** are also called **Provided/Required Ports/Adapters** or **Primary/Secondary 
    Ports/Adapters**
  * Adapter is an implementation of the same-name design pattern
  * Ports are primarily Interfaces and implemented using Strategy Design Pattern

## Layers
  * Application layer (or Use Case layer) is the "brain" of your specific software
    * DTO's to communicate data between various layers
  * Infrastructure Layers
  * Presentation Layer
  * Domain Layer

## Evolving Legacy Systems to DDD
* Legacy systems often pose challenges around maintainability, scalability, and adaptability to evolving business 
  requirements. Domain-Driven Design (DDD) provides a structured methodology to modernize and refactor these systems 
  by emphasizing the core domain and aligning software design with business objectives.

* Legacy systems are typically characterized by monolithic architectures, obsolete technologies, and tightly coupled 
  components—factors that impede maintainability and extensibility. Applying DDD principles demands a deliberate, 
  phased approach to minimize risk and ensure business continuity during transformation.

###  Essential Elements
1. **Domain Discovery & Analysis**
   * **What are the Core Domain Areas:** Pinpoint critical business processes and high-value functionalities
   * **What are the Dependencies:** Map internal and external dependencies, including data flows, service 
     interfaces, and integration points.
   * **What's Driving the Change:** Quantify hotspots with high maintenance overhead, recurring defects (techdebt), or 
     scalability limitations.
     
2. **Ubiquitous Language**
   * **Engaging Stakeholders:** Collaborate with domain experts, product owners, and business analysts to co-create 
     a shared domain vocabulary..
   * **Documenting Domain Concepts:** Maintain a living glossary of domain terms to enforce consistency across code, 
     documentation, and communication.

3. **Bounded Context Carving**
   * Bounded contexts reduce systemic complexity by partitioning the domain into cohesive, independently  evolvable 
     units.
   * **Identify Natural Boundaries:** Detect seams where contexts can be delineated—such as lines of business,  
     functional modules, or ownership areas.
   * **Decouple Components:** Incrementally decouple shared dependencies within each bounded context to enable 
     autonomous deployment and scaling.

4. **Refactoring the Domain Model**
   Legacy systems frequently exhibit a weak domain model, with business logic fragmented across layers. Refactoring 
   focuses on restoring behavioral richness and model integrity.
   * **Centralizing Business Logic:** Consolidate domain logic within entities and aggregates to ensure 
     encapsulation and coherent behavior.
   * **Simplifying Entities:** Eliminate redundant attributes and methods, retaining only domain-relevant state and 
     operations.
   * **Introducing Value Objects:** Leverage value objects for immutable, identity-less concepts to enhance model 
     expressiveness and integrity.

5. **Anti-Corruption Shielding:**
   * **Encapsulate Legacy APIs:** Wrap legacy contracts to shield domain from impurities.
   * **Enable Incremental Replacement:** Proxy façade for phased strangulation.

6. **Event Storming Integration:**
   * **Emit Domain Events:** Publish bounded context events for loose coupling.
   * **React to Event Flows:** Subscribe via handlers for cross-context orchestration.

7. **Strangler Fig Migration:**
   * **Incremental Facade Routing:** Intercept and route to new services progressively.
   * **Parallel Run Validation:** Canary new capabilities alongside legacy.

8. **Invariant Data Guardianship:**
   * **Eventual Consistency Migration:** CDC pipelines with dual writes/at-least-once semantics.
   * **Saga Transaction Orchestration:** Compensating actions across distributed aggregates.

## Alternatives to Hexagonal Architecture:

### Layered Architecture (n-tier / 3-tier)
### Microkernel Architecture (Plug-in Architecture)
### Microservices Architecture
### Serverless / Function-as-a-Service (FaaS)




**References:**
google: what is hexagonal architecture
https://softengbook.org/articles/hexagonal-architecture
https://www.linkedin.com/pulse/domain-driven-design-considerations-legacy-systems-vintageglobal-uidxe/