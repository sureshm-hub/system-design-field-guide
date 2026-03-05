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
* **Strategic Design:** High-level, long-term mapping of the domain, focusing on how different parts of the system 
interact and align with business needs.
* **Tactical Design:** Specific technical patterns used to implement the model, such as Entities, Value Objects, 
Repositories, and Services.

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
  
**References:**
google: what is hexagonal architecture
https://softengbook.org/articles/hexagonal-architecture