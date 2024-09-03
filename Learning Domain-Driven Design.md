# Learning Domain-Driven Design (Aligning Software Architecture and Business Strategy) by Vlad Khononov

## Part I. Strategic Design
### 1. Analyzing Business Domains

**Core subdomain**: 
 - A core subdomain is what a company does differently from its competitors. Gives specific advantage vs competitors, should be as hard for competitors to copy as possible—the company’s profitability depends on it. Must be implemented in house. Example: for a jewelry company the jewelry design. Would someone pay for it on its own? If so, this is a core subdomain.
 - Volatility: Require constant refinement and evolving , the competitive advantage must be maintained, add new features, optimize,…

**Generic subdomain**: 
 - Busines activities that all companies perform in the same way, so it's not that important. Example: the online shop to sell jewels or an IdentityProvider,
 - It's ok to just buy an existing product and not build your own. 
 - Are usually complex, are the things that you know you don’t know, if a competitors is doing it is for a good reason, for instance encryption algorithms
 - Volatility: Change over time, like bugfixes, patches, or replace by a new solution

**Supporting subdomains**: 
 - Support the company’s business but do not provide any competitive advantage. Example, for google a subdomain to store a catalog of images or banners that will be displayed in the adds. 
 - Typically are ETL operations and CRUD interfaces, often it's just validate inputs and convert data from 1 structure to another. 
 - Would it be simpler and cheaper to hack your own implementation, rather than integrating an external one? If so, this is a supporting subdomain
 - Volatility: Do not change often, no competitive advantage so Why add features?


 Table: Differences between the 3 types of subdomains

| Subdomain type | Competitive advantage | Complexity | Volatility | Implementation     | Problem     |
|---             |:---                   |:---        |:---        |:---                |:---         |
| Core           | Yes                   | High       | High       | In-house           | Interesting |
| Generic        | No                    | High       | Low        | Buy/adopt          |   Solved    |
| Supporting     | No                    | Low        | Low        | In-house/outsource |    Obvious  |


**Subdomains** are sets of interrelated and coherent use cases, usually involve the same actor, business entities and manipulate a related set of data. Core subdomains are the most important, volatile, and complex. It’s essential that we distill them as much as possible since that will allow us to extract all generic and supporting functionalities and invest the effort on a much more focused functionality. If drilling down further doesn’t unveil any new insights that can help you make software design decisions, it can be a good place to stop.

**Domain experts** are subject matter experts who know all the intricacies of the business that we are going to model and implement in code. Domain experts are either the people coming up with requirements or the software’s end users. The software is supposed to solve their problems. Domain experts represent the business. They are the people who identified the business problem in the first place and from whom all business knowledge originates. They are not the analysts gathering the requirements nor the engineers designing the system

### 2. Discovering Domain Knowledge

**Business problem** can be challenges associated with optimizing workflows and processes, minimizing manual labor, managing resources, supporting decisions, managing data, and so on. Appear both at the business domain and subdomain levels. Subdomains are finer-grained problem domains whose goal is to provide solutions
for specific business capabilities

**Knowledge** belongs to domain experts, we cannot  become domain experts and we should use their business terminology. The software has to mimic the domain experts’ way of thinking about the problem

**Communication** of all stakeholders (domain experts, product owners, engineers, UI/UX designers, project managers, testers, analysts,...) is essential for knowledge sharing and project success. Instead of the typical approach where communication goes from : Domain Expert -> Software Analyst -> Developers -> End users. Domain-driven design proposes a better way to get the knowledge from domain experts to software engineers.

**Ubiquitous language**: instead of relying on translations, stakeholders have to speak the same language. It's the language of the business, should consist of business domain related terms only, no technical names(things like Singleton, iframe,... should never be used) and not ambiguous terms. Constantly evolves, is an ongoing process, if we add new features that involve new domain knowledge the ubiquitous language also evolves.

**Model**:  
- Is not a copy of the real world
- Is intended to solve a problem, and it should provide just enough information for that purpose
- Is supposed to capture the domain experts’ mental models their thought processes about how the business works to implement its function
- Has to reflect the involved business entities and their behavior, cause and
effect relationships, and invariants

