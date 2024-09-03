# Learning Domain-Driven Design (Aligning Software Architecture and Business Strategy) by Vlad Khononov

## 1. Analyzing Business Domains

**Core subdomain**: 
 - A core subdomain is what a company does differently from its competitors. Gives specific advantage vs competitors, should be as hard for competitors to copy as possible—the company’s profitability depends on it. Must be implemented in house. Example: for a jewelry company the jewelry design. Would someone pay for it on its own? If so, this is a core subdomain.
 - Volatility: Require constant refinement and evolving , the competitive advantage must be maintained, add new features, optimize,…

**Generic subdomain**: 
 - Busines activities that all companies perform in the same way, so it's not that important. Example: the online shop to sell jewels or an IdentityProvider,
 - It's ok to just buy an existing product and not build your own. 
 - Are usually complex, are the things that you know you don’t know, if a competitors is doing it is for a good reason, for instance encryption algorithms
 - Volatility: Change over time, like bugfixes, patches, or replace by a new solution

**Supporting subdomains**: 
 - Support the company’s business but do not provide any com‐petitive advantage. Example, for google a subdomain to store a catalog of images or banners that will be displayed in the adds. 
 - Typically are ETL operations and CRUD interfaces, often it's jsut validate inputs and convert data from 1 structure to another. 
 - Would it be simpler and cheaper to hack your own implementa‐tion, rather than integrating an external one? If so, this is a supporting subdomain
 - Volatility: Do not change often, no competitive advantage so Why add features?


 Table: Differences between the 3 types of subdomains

| Subdomain type | Competitive advantage | Complexity | Volatility | Implementation     | Problem     |
|---             |:---                   |:---        |:---        |:---                |:---         |
| Core           | Yes                   | High       | High       | In-house           | Interesting |
| Generic        | No                    | High       | Low        | Buy/adopt          |   Solved    |
| Supporting     | No                    | Low        | Low        | In-house/outsource |    Obvious  |


**Subdomains** are sets of interrelated and coherent use cases., usually involve the smae actor, business entities and manipulate a related set of data. Core subdomains are the most important, volatile, and complex. It’s essential that we distill them as much as possible since that will allow us to extract all generic and supporting functionalities and invest the effort on a much more focused functionality. If drilling down further doesn’t unveil any new insights that can help you make software design decisions, it can be a good place to stop.

**Domain experts** are subject matter experts who know all the intricacies of the business that we are going to model and implement in code. Domain experts are either the people coming up with requirements or the software’s end users. The software is supposed to solve their problems. Domain experts represent the business. They are the people who identified the business problem in the first place and from whom all business knowledge originates. They are not the analysts gathering the requirements nor the engineers designing the system

## 2. Discovering Domain Knowledge

**Business problem** can be challenges associated with optimizing workflows and processes, minimizing manual labor, managing resources, supporting decisions, managing data, and so on. Appear both at the business domain and subdomain levels. Subdomains are finer-grained problem domains whose goal is to provide solutions
for specific business capabilities

**Knowledge** belongs to domain experts, we cannot  become domain experts and we should use their business terminology. The software has to mimic the domain experts’ way of thinking about
the problem

**Communication** of all stakeholders (domain experts, product owners, engineers, UI/UX designers, project managers, testers, analysts,...) is essential for knowledge sharing and project success 

Instead of the typical approach where communication goes from : Domain Expert -> Software Analyst -> Developers -> End users. Domain-driven design proposes a better way to get the knowledge from domain experts to software engineers.

**Ubiquitous language**: instead of relying on translations, stakeholders have to speak the same language. It's the language of the business, should consist of business domain–related terms only, no technical names(things like Singleton, iframe,... should never be used) and not ambiguous terms. Constantly evolves, is an ongoing process, if we add new features that involve new domain knowledge the ubiquitous language also evolves.

