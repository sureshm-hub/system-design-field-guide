# **Orchestration:**
- Microservice orchestration is a centralized management approach where a single controller—the 
  "orchestrator"—coordinates interactions, workflows, and dependencies between multiple independent services. It acts  
  like a conductor, managing service discovery, load balancing, and error handling to ensure reliability and 
  simplify complex, distributed processes

  - **Key Aspects of Orchestration:**
    * Centralized Control: A single, central component governs the interaction flow.
    * Workflow Management: The orchestrator defines and manages the sequence of steps for business processes.
    * Error Handling & Retries: Centralized monitoring allows for easier troubleshooting and automatic handling of service failures.
    * Service Discovery: The orchestrator helps services find and communicate with each other.
    * Scalability: Facilitates scaling individual services based on demand.

# **Choreography:**
- choreography: decoupled producers and consumers that coordinate long-lived distribution transactions using events
- Services interact independently via events without a central controller.
- "Kafka" is the goto for event driven choreography
- 

# **Saga:**
- **How:** Each service performs its own local transaction and publishes an event. Other services listen and execute 
  the next step.
- **Failure:** compensating transactions or rollback steps
  - if one saga step fails, we need to roll back a different step
- **Types:**
  - Choreography
  - Orchestration:
- **Reliability:**
  - Transactional Outbox
  - CDC (Tight DB Coupling)

# **Outbox:**
-  ensures reliable communication between microservices by atomically updating a database and publishing messages. 
   It solves the dual-write problem by saving events into an "outbox" table within the same transaction as the 
   business data, which are then asynchronously published to a message broker
- **key Components:**
  - Outbox Table: A table in the service's database that stores pending messages.
  - Message Relay (Publisher)
- Eventual Consistency: The pattern guarantees that messages are delivered at least once, promoting eventual consistency
  between services.
- Idempotency Requirement: Because the message relay might publish the same message multiple times 
  (e.g., after a crash), consumers should be designed to handle duplicate messages.
- Alternatives for Publishing:
  Polling Publisher: Periodically queries the outbox table for new entries.
  Transaction Log Tailing (CDC): Uses tools like Debezium to read the database transaction log directly, reducing 
  load on the database

# **CQRS:**
- data modification operations (commands) from data retrieval operations (queries) into distinct models and often, 
  separate physical services and databases
- **Context:** You have applied the Microservices architecture pattern and the Database per service pattern. As a 
  result, it is no longer straightforward to implement queries that join data from multiple services. Also, if you
  have applied the Event sourcing pattern then the data is no longer easily queried.
- **Key Components**
  - Commands: These are task-based requests that change the state of the system (e.g., "Book hotel room," 
    "CreateOrder"). They encapsulate validation and business logic and are processed by the write model.
  - Queries: These are requests that retrieve data without altering the system's state. They return Data Transfer 
    Objects (DTOs) and bypass complex business logic to optimize for read performance.
  - Command Model (Write Side): This part focuses on data integrity and consistency, often using a normalized database 
    schema and ensuring ACID properties.
  - Query Model (Read Side): This part is optimized for fast data retrieval, often using denormalized schemas, NoSQL 
    databases, or caching mechanisms to avoid complex joins.
  - Synchronization Mechanism: To keep the read and write databases consistent, an event-driven architecture is 
    typically used. When a command updates the write database, an event is published to a message broker, which the 
    read model subscribes to and uses to update its own data store in near real-time. This introduces eventual 
    consistency.

# **Event Sourcing:**
- is a data persistence strategy
- Event Sourcing (ES):
  ES is a persistence pattern that stores all changes to an application's state as an immutable, time-ordered sequence 
  of events, which acts as the single "source of truth". The current state is then derived by replaying these events.

# **Backpressure:**
- Back pressure occurs when the incoming flow of events exceeds the system’s processing capacity
  - Increased latency and delayed event processing
  - Overloading of downstream services
  - Loss or incomplete processing of events
- Handling Strategies:
  - Rate Limiting: Controlling the Flow of Incoming Events
  - Load Shedding: Discarding Non-Essential Events During High Load
  - Queue Depth Monitoring: Scaling Based on Backlog Size
  - Reactive Streams: Built-in Backpressure Handling
  - Circuit Breaker: Protecting Downstream Services
  - Dead Letter Queues (DLQ): Handling Failed Events Gracefully
  - Combining Strategies for Optimal Back-Pressure Control
    - Kafka + HPA/Kubernetes for Scalable EDA

distributed system:
https://medium.com/@ali.gelenler/architectural-microservices-patterns-saga-outbox-and-cqrs-with-kafka-25469d75c18c
https://microservices.io/patterns/data/cqrs.html

Adv. Error handling/Backpressure in Saga: 
https://medium.com/ssense-tech/exploring-advanced-error-handling-patterns-with-event-driven-architecture-part-i-e2f37741d904
https://medium.com/@mokarchi/managing-back-pressure-in-event-driven-architectures-fe370aa82df1