### 3. Managing Domain Complexity
In complex environments **inconsistent models** can be common, for instance  _lead_ has a different meaning in marketing and sales departments. This term is ambiguous but each domain expert must use his own terminology, prefixing MarketingLead and SalesLead would not be correct, no expert would use that terminology and would be easy to make mistakes

**Bounded context** is the solution in domain-driven design: divide the ubiquitous language into multiple smaller languages, then assign each one to the explicit context in which it can be applied. Bounded contexts define the applicability of a ubiquitous language and of the model it represents. A ubiquitous language is ubiquitous only in the boundaries of its bounded
context

**Model boundary** : a model will expand to become a copy of the real world, it's boundary is defined by the bounded context

Bounded context size is not a deciding factor. But the wider it is the the harder is to keep it consistent and the smaller they are integration overhead can be a problem. Reasons to have smaller bounded context are: create new software teams, separate development lifecycles,... When separating bounded contexts we should remember to not separate coherent functionality, related sets of data should be always part of the same bounded context.

In the analysis phase we identify or discover different subdomains, a subdomain resembles a set of interrelated use cases, on the other hand, bounded contexts are designed. Having a one-to-one relationship between bounded contexts and subdomains can be perfectly reasonable in some scenarios. In others, however, different decomposition strategies can be more suitable.

Bounded contexts should be physical boundaries, implemented as individual service/project, evolved and versioned independently of other bounded contexts, each bounded context can be implemented with different technologies. A bounded context can contain multiple subdomains. In such a case, the bounded context is a physical boundary, while each of its subdomains is a logical boundary. Logical boundaries bear different names in different programming languages (namespaces, modules, packages)

A bounded context should be implemented, evolved, and maintained by one team only. No two teams can work on the same bounded context. The relationship between teams and bounded contexts is one-directional: a bounded context should be owned by only one team. However, a single team can own multiple bounded contexts

### 4. Integrating Bounded Contexts

Bounded contexts can evolve independently, but they have to integrate with one another, touchpoints between bounded contexts are called contracts.
Design patterns for defining relationships and integrations between bounded contexts:

- **Cooperation**: implemented by teams with well established communication or even a single team:

    - **Partnership**: integration between bounded contexts is coordinated in an ad hoc manner, One team can notify a second team about a change in the API and the second team adapts. Frquent synchronization and Continuous Integration would help

    - **Shared Kernel**: same model of a subdomain, or a part of it, will be implemented in multiple bounded contexts. Changes made to the shared model have an immediate effect on all the bounded contexts. Ideally, the shared kernel will consist only of integration contracts and data structures that are intended to be passed across the bounded contexts’boundaries. Continuous integration of changes is required because the shared kernel belongs to multiple bounded contexts. Should be applied only when the cost of cuplication is higher than the cost of coordination. Example: for subdomains that change frequently, gradual modernization of a legacy system, to integrate bounded contexts owned by a single team.

- **Customer-supplier**: both teams can succeed independently and both are at the same level of power

    - **Conformist**: 1 team just accepts another's proposal

    -  **Anticorruption Layer**: a team does not accept other's proposal

    - **Open-Host Service**: The supplier wants to protect the consumer, so decides to create a public protocol named _published language_ so the consumer does not depend on him. Is the reversal of the _anticorruption layer_

- **Separate ways**: not collaborate at all. An example is a logging framework, duplicating functionality might be less expensive than collaborating. Or with very different models, when implementing anticorruption layers would be more expensive than duplicating. This pattern should be avoided in core subdomains.

## Part II. Tactical Design
### 5. Implementing Simple Business Logic

**Transaction Script**: The pattern organizes the system’s business logic based on procedures, where each procedure implements an operation that is executed by the system’s consumer via its public interface. Each operation should either succeed or fail but can never result in an invalid state. In a relational database we just have to create a transaction to encapsulate all the actions we want to take. With distributed transactions, for example doing an insert and then publishing an Event  this is more complex, you can use the outbox pattern. We should also try to have always idempotent operations, for instance, increasing a counter with retries can lead to errors unless the operation consists on reading the actual value then increasing it and if it fails do everything again, just in case in the middle other operations was executed. Transaction script should never be used for core subdomains, as this pattern won’t cope with the high complexity of a core subdomain’s business logic

**Active Record**: Also supports logic where the business logic is simple but on more complex data structures like trees and hierarchies. The ActiveRecords represent data structures and also implement CRUD operations. Encapsulate the complexity of mapping the in-memory object
to the database’s schema and can also contain business logic like validations. Typically used for CRUD operations

### 6. Tackling Complex Business Logic

**Domain Model pattern**: is intended to cope with cases of complex business logic. instead of CRUD interfaces, we deal with complicated state transitions, business rules, and invariants (rules that have to be protected at all times). Incorporates both behavior and data, should be plain old objects, objects implementing business logic without relying on or directly incorporating any infrastructural knowledge.
- **Value objects**: can be identified by the composition of its values, should be immutable objects and modifying one should create a new object. Example: A VO named RGBcolor made of 3 integers, changing 1 value results in a new color and 2 instances of the same color must have the same values. No Id is required because would allow duplication of values. We should not rely exclusiveley in primitive types, for example a string for phone is not correct, we should have a new type named PhoneNumber that includes his own validation, parsing functions even detecting the country. Use value objects for the domain’s elements that describe properties of other objects

- **Entities**: requires an explicit identification field to distinguish between the different instances of the entity. The identification Id, for example for a person should be PersonId can the Id can be a GUID and is usually immutable

- **Aggregates**: Aggregates are entities and their data is mutable. Must contain logic to validate all incoming modifications and ensure business rules are respected. Only aggregate's business logic is allowed to modify it's state. The state-modifying methods exposed as an aggregate’s public interface are often referred to as commands and usually implemented  as methods in the aggregate objects. Typically aggregates also have a Version field in the database to manage concurrency. All changes to the
aggregate’s state should be committed transactionally as one atomic operation, no system operation can assume a multi-aggregate transaction. A
change to an aggregate’s state can only be committed individually, one aggregate per database transaction. The need to commit changes in multiple aggregates signals a wrong transaction boundary, and hence, wrong aggregate boundaries. Aggregates are used to encapsulate entities, we could have multiple entities and value objects encapsulated in 1 agrgegate and in this case all belong to the same transaction boundary. Only the information that is required by the aggregate’s business logic to be strongly consistent should be a part of the aggregate, aggregates should be kept as small as possible. How Aggregates can be modified?
    - Aggregate root: is the entity of the group of entities in an aggregate in charge of modifying the state of all the other dependent entities
    
    - Domain Events: message describing what has happened in the business domain and provide all the necessary data related to the event. An aggregate publishes its domain events. Other processes, aggregates, or even external systems can subscribe to and execute their own logic in response to the domain events

- **Domain services**: business logic that either doesn’t belong to any
aggregate or value object, or that seems to be relevant to multiple aggregates


### 7. Modeling the Dimension of Time

**Event-sourced domain model pattern**: To be used when the business logic is complex and belongs to a core subdomain and also uses value objects, aggregates and domain events. This pattern uses the event sourcing pattern to manage the aggregate's state, instead of persisting an aggregate’s state, the model generates domain events describing each change.

**Event-sourcing**: Introduces the dimension of time into the data model, how data has been evolving. Instead of the aggregate's current state, an event sourcing based system persists events documenting every change in aggregate's lifecycle. We could have all the following events: the user was created in the system, the user has changed his address, the user has been contacted by phone,... This events are typically implemented in the Entity object as Apply methods, Example:

```csharp
public void Apply(ContactDetailsChanged @event)
{
    // TODO
}
```

With this pattern we can also implement *Search* that takes into account past data and look for a user name that maybe has been updated by a different user and different Analysis functionalities. All this events must be persisted in a database or a _event store_.  The 
**Event Store** should not allow modifying or deleting the events, only append and fetch all events.

**Event Sourced Domain Model**: The original domain model maintains a state representation of its aggregates and emits select domain events. The event-sourced domain model uses domain events exclusively for modeling the aggregates’ lifecycles. All changes to an aggregate’s state
have to be expressed as domain events. Each operation on an event-sourced aggregate follows this script:
- Load the aggregate’s domain events.
- Reconstitute a state representation—project the events into a state representation
that can be used to make business decisions.
- Execute the aggregate’s command to execute the business logic, and consequently,
produce new domain events.
- Commit the new domain events to the event store.

And provides the following advantages:
- Time traveling: events can be used to reconstitute an aggregate’s current state and past states
- Deep insight: provides
the flexible model that allows for transforming the events into different state representations
- AuditLog: persisted domain events represent a strongly consistent audit log of everything that has happened to the aggregates’ states
- Advanced optimistic concurrency management: raises an exception when the read data becomes stale—overwritten by another process—while it is being written.

Disadvantages:
- Learning curve
- Evolving model: evolving the events schemas can be very complex
- Architectural complexity
- Projecting events into a state representation indeed requires compute power, and that need will grow as more events are added to an aggregate’s list. This will be notice after 10k events per aggregate, but usually you won't have more than 100? We can also generate snapshots or use caches...
- Data cannot be deleted which is a problem for GDPR, we could encrypt data and remove the the encryption key when decomissioning making the data "unavailable".

### 8. Architectural patterns

**Layered Architecture**: Organized in horizontal layers, each layer addresses 1 technical concer: interaction with the consumers, implementing business logic, and
persisting the data. Each layer can hold a dependency only on the layer directly beneath it to reduce the knowledge shared between the layers. Not a good fit to implement a domain model because in this model the business entities (aggregates and value objects) should have no dependency and no knowledge of the underlying infrastructure
- *Presentation* Layer(user interface layer): Can be a GUI(web or dekstop), and CLI, API for integration with other systems, subscription to events in a message broker, message topics to publish outgoing events.
- [Optional] Service laver(application layer): acts as an intermediary between the program’s presentation and business logic layers, is a logical boundary not a physical service. The sme service layer can be used by multiple presentation layers
- Business Logic Layer(domain layer || model layer): implements and encapsulates the program’s business logic, is the heart of the software, active records or domain model is implemented here.
- Data Access Layer (infrastructure layer): provides access to persistence mechanisms: databases, in-memory stores, message bus, cloud storage,...
- Communication Between Layers:

**Ports & Adapters (hexagonal architecture)**: Like in *Layered architecture*, the *presentation layer* and *data access layer* represent integration with external components(databases, external services or GUIs) so they can be unified in a single Infrastructure "layer". Is a perfect architecture for domain model pattern.

Dependency inversion principle (DIP) states that high-level modules (which
implement the business logic) should not depend on low-level modules. The *business logic layer* should now have the central role and the infrastructure should depend on it.

Create an *Application layer* as a Facade for the system's public interface, this part describes all operations exposed. All layers depend on the Business logic layer which should be at the center of the Hexagon, the Application layer surrounds it and uses it and after is the Infrastructure layer.

The business logic layer defines “ports” that have to be implemented by the infrastructure
layer. The infrastructure layer implements “adapters”(concrete implementation of the ports). The abstract ports are resolved into concrete adapters in the infrastructure layer

**Command-Query Responsibility Segregation (CQRS)**: online transaction processing (OLTP) and online analytical processing (OLAP) may require different representations of the system’s data, we often have to balance the needs for scale, consistency, or supported querying models. An alternative to finding a perfect database is the polyglot persistence model: using multiple databases to implement different data-related requirements

There are two types of models: the command execution model and the read models:
- Command execution model: single model to executing operations that modify the system’s state (system commands), used to implement the business logic, validate rules, and enforce invariants
- Read models (projections): Read models are read-only, the system can define as many models as needed to present data to users or supply information to other systems. Data can reside in a durable database, flat file, in-memory cache

The system has to project changes from the command execution model to all its read models, whenever source tables are updated, the changes have to be reflected in the precached views, can be done in 2 ways:
- Synchronous projections: fetch changes to the OLTP data through the catch-up sub‐
scription model. Can be implemented using SQL Server's rowversion column or a custom implementation:

    - The projection engine queries the OLTP database for added or updated records
after the last processed checkpoint.
    - The projection engine uses the updated data to regenerate/update the system’s
read models.
    - The projection engine stores the checkpoint of the last processed record, will be used during the next iteration for getting records added or modified after the last processed record

- Asynchronous projections: the command execution model publishes all committed changes to a message bus. The system can subscribe to the published messages and use them to update the read models. If the messages are processed out of order or duplicated, inconsistent data will be projected into the read models

A command should always let the caller know whether it has succeeded or failed. If it has failed, why did it fail? Was there a validation or technical issue? The caller has to know how to fix the command. Therefore, a command can—and in many cases should—return data; for example, if the system’s user interface has to reflect the modifications resulting from the command

The CQRS pattern can be useful for applications that need to work with the same data in multiple models, potentially stored in different kinds of databases. The pattern supports domain-driven design’s core value of working with the most effective models for the task at hand, and continuously improving the model of the business domain

### 9. Communication patterns

**Model Translation**:
- Teams implementing 2 bounded contexts are collaborating effectively, in this case protocols can be coordinated or even created a *shared kernel*
- In a customer-supplier. The upstream (supplier) can act as an open-host service (OHS) and protect its consumers from changes to its implementation model and the downstream (consumer) can use an anticorruption layer (ACL)

The model’s translation logic can be either stateless or stateful:
- Stateless model translation: the bounded context that owns the translation (OHS for upstream, ACL for downstream) implements the proxy design pattern to interject the incoming and outgoing requests and map the source model to the bounded context’s target model
    - Synchronous: embed the transformation logic in the bounded context’s codebase. In an open-host service, translation to the public language takes place when processing incoming requests, and in an anticorruption layer, it occurs when calling the upstream bounded context. This logic can be extracted to an external API gateway, from AWS, Azure,...

    - Asynchronous: you can implement a message proxy (an intermediary component subscribing to messages coming from thesource bounded context). The proxy will apply the required model transformationsand forward the resultant messages to the target subscriber. Can be used to intercept the domain events and convert them into a published language, thus providing better encapsulation of the bounded context’s implementation details

- Stateful Model Translation: For more significant model transformationsa stateful translation may be required:
    - Aggregating incoming data: Could be that a bounded context is interested in aggregating incoming requests and processing them in batches for performance optimization, or  combining multiple finegrained messages into a single message containing the unified data. It cannot be implemented using an API Gateway, but there are solutions like Kafka, AWS Kinesis that are better than implementing your own.

    - Unifying multiple sources: A typical example for this is the backend-forfrontend pattern, in which the user interface has to combine data originating from multiple services

**Integrating Aggregates**: one of the ways aggregates communicate with the rest
of the system is by publishing domain events. The Aggregate entity should not be responsible of publising sending a Domain Event to update an item and after publishing the event in the message bus to communicate external systems, what if the domain event fails and event has been alreayd published? we would have an inconsistent state. Let's imagine now that at Application layer we load the Aggregate, we modify it and save it and after we publish the event to the message bus, What if the messages bus was not available? again an inconsistent state. to solve this we can use the **Outbox patter**.

- **Outbox**:  ensures reliable publishing of domain events using the following algorithm.
    - Both the updated aggregate’s state and the new domain events are committed in the same atomic transaction.
    - A message relay fetches newly committed domain events from the database.
    - The relay publishes the domain events to the message bus.
    - Upon successful publishing, the relay either marks the events as published in the database or deletes them completely.

    When using a relational database, we should have a specific table named *OutboxIntegrationEvents*. The outbox pattern guarantees delivery of the messages at least once. The publishing relay can fetch the new domain events in either a pull-based or pushbased manner:
    - Pull (polling publisher): continuously query the database for unpublished events, required proper DB indexes
    - Push (transaction log tailing): proactively call the publishing relay when new events are appended

    The outbox pattern can be used not only for publishing messages to a message bus, but also to implement asynchronous execution of external components.

- **Saga**: One of the core aggregate design principles is to limit each transaction to a single instance of an aggregate to ensure that an aggregate’s boundaries are carefully considered and encapsulated, but in ome cases you have to implement a business process that spans multiple aggregates. A saga is a long-running business process, not in terms of time but in terms of transactions because spans multiple transactions, involves multiple Aggregates and Events, if one of them fails the Saga is responsible of issuing the compensating actions to assure consistency, these Sagas require state management to take compensating actions in case of failure. Although the saga will eventually execute the relevant commands the transactions cannot be considered atomic, **Only the data within an aggregate’s boundaries can be considered strongly consistent.Everything outside is eventually consistent.**. For example this Sag is a very simple example, relies on the messaging infrastructure to deliver the relevant events, and it reacts to the events by executing the relevant commands, if your Saga requires state instead of messaging we shoudl use a event-sourced aggregate, persisting the complete history of received events and issued commands:
    - listen to the `CampaignActivated` event from the `Campaign aggregate`
    - listen the `PublishingConfirmed` and `PublishingRejected` events from the AdPublishing bounded context
    - The saga has to 
        - execute the `SubmitAdvertisement` command on AdPublishing
        - execute the `TrackPublishingConfirmation` and `TrackPublishingRejection` commands on the Campaign aggregate. `TrackPublishingRejection` acts as a compensation action

```csharp
public class CampaignPublishingSaga
{
    private readonly ICampaignRepository _repository;
    private readonly IPublishingServiceClient _publishingService;
    ...
    public void Process(CampaignActivated @event)
    {
        var campaign = _repository.Load(@event.CampaignId);
        var advertisingMaterials = campaign.GenerateAdvertisingMaterials();
        _publishingService.SubmitAdvertisement(@event.CampaignId,
        advertisingMaterials);
    }
    
    public void Process(PublishingConfirmed @event)
    {
        var campaign = _repository.Load(@event.CampaignId);
        campaign.TrackPublishingConfirmation(@event.ConfirmationId);
        _repository.CommitChanges(campaign);
    }

    public void Process(PublishingRejected @event)
    {
        var campaign = _repository.Load(@event.CampaignId);
        campaign.TrackPublishingRejection(@event.RejectionReason);
        _repository.CommitChanges(campaign);
    }
}
```

- **Process Manager**: The saga pattern manages simple, linear flow, a saga matches events to the corresponding commands. The process manager pattern is intended to implement a business-logic-based process, defined as a central processing unit that maintains the state of the sequence and determines the next processing steps, if a saga contains if-else statements to choose the correct course of action, it is probably a process manager. A saga is instantiated implicitly when a particular event is observed but a process manager cannot be bound to a single source event and requires explicit instantiation. Are typically implemented as aggregates(state based or event sourced), in some cases they can have an ID and also persist their state

## Part III. Applying Domain-Driven Design in Practice
### 10. Design Heuristics

A heuristic is not a hard rule that is guaranteed and mathematically proven to be correct in 100% of cases,  is an effective problem-solving approach that ignores the noise inherent.

**Bounded Contexts**: what is the optimal size of a bounded context? Software changes affecting multiple bounded contexts are expensive and require lots of coordination. Changes that invalidate the bounded contexts’ boundaries typically occur when the business domain is not well known or the business requirements change frequently. Refactoring logical boundaries is considerably less expensive than refactoring physical boundaries, it's a better idea to start with wider boundaries  and decompose them as you gain domain knowledge

**Business Logic Implementation Patterns**: 
- transaction script and active record patterns are better suited for subdomains with simple business logic. The transaction script pattern can be used for simple data structures, while the active record pattern helps to encapsulate the mapping of complex data structures to the underlying database.
- The domain model and its variant, the event-sourced domain model, lend themselves
to subdomains that have complex business logic: core subdomains

    How to choose? Is a core subdomain or a supporting/generic subdomain?
    - If core subdomain, needs monetary transactions, analysis or logs? Event-sourced domain model if NO domain model
    - If supporting/generic subdomain,  has complex data structure? ActiveRecord, if NOT then transactionScript

**Architectural Patterns**: we have three architectural patterns: layered architecture, ports & adapters, and CQRS:
- The event-sourced domain model requires CQRS
- The domain model requires the ports & adapters architecture. Otherwise, the
layered architecture makes it hard to make aggregates and value objects ignorant
of persistence
- The Active record pattern is best accompanied by a layered architecture with the
additional application (service) layer
- The transaction script pattern can be implemented with a minimal layered archi‐
tecture

**Testing Strategy**:
- Testing Pyramid: emphasizes unit tests, fewer integration tests, and even
fewer end-to-end tests. domain model patterns are best addressed with the testing pyramid
- Testing Diamond: focuses the most on integration tests. When the active record
pattern is used,  to focus on integrating the two layers, the testing pyramid is the more effective choice
- Reversed Testing Pyramid: most attention to end-to-end tests, perfect choice for transaction script architecture

### 11. Evolving Design Decisions

As an organization grows and evolves, it’s not unusual for some of its subdomains to morph from one type to another:

- Core to Generic: Imagine a retail company that develops it's own OrderDeliverySOlution. After some time an external company can create a better product can easily be integrated by third parties
- Generic to Core: An external solution that was being used was not giving the right answer, at some point is decided to develop a new solution internally. A good example of this Amazon Web Services
- Supporting to Generic: A few years after a specific tool has been developed internally appears a new open source tool that provides much better functionality
- Supporting to Core: a company finds a way to optimize the supporting logic in such a way that it either reduces costs or generates additional profits. if it enhances the company’s profitability, it’s a sign of a supporting subdomain becoming a core subdomain.
- Core to Supporting: If a core subdomain is not profitable, can eventually become a Supporting subdomain
- Generic to Supporting: If an open source solution suddenly becomes to complex to integrate, might be easier to jsut develop something internally

**Strategic Design Concerns**: A change in a subdomain’s type directly affects its bounded context and, consequently, corresponding strategic design decisions. The core subdomains have to protect their models by using anticorruption layers and have to protect consumers from frequent changes in the implementation models by using published languages (OHS).

**Tactical Design Concerns**: The main indicator of a change in a subdomain’s type is the inability of the existing technical design to support current business needs. Imagine you have a generic subdomain develop with a simple Layer architecture, over time will really hard to update and improve
- Transaction Script to Active Record: when working with data becomes challenging in a transaction script, refactor it into the active record pattern. Look for complicated data structures and encapsulate them in active record objects. Instead of accessing the database directly, use active records to abstract its model and structure
- Active Record to Domain Mode: If the business logic that manipulates active records becomes complex and you notice more and more cases of inconsistencies and duplications, refactor the implementation to the domain model pattern. Start by identifying value objects, then analyze the data structures and look for transactional boundaries and move all the state-modifying business logic is moved inside the boundaries of the corresponding objects. Those are good candidates for aggregates
- Domain Model to Event-Sourced Domain Model: you can transition a domain model to the event-sourced model. The problem with this mgirations is that all past events are not going to exist. The easiest is to create a mgiration message to make this mgiartion explicit and avoid confusions but the trace of this type of migration will always remain.

**Organizational Changes**: Changes in the organization’s structure can affect teams’ communication and collaboration levels and, as a result, the ways the bounded contexts should be integrated. For example create new teams and split the bounded contexts among the teams. This communication can move from "Partnership to Customer–Supplier" or  from "Customer–Supplier to Separate Ways"

**Domain Knowledge**: the domain model has to evolve and reflect that more domain knowledge ahs been acquired. But knowledge can also be lost: documentation is outdated, people leave the company

**Growth**: Growth is a sign of a healthy system. When new functionality is continuously added, it’s a sign that the system is successful but unregulated growth that leads to big balls of mud results from extending a software system’s functionality without re-evaluating its design decisions

**Subdomains**: subdomains’ boundaries can be challenging to identify, and as a result, instead of striving for boundaries that are perfect, we must strive for boundaries that are useful. As the business domain grows, the subdomains’ boundaries can become even more blurred, making it harder to identify cases of a subdomain spanning multiple, finergrained subdomains

**Bounded Contexts**: Instead of building a huge messy bounded context we should create smaller and specialized ones, but over time even them can lose focus and not be so eficient anymore, in that case we should refactor them.

**Aggregates**: As the system grows we add functionality to existing Aggregates , but aggregagtes should be small, at some point probably there should be new Aggregates to encapsulate this enw functionality. Try to always keep consisting data in yoru Aggregates

### 12. EventStorming
EventStorming is a low-tech activity for a group of people to brainstorm and rapidly model a business process. In a sense, EventStorming is a tactical tool for sharing business domain knowledge. Has a scope: the business process that the group is interested in exploring and step by step is enhanced adding new concepts. A diverse group is required: engineers, domain experts, UI/UX,... Can be done ina  room with a whiteboard and sticky notes. Usually is conducted in 10 steps:

- Step 1: Unstructured Exploration: a brainstorm of the domain events related to the business domain being explored. Should be formulated in past like *RequestApproved*
- Step 2: Timelines:  go over the generated domain events and organize them in the order in which they occur in the business domain starting with "happy paths"
- Step 3: Pain Points: Once you have the events organized in a timeline then find bottlenecks, manual steps or any other potential issue
- Step 4: Pivotal Events: now look for significant business events indicating a change in context or phase. things like “shopping cart initialized,” “order initialized,” that bare indicators of potential bounded context boundaries.
- Step 5: Commands: a domain event describes something that has already happened a command  describe the system’s operations and are formulated in the imperative like  *SubmitOrder*
- Step 6: Policies: are used for commands without a specific actor, a command is automatically executed when a specific domain event occurs.
- Step 7: Read Models: A read model is the view of data within the domain that the actor uses to make a
decision to execute a command
- Step 8: External Systems: An external system is defined as any system that is not a part of the domain being explored
- Step 9: Aggregates: An aggregate receives commands and produces events.
- Step 10: Bounded Contexts: look for aggregates that are related to each other



### 13. Domain-Driven Design in the real world

## Part IV. Relationships to Other Methodologies and Patterns
### 14. Microservices

A service is defined by its public interface, a microservice is a service with a micro-public interface. Reducing a service’s functionality also limits its reasons for change and makes the service more autonomous for development, management, and scale. In a proper microservices-based system, however decoupled, the services still have to be integrated and communicate with each other. *Local complexity* is the complexity of each individual microservice(how they are implemented), whereas global complexity is the complexity of the whole system(how microservices interact and depend on each other). A microservice must be a Deep Module and encapsulate a lot of compelxity and not a shallow module that introduces a component that doesn’t encapsulate its local complexity increasing the global one.

How to find the microservice boundaries:
- Bounded contexts’ boundaries do not correlate with the boundaries of effective microservices, microservices are bounded contexts but not every bounded context is a microservice. Bounded contexts impose limits on the widest valid boundaries. But have some things in common
    - Both microservices and bounded contexts are physical boundaries
    - Microservices, as bounded contexts, are owned by a single team
    - As in bounded contexts, conflicting models cannot be implemented in a microservice
- Aggregate’s boundary is the narrowest boundary possible, decomposing it into multiple physical services or bounded context would not be optimal. An aggregate is an indivisible business functionality unit that encapsulates the complexities of its internal business rules, invariants, and logic. Frequently having an aggregate as a service will increase the system's global complexity
- Subdomains would be a more balanced heuristic for defining boundaries when designing microservices because subdomains describe the capabilities—what the business does without explaining how the capabilities are implemented, they represent sets of coherent use cases

How can we make services deeper?
- Open-Host Service: encapsulates the complexity of the implementation that is not relevant to the service’s consumers. For example, it can expose less data and in a more convenient model for consumers
- Anticorruption Layer: reduces the complexity of integrating the service with other bounded contexts.

### 15. Event-Driven Architecture
Is an architectural style in which a system’s components communicate with one another asynchronously by exchanging event messages. The components publish events to notify other system elements of changes in the system’s domain. The components can subscribe to events raised in the system and react accordingly, a typical example of this is the *saga pattern*. EDA is not the same as Event Sourcing, EDA refers to the communication between services, while event sourcing happens inside a service. An event is a message, but a message is not necessarily an event. There are two types of messages:

- Event: A message describing a change that has already happened
- Command: A message describing an operation that has to be carried out

Both can be asynchronous but only the command can be rejected, for exampel if it's invalid, the event cannot be rejected because jsut reflects somethign that has already happened. There are 3 types of Events:

- Event notification: is a message regarding a change in the business domain that other components will react to. the message doens't need a lot of information, it's mainly a notification, the recipient then needs to retrieve all needed data, this also simplifies concurrency if the message arrives late
- Event-carried state transfer: ECST messages notify subscribers about changes in the producer’s internal state, include all the data reflecting the change
- Domain event: describe changes in the producer’s business domain but domain events include all the information describing the event. This events are designed to alleviate integration of components. The data in the domain event is not to describe the aggregate state, just to describe a business event.


### 16. Data Mesh