**Model**:  
- Is not a copy of the real world
- Is intended to solve a problem, and it should provide just enough information for that purpose
- Is supposed to capture the domain experts’ mental models their thought processes about how the business works to implement its function
- Has to reflect the involved business entities and their behavior, cause and
effect relationships, and invariants

## 3. Managing Domain Complexity
In complex environments **inconsistent models** can be common, for instance  _lead_ has a different meaning in marketing and sales departments. This term is ambiguous but each domain expert must use his own terminology, prefixing MarketingLead and SalesLead would not be correct, no expert would use that terminology and would be easy to make mistakes

**Bounded context** is the solution in domain-driven design: divide the ubiquitous language into multiple smaller languages, then assign each one to the explicit context in which it can be applied. Bounded contexts define the applicability of a ubiquitous language and of the model it represents. A ubiquitous language is ubiquitous only in the boundaries of its bounded
context

**Model boundary** : a model will expand to become a copy of the real world, it's boundary is defined by the bounded context

Bounded context size is not a deciding factor. But the wider it is the the harder is to keep it consistent and the smallerthey are integration overhead can be a problem. Reasons to have smaller bounded context are: create new software teams, separate development lifecycles,... When separating bounded contexts we should remember to not separate coherent functionality, related sets of data should be always part of the same bounded context.

In the analysis phase we identify or discover different subdomains, a subdomain resembles a set of interrelated use cases, on the other hand, bounded contexts are designed. Having a one-to-one relationship between bounded contexts and subdomains can be perfectly reasonable in some scenarios. In others, however, different decomposition strategies can be more suitable.

Bounded contexts should be physical boundaries, implemented as individual service/project, evolved and versioned independently of other bounded contexts, each bounded context can be implemented with different technologies. A bounded context can contain multiple subdomains. In such a case, the bounded context is a physical boundary, while each of its subdomains is a logical boundary. Logical boundaries bear different names in different programming languages (namespaces, modules, packages)

A bounded context should be implemented, evolved, and maintained by one team only. No two teams can work on the same bounded context. The relationship between teams and bounded contexts is one-directional: a bounded context should be owned by only one team. However, a single team can own multiple bounded contexts

## 4. Integrating Bounded Contexts

Bounded contexts can evolve independently, but they have to integrate with one another, touchpoints between bounded contexts are called contracts.
Design patterns for defining relationships and integrations between bounded contexts:

- **Cooperation**: implemented by teams with wellestablished communication or even a single team:

    - **Partnership**: integration between bounded contexts is coordinated in an ad hoc manner, One team can notify a second team about a change in the API and the second team adapts. Frquent synchronization and Continuous Integration would help

    - **Shared Kernel**: same model of a subdomain, or a part of it, will be implemented in multiple bounded contexts. Changes made to the shared model have an immediate effect on all the bounded contexts. Ideally, the shared kernel will consist only of integration contracts and data structures that are intended to be passed across the bounded contexts’boundaries. Continuous integration of changes is required because the shared kernel belongs to multiple bounded contexts. Should be applied only when the cost of duplication is higher than the cost of coordination. Example: for subdomains that change frequently, gradual modernization of a legacy system, to integrate bounded contexts owned by a single team.

- **Customer-supplier**: both teams can succeed independently and both are at the same level of power

    - **Conformist**: 1 team just accepts another's proposal

    -  **Anticorruption Layer**: a team does not accept other's proposal

    - **Open-Host Service**: The supplier wants to protect the consumer, so decides to create a public protocol named _published language_ so the consumer does not depend on him. Is the reversal of the _anticorruption layer_

- **Separate ways**: not collaborate at all. An example is a logging framework, duplicating functinoality might be less expensive than collaborating. Or with very different models, when implementing anticorruption layers would be more expensive than duplicating. This pattern should be avoided in core subdomains.

## 5. Implementing Simple Business Logic

**Transaction Script**: The pattern organizes the system’s business logic based on procedures, where each procedure implements an operation that is executed by the system’s consumer via its public interface. Each operation should either succeed or fail but can never result in an invalid state. In a relational database we jsut have to create a transaction to encapsulate all the actions we want to take. With distributed transactions, for example doing an insert and then publishing an Event  this is mroe complex,  you can use the outbox pattern. We should also try to have always idempotent operations, for instance, increasing a counter with retries can lead to errors unles the operation consists on reading the actual value then increasing it and if it fails do everything again, just in case in the middle other operations was executed. Transaction script should never be used for core subdomains, as this pattern won’t cope with the high complexity of a core subdomain’s business logic

**Active Record**: Also supports logic where the business logic is simple but on more complex data structures like trees and hierarchies. The ActiveRecords represent data structures and also implement CRUD operations. Encapsulate the complexity of mapping the in-memory object
to the database’s schema and can also contain business logic like validations. Typically used for CRUD operations

## 6. Tackling Complex Business Logic

**Domain Model pattern**: is intended to cope with cases of complex business logic. instead of CRUD interfaces, we deal with complicated state transitions, business rules, and invariants (rules that have to be protected at all times). Incorporates both behavior and data, should be plain old objects, objects implementing business logic without relying on or directly incorporating any infrastructural knowledge.
- **Value objects**: can be identified by the composition of its values, should be immutable objects and modifying one should create a new object. Example: A VO named RGBcolor made of 3 integers, changing 1 value results in a new color and 2 instances of the same color must have the same values. No Id is required because would allow duplication of values. We should not rely exclusiveley in primitive types, for example a string for phone is not correct, we should have a new type named PhoneNumber that includes his own validation, parsing functions even detecting the country. Use value objects for the domain’s elements that describe properties of other objects

- **Entities**: requires an explicit identification field to distinguish between the different instances of the entity. The identification Id, for example for a person should be PersonId can the Id can be a GUID and is usually immutable

- **Aggregates**: Aggregates are entities and their data is mutable. Must contain logic to validate all incoming modifications and ensure business rules are respected. Only aggregate's business logic is allowed to modify it's state. The state-modifying methods exposed as an aggregate’s public interface are often referred to as commands and usually implemented  as methods in the aggregate objects. Typically aggregates also have a Version field in the database to manage concurrency. All changes to the
aggregate’s state should be committed transactionally as one atomic operation, no system operation can assume a multi-aggregate transaction. A
change to an aggregate’s state can only be committed individually, one aggregate per database transaction. The need to commit changes in multiple aggregates signals a wrong transaction boundary, and hence, wrong aggregate boundaries. Aggregates are used to encapsulate entities, we could have multiles entities and value objects encapsulated in 1 agrgegate and in this case all belong to the same transaction boundary. s. Only the information that is required by the aggregate’s business logic to be strongly consistent should be a part of the aggregate, aggregates should be kept as small as possible.

How Aggregates can be modified?
    - Aggregate root: is the entity of the group of entities in an aggregate in charge of modifying the state of all the other dependent entities
    - Domain Events: message describing what has happened in the business domain and provide all the necessary data related to the event. An aggregate publishes its domain events. Other processes, aggregates, or even external systems can subscribe to and execute their own logic in response to the domain events


- **Domain services**: business logic that either doesn’t belong to any
aggregate or value object, or that seems to be relevant to multiple aggregates


## 7. Modeling the Dimension of Time

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

**Event-Sourced Domain Model**: The original domain model maintains a state representation of its aggregates and emits select domain events. The event-sourced domain model uses domain events exclusively for modeling the aggregates’ lifecycles. All changes to an aggregate’s state
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

## 8. Architectural patterns

## 9. Communication patterns

## 10. Design Heuristics

## 11. Evolving Design Decisions

## 12. EventStorming

## 13. Domain-Driven Design in the real world

## 14. Microservices

## 15. Event-Driven Architecture

## 16. Data Mesh