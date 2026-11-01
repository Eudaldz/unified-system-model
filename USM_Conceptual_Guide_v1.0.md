# Unified System Model (USM)
**A practical guide to modeling software systems across abstraction levels and states of realization**

Version 1.0 — Conceptual Guide

---

## 1. Introduction
The **Unified System Model (USM)** is an information model for describing software systems in a consistent, structured, and continuously evolving way.

Its purpose is to establish a shared understanding of a software system for both humans and AI agents, supporting software design, development, maintenance, and evolution.

Software systems have a fundamental documentation challenge: **what exists today is not necessarily what has been designed or agreed upon.**

A capability may be approved but not yet implemented. An architecture may be partially migrated. An existing implementation may contradict a newly established business rule.

Traditional documentation often mixes these perspectives, making it difficult to distinguish reality from intent.

USM addresses this by describing **one software system through two independent dimensions**:

1. **Abstraction:** At what level are we describing the system?
2. **State:** Are we describing what exists, what is intended, or what is being explored?

These dimensions form a common foundation for documentation that remains understandable regardless of application size, architectural style, or development methodology.

USM draws on principles from Domain-Driven Design (DDD), Clean Architecture, and Specification-Driven Development, but is not dependent on any particular methodology.

### Core principles
- **One system, one model.** All representations describe the same underlying software system.
- **Abstraction and state are independent.** Every abstraction level can represent reality, commitment, and exploration.
- **Concept-oriented documentation.** Information is organized around system concepts, not around document types or development activities.
- **Explicit authority.** Readers must always know whether information describes existing behavior, accepted intent, or an alternative.
- **Progressive discovery.** Readers should be able to understand the system from a single entrypoint and navigate toward relevant details.
- **Technology and scale independence.** The model works for small applications, modular monoliths, and distributed systems.

---

## 2. The Two Dimensions of USM
USM describes the software system through two orthogonal dimensions: **Abstraction** and **State**.

### 2.1 Dimension One: Abstraction
Abstraction determines the level of detail at which the software system is described.

USM defines three abstraction layers:

| Layer | Fundamental question | Scope |
|---|---|---|
| **Product** | What must the system accomplish? | Purpose, capabilities, observable behavior, and requirements |
| **System Design** | How is the system conceptually organized? | Domain concepts, business rules, responsibilities, boundaries, and logical architecture |
| **Technical Design** | How is the design concretely realized? | Technologies, interfaces, modules, data structures, and code-level design |

The layers progressively refine the same system.

**Product → System Design → Technical Design**

Each layer introduces detail without simply duplicating the layer above it.

For example:

- **Product:** Customers can cancel orders before shipment.
- **System Design:** The Order aggregate enforces cancellation eligibility according to its lifecycle.
- **Technical Design:** A `CancelOrderHandler` coordinates the operation using an `OrderRepository` backed by PostgreSQL.

These are three complementary representations of the same capability.

### Product
Describes the system from the perspective of its users, stakeholders, and externally observable outcomes.

It defines what the system must accomplish without prescribing its internal organization.

### System Design
Describes the conceptual solution.

It combines domain modeling with logical software architecture, including domain semantics, consistency boundaries, component responsibilities, and interactions.

This layer is strongly influenced by DDD and Clean Architecture.

### Technical Design
Describes the concrete technical realization of the system.

It includes implementation contracts, code structures, technologies, persistence representations, APIs, and other technical mechanisms.

The distinction between System Design and Technical Design is not simply whether information is technical.

**System Design defines the conceptual organization and constraints of the solution. Technical Design defines its concrete realization.**

---

### 2.2 Dimension Two: State
State describes the relationship between information and the system's current or intended existence.

USM defines three states:

| State | Question | Nature |
|---|---|---|
| **Reality** | What exists today? | Descriptive |
| **Commitment** | What has been accepted as the intended system? | Normative |
| **Exploration** | What alternatives are being considered? | Hypothetical |

### Reality
Reality describes the system as it is currently realized.

At the Product layer, this means available capabilities and observable behavior.

At the System Design layer, it means the domain models, responsibilities, and architectural structures embodied by the existing application.

At the Technical Design layer, it means the actual technical implementation.

Reality is a description supported by the available evidence, not a guarantee of perfect or complete knowledge. Evidence management and verification mechanisms are separate from the abstract information model.

### Commitment
Commitment represents accepted product requirements and design decisions.

It describes what the system is expected to satisfy, regardless of whether that design has already been realized.

Commitment is the authoritative source of intended behavior and structure within its abstraction layer.

A commitment does not imply implementation.

### Exploration
Exploration represents uncommitted alternatives, hypotheses, and possible future directions.

Exploratory information is not authoritative and must not be interpreted as an obligation.

Exploration is optional for individual concepts and should only be represented when meaningful alternatives exist.

### State is not a lifecycle
The three states are not mandatory sequential stages.

Although an idea may move from Exploration to Commitment and eventually become Reality, this is not always the case.

An implementation may exist without an explicit prior commitment. A commitment may be replaced without being implemented. An exploration may challenge an existing design.

USM represents these situations without assuming a fixed workflow.

---

## 3. The Unified Model
Combining the two dimensions produces the following conceptual matrix:

| Abstraction | Reality | Commitment | Exploration |
|---|---|---|---|
| **Product** | Available capabilities and current behavior | Accepted capabilities, requirements, and quality expectations | Potential features and alternative behaviors |
| **System Design** | Existing domain model and logical architecture | Intended domain model and system architecture | Alternative domain and architectural designs |
| **Technical Design** | Existing code and technical implementation | Accepted implementation design and technology choices | Alternative implementation approaches |

Every system concept belongs primarily to an abstraction layer and may have representations in all three states.

For example, an Order Cancellation capability may have:

**Product**

- Reality: Cancellation is available before payment.
- Commitment: Cancellation is available until shipment.
- Exploration: Cancellation remains available during a fulfillment grace period.

**System Design**

- Reality: Payment completion determines cancellation eligibility.
- Commitment: Order lifecycle rules determine eligibility.
- Exploration: Cancellation is modeled as a separate domain process.

**Technical Design**

- Reality: Controller-level cancellation logic with Firestore.
- Commitment: Application use-case handler with PostgreSQL persistence.
- Exploration: Alternative persistence and event-delivery implementations.

The model supports these differences without treating them as contradictory documentation.

**A concept may be fully realized at one abstraction level while remaining divergent from commitment at another.**

For instance, a product capability may already work as intended even though its committed technical architecture has not yet been implemented.

---

## 4. Modeling the Product Layer
### 4.1 Purpose
The Product layer defines the software system through its purpose, externally observable behavior, and expected outcomes.

It should be understandable without knowledge of the application's internal architecture.

### 4.2 Main Concepts
The Product layer may contain:

| Concept | Description |
|---|---|
| Purpose | Why the product exists |
| Actors | People or systems interacting with the product |
| Capabilities | What the system enables |
| Functional requirements | Observable behavior the system must provide |
| Use cases | How actors accomplish goals through the system |
| Quality requirements | Required levels of performance, reliability, security, accessibility, and other externally meaningful qualities |
| Product constraints | Restrictions and conditions affecting product behavior |

Functional and non-functional requirements primarily belong here because they define **what the system must satisfy**, rather than the mechanisms used to satisfy it.

### 4.3 Example: Order Cancellation
**Concept:** Order Cancellation  

**Layer:** Product  

**Purpose:** Allow customers to cancel unwanted orders.

### Reality
Customers can cancel orders only before payment is completed.

### Commitment
Customers can cancel orders until shipment begins.

Functional requirements:

- Customers can request cancellation of eligible orders.
- The system rejects cancellation of ineligible orders.
- Successful cancellations are confirmed to the customer.
- Paid cancellations initiate the applicable refund process.

Use case — Cancel Order:

1. A customer selects an order.
2. The customer requests cancellation.
3. The system evaluates eligibility.
4. The system either confirms cancellation or explains its rejection.

Quality requirements:

- Confirmed cancellations must not be silently lost.
- Cancellation operations must be auditable.
- At least 95% of cancellation requests should receive a response within 500 ms (illustrative target; an actual system must agree its own measurable objective).

### Exploration
Allow cancellations during a short grace period after fulfillment preparation begins.

### Boundary
The Product layer does not define aggregates, service decomposition, database schemas, or programming constructs.

These belong to lower abstraction levels.

---

## 5. Modeling the System Design Layer
### 5.1 Purpose
The System Design layer describes the domain and conceptual architecture that make product behavior possible.

It explains the system's internal meaning, responsibilities, structural boundaries, and interactions without prescribing every implementation detail.

This layer combines the concerns traditionally associated with domain modeling and high-level architecture.

### 5.2 Main Concepts
| Concept | Description |
|---|---|
| Bounded contexts | Semantic boundaries within which domain models are consistent |
| Ubiquitous language | Shared terminology and business definitions |
| Entities | Domain concepts defined by identity |
| Value objects | Domain concepts defined by value |
| Aggregates | Consistency boundaries protecting domain invariants |
| Business rules | Conditions governing valid domain behavior |
| Domain events | Meaningful occurrences in the business domain |
| Application use cases | Responsibilities that coordinate domain operations |
| Components and services | Logical units with defined responsibilities |
| Ports and adapters | Boundaries separating core responsibilities from external mechanisms |
| Interactions | Logical communication and coordination between components |
| Architectural constraints | Required dependency directions, consistency guarantees, and structural properties |

The System Design layer should distinguish domain concepts from software constructs while allowing them to coexist in one coherent conceptual design.

### 5.3 Example: Order Cancellation Design
**Concept:** Order Cancellation  

**Layer:** System Design

### Reality
The existing Order model permits cancellation only before payment.

Cancellation eligibility is determined by payment status.

The Ordering component performs cancellation validation within the request-handling flow.

### Commitment
The Ordering bounded context owns the Order lifecycle.

The Order aggregate enforces cancellation eligibility.

Domain invariants:

- A shipped order cannot be cancelled.
- A cancelled order cannot subsequently be shipped.
- Cancellation must not produce duplicate successful state transitions.
- Cancellation of a refundable paid order creates the appropriate refund obligation.

Application responsibilities:

- Receive cancellation requests.
- Load the Order.
- Invoke domain cancellation behavior.
- Persist the updated state.
- Coordinate any required external effects.

Architectural constraints:

- Domain behavior must remain independent of infrastructure.
- Persistence must be accessed through an explicit abstraction.
- Cancellation and refund coordination must tolerate partial failures.

### Exploration
Evaluate whether cancellation should become a separate domain process responsible for coordinating Ordering, Payment, and Fulfillment.

### Boundary
The System Design layer defines responsibilities, domain semantics, and architectural constraints.

It does not prescribe concrete SQL schemas, specific framework classes, or programming-language syntax.

---

## 6. Modeling the Technical Design Layer
### 6.1 Purpose
The Technical Design layer defines how System Design concepts are realized using concrete technologies and software constructs.

It connects the conceptual system to the actual codebase.

### 6.2 Main Concepts
| Concept | Description |
|---|---|
| Modules and packages | Concrete source-code organization |
| Interfaces and classes | Programming contracts and implementations |
| Functions and algorithms | Concrete behavior and computational mechanisms |
| API contracts | Endpoints, payloads, schemas, and protocols |
| Persistence implementations | Repositories, database technologies, queries, and mappings |
| Data schemas | Tables, collections, indexes, and storage structures |
| Integrations | SDKs, clients, transport adapters, and messaging implementations |
| Frameworks and libraries | Selected implementation technologies |
| Runtime topology | Concrete deployment and execution configuration |
| Technical constraints | Implementation-level guarantees and restrictions |

Not every function or class needs its own documented concept.

The model should capture technical knowledge necessary to understand and safely evolve the system without reproducing the entire source code.

### 6.3 Example: Order Repository
**Concept:** Order Repository  

**Layer:** Technical Design  

**Related System Design concept:** Ordering Persistence

### Reality
The existing application persists Orders using Firestore.

The implementation uses `FirestoreOrderRepository`, stores each Order as a document, and uses Firestore transactions for updates.

### Commitment
Order persistence will use PostgreSQL.

The accepted implementation design includes:

- An `OrderRepository` interface used by the application layer.
- A `PostgresOrderRepository` adapter.
- Relational persistence for Orders and OrderLines.
- Explicit mapping between persistence records and domain objects.
- Transactional updates preserving aggregate consistency.

Example interface:

```typescript

interface OrderRepository {

  findById(id: OrderId): Promise<Order | null>;

  save(order: Order): Promise<void>;

}

```

### Exploration
Evaluate:

- PostgreSQL with direct SQL.
- PostgreSQL with an ORM.
- Retaining Firestore with an improved data model.

These alternatives remain uncommitted.

### Boundary
The Technical Design layer describes the implementation, not the underlying product rationale or the full business semantics of the Order concept.

Those remain defined in the Product and System Design layers.

---

## 7. Concept-Oriented Documentation
USM organizes information around **system concepts**, rather than around states or traditional document types.

A concept is an identifiable element of the system, including a meaningful grouping of other concepts, worth understanding independently.

USM distinguishes four related terms:

- **Concept:** An identifiable element or meaningful collection within the modeled system.
- **Subject:** The particular concept, relationship, or bounded collection addressed by a page.
- **Page:** A documentation representation of a subject, with relevant Reality, Commitment, and optional Exploration views.
- **Index:** An orientation and navigation page for a system, abstraction layer, or collection of subjects; it is not a competing authority for detailed concepts.

Examples include:

- Order Cancellation — Product
- Order Aggregate — System Design
- Ordering Service — System Design
- Order Repository — Technical Design
- Order REST API — Technical Design

Each concept has a primary abstraction layer and may contain Reality, Commitment, and Exploration.

### 7.1 Concept Page Structure
A typical concept page contains:

### Identity and context
- Concept name
- Abstraction layer
- Purpose and scope
- Related concepts

### Reality
The currently realized representation of the concept.

### Commitment
The accepted intended representation.

### Exploration (optional)
Alternative approaches, hypotheses, or unresolved choices.

### Relationships
Connections to related concepts at the same or other abstraction levels.

This layout allows readers to compare current and intended representations without navigating between competing documents.

### 7.2 Avoid Unnecessary Duplication
USM does not require all three states to contain complete descriptions.

When Reality and Commitment agree, that alignment may be expressed without duplicating every detail.

However, the absence of a Reality description must not be interpreted as proof that the committed design is implemented.

Unknown, aligned, divergent, and unrealized are distinct conditions.

The representation must preserve that distinction. Avoid claiming alignment by omission: if a page summarizes a shared design without repeating it under Reality, it should explicitly indicate whether realization has been verified or remains unknown.

### 7.3 Concepts and Their Granularity
Not every rule, function, or implementation detail needs an independent page.

A concept deserves independent treatment when it has meaningful responsibilities, relationships, design decisions, or a lifecycle that readers need to understand.

Smaller concepts may be described within a larger conceptual page.

USM defines the information model, not a mandatory documentation granularity.

---

## 8. Relationships Between Concepts
Concepts must not exist as isolated descriptions.

USM relies on explicit relationships connecting the system across abstraction levels and states.

### 8.1 Cross-Layer Relationships
Higher-level concepts are progressively refined by lower-level concepts.

For example:

**Product: Order Cancellation**

Defines the externally observable capability.

↓ Refined by

**System Design: Order Cancellation Process**

Defines the domain rules and collaborating responsibilities.

↓ Realized by

**Technical Design: CancelOrderHandler**

Defines the concrete implementation mechanism.

These relationships enable both top-down and bottom-up navigation.

A reader can ask:

- How is this product capability implemented?
- Which domain rules govern this operation?
- Which product behaviors depend on this technical component?
- What happens if this interface changes?

Relationships may be many-to-many.

One capability may depend on several bounded contexts, and one technical component may support multiple capabilities.

### 8.2 State Relationships
Within a concept, Reality and Commitment may have different relationships:

- **Aligned:** Available evidence establishes that Reality satisfies Commitment; wording and implementation details need not be identical.
- **Divergent:** Available evidence establishes that Reality does not fully satisfy Commitment, including partial realization.
- **Unrealized:** A specific case of divergence: available evidence establishes that the committed capability or structure does not yet exist.
- **Unknown:** Available information is insufficient to determine alignment. Missing documentation alone is not proof of non-implementation.

Exploration represents alternatives and may challenge or extend committed designs without changing their authority.

These relationships should be explicit when relevant.

### 8.3 Change Propagation
Changes at one abstraction level may affect other levels. A higher-level commitment change triggers review of affected lower-level commitments; it does not silently revise or approve them.

For example:

1. Product Commitment changes to allow cancellation after payment.
2. System Design Commitment must support the revised business rules.
3. Technical Design Commitment must provide an implementation consistent with the updated design.
4. Reality changes only when the corresponding system behavior or structure is actually realized.

A commitment change does not automatically update Reality.

Likewise, an implementation change does not automatically redefine accepted Product behavior.

USM separates these changes so their implications remain visible.

---

## 9. Authority and Consistency
USM must provide unambiguous information to both humans and AI agents.

### 9.1 Authority Depends on the Question
For questions about what should exist, Commitment is authoritative.

For questions about what currently exists, Reality is the relevant representation.

For questions about alternatives, Exploration provides possibilities but no obligations.

The abstraction layer determines the scope of that authority.

Examples:

- Intended feature behavior → Product Commitment
- Accepted domain invariant → System Design Commitment
- Intended repository implementation → Technical Design Commitment
- Existing repository implementation → Technical Design Reality

**Vertical consistency:** Commitments across abstraction layers must be mutually compatible. System Design Commitment must honor the applicable Product Commitment; Technical Design Commitment must honor the applicable System Design and Product commitments. When a conflict is discovered, it is a design inconsistency to resolve, not an accepted Reality–Commitment gap.

**Horizontal alignment:** Within each layer, Reality may legitimately differ from Commitment. Such divergence should be visible, but it does not by itself invalidate the intended design.

### 9.2 Reality and Commitment May Contradict Each Other
Such differences are expected.

A system can legitimately have a current implementation that does not match its accepted target design.

The difference may represent an incomplete migration, historical behavior, technical debt, or a planned capability.

USM must not silently resolve these differences by choosing one state over another.

### 9.3 Exploration Must Remain Non-Authoritative
An exploratory alternative must not be interpreted as an accepted requirement or design.

Only explicitly committed information defines intended behavior.

### 9.4 Source Code as Evidence
The actual codebase, tests, schemas, and runtime configuration provide important evidence for technical Reality.

However, source code alone does not necessarily explain the intended design.

USM therefore distinguishes the **representation of existing technical reality** from the **evidence used to establish it**.

The mechanisms for validating documentation against code can be defined separately from the information model.

---

## 10. Single Entrypoint and Progressive Discovery
Every USM system should provide a single conceptual entrypoint.

Its purpose is to orient readers and provide navigation, not to reproduce the entire model.

The entrypoint should answer:

- What is this system?
- What is its purpose?
- What are its principal capabilities?
- What is currently realized?
- What is the committed direction?
- What are the major domain and architectural boundaries?
- Where can more detailed information be found?

From this entrypoint, readers should be able to navigate progressively through the abstraction layers.

For example:

**System Overview → Order Management → Order Cancellation → Ordering Domain Model → Cancellation Application Use Case → Concrete Implementation**

Readers should not need to load or understand the entire system to work safely within one bounded context.

The same navigation model should support a small single-repository application or a distributed system containing many services and repositories.

USM does not require every service to be an independent documentation universe.

Bounded contexts, services, and repositories are different types of concepts and boundaries. Their relationships should be explicit rather than assumed to be equivalent.

---

## 11. Organizing USM Documentation
USM defines an information model independently of how it is stored. However, a consistent documentation structure makes the model easier to navigate, maintain, and understand for both humans and AI agents.

The recommended starting point is a simple directory hierarchy organized by the three abstraction layers, with a single entrypoint at the root.

### 11.1 Recommended Base Structure
```text

docs/

├── README.md

├── product/

│   └── README.md

├── system-design/

│   └── README.md

└── technical-design/

    └── README.md

```

Each element has a clear responsibility:

- **Root README:** Introduces the system, summarizes its current reality and committed direction, and provides navigation to the three layers.
- **Product README:** Introduces the product model, its principal actors, capabilities, and observable behavior.
- **System Design README:** Introduces the domain landscape, logical architecture, and principal system responsibilities.
- **Technical Design README:** Introduces the concrete technical landscape, codebase organization, technologies, and major implementation boundaries.

These four entrypoints provide a predictable navigation structure, regardless of system size.

The root entrypoint should be concise. It should not attempt to reproduce information maintained in the individual layers.

### 11.2 Organize by Subject, Not Document Type
Below the layer entrypoints, detailed subject pages should be organized around what they describe. Index pages are a separate, navigation-oriented page purpose: they introduce a scope and link to authoritative subject pages without duplicating their detailed claims.

A subject may be:

- An individual concept, such as an Order aggregate.
- A capability, such as Order Cancellation.
- A subsystem, such as Ordering.
- A collection of related concepts, such as the Ordering domain model.
- A system-wide concern, such as authorization or observability.
- A relationship between concepts, such as the interaction between Ordering and Payments.

These subjects can have different levels of granularity.

**The organizing principle is that each page has a clear subject and a defined scope, not that every page describes an atomic concept.**

For example, a System Design page named `domain-model.md` might describe several entities and their relationships through an entity diagram. The subject of the page is the domain model as a whole.

A separate `order.md` page might describe an individual aggregate in greater detail.

Both are valid USM pages.

### 11.3 Example: Small Application
For a small application, a relatively flat structure is sufficient.

```text

docs/

├── README.md

│

├── product/

│   ├── README.md

│   ├── authentication.md

│   ├── order-management.md

│   └── order-cancellation.md

│

├── system-design/

│   ├── README.md

│   ├── domain-model.md

│   ├── order-lifecycle.md

│   ├── components.md

│   └── authorization.md

│

└── technical-design/

    ├── README.md

    ├── project-structure.md

    ├── database.md

    ├── order-api.md

    └── authentication.md

```

The Product layer describes capabilities and externally observable behavior.

The System Design layer describes the overall domain model, important domain processes, and logical components.

The Technical Design layer describes their concrete realization.

For example, `system-design/domain-model.md` may contain an entity relationship diagram covering the whole application, while `technical-design/database.md` describes its concrete persistence schema.

The two diagrams serve different purposes:

- The domain diagram explains business concepts and relationships.
- The database diagram explains technical storage structures.

There is no requirement to create a separate file for each entity or table.

### 11.4 Example: Medium-Sized Application
As the system grows, its documentation can be grouped by capabilities, bounded contexts, or technical subsystems.

```text

docs/

├── README.md

│

├── product/

│   ├── README.md

│   ├── identity/

│   │   ├── README.md

│   │   └── account-management.md

│   ├── ordering/

│   │   ├── README.md

│   │   ├── order-placement.md

│   │   └── order-cancellation.md

│   └── payments/

│       ├── README.md

│       └── payment-processing.md

│

├── system-design/

│   ├── README.md

│   ├── system-context.md

│   ├── context-map.md

│   ├── ordering/

│   │   ├── README.md

│   │   ├── domain-model.md

│   │   ├── order-lifecycle.md

│   │   └── cancellation-process.md

│   ├── payments/

│   │   ├── README.md

│   │   ├── domain-model.md

│   │   └── refund-process.md

│   └── cross-context/

│       └── ordering-payments.md

│

└── technical-design/

    ├── README.md

    ├── repository-structure.md

    ├── ordering/

    │   ├── README.md

    │   ├── order-api.md

    │   ├── order-repository.md

    │   └── persistence-schema.md

    ├── payments/

    │   ├── README.md

    │   └── payment-provider.md

    └── infrastructure/

        ├── deployment.md

        └── event-delivery.md

```

The structure follows the natural decomposition of each abstraction layer.

The Product layer may group documentation by capabilities or functional areas.

The System Design layer may group documentation by bounded contexts and logical responsibilities.

The Technical Design layer may group documentation by implementation modules, services, or repositories.

**The three layers do not need identical internal directory structures.**

A bounded context does not necessarily correspond to one service, and a service does not necessarily correspond to one repository.

USM relationships connect these representations without requiring their physical organization to mirror one another.

### 11.5 Pages Can Represent Different Scales
USM supports several levels of documentation granularity.

| Scope | Example page | Layer |
|---|---|---|
| Whole system | System capabilities overview | Product |
| Functional area | Order Management | Product |
| Individual capability | Order Cancellation | Product |
| Whole domain | Domain landscape and context map | System Design |
| Bounded context | Ordering domain model | System Design |
| Individual domain concept | Order aggregate | System Design |
| Cross-context interaction | Ordering–Payments integration | System Design |
| Whole codebase | Repository structure | Technical Design |
| Technical subsystem | Persistence design | Technical Design |
| Concrete component | PostgreSQL Order Repository | Technical Design |

A page may contain several diagrams, rules, descriptions, and related concepts as long as they collectively describe a coherent subject.

The decision to split a page should be driven by comprehension, maintainability, and navigation, not by an arbitrary requirement that each concept must have its own file.

### 11.6 Applying States Within Pages
The three USM states remain applicable regardless of a page's granularity.

For an individual concept, such as an Order aggregate, Reality and Commitment may describe different lifecycle rules.

For a broader concept, such as an entire domain model, Reality and Commitment may contain different entity diagrams.

Example:

```text

# Ordering Domain Model

## Overview

Purpose and scope of the Ordering domain.

## Reality

Current domain concepts and relationships.

Current entity diagram.

## Commitment

Accepted domain concepts and relationships.

Target entity diagram.

## Exploration

Optional alternative domain boundaries.

## Related Concepts

Order Lifecycle

Cancellation Process

Payments Context

```

Reality and Commitment should be independently understandable when their differences are significant.

When both states are aligned, unnecessary duplication should be avoided. The page should nevertheless state whether the alignment is verified or unknown; shared wording alone is not proof of realization.

Exploration remains optional.

### 11.7 Layer Indexes
Every layer should have its own introduction and index.

A layer's `README.md` should provide:

1. **Scope:** What the layer represents and what belongs within it.
2. **Overview:** A concise explanation of the relevant part of the system.
3. **Reality summary:** The most important characteristics of the currently realized system at that abstraction level.
4. **Commitment summary:** The principal intended direction and accepted design.
5. **Navigation:** Links to major subjects and their detailed documentation.

The index is not a separate source of authority for detailed concepts.

It summarizes and points toward the pages that own those details.

### 11.8 Keep the Structure Proportional to the System
USM should not impose unnecessary documentation complexity.

A small application may need only a handful of pages per layer.

A larger system may require nested directories for bounded contexts, capabilities, services, or subsystems.

Directory depth should grow only when it makes discovery easier.

Avoid creating empty folders, placeholder documents, or one-file-per-concept structures solely to satisfy a predefined hierarchy.

The goal is a model that remains easy to navigate, not a perfectly symmetrical directory tree.

### 11.9 Recommended Organizational Principles
- Start with one root entrypoint and one directory per abstraction layer.
- Give each layer its own introduction and index.
- Organize detailed documentation around clearly scoped subjects.
- Allow subjects to represent individual concepts, groups of concepts, or entire subsystems.
- Keep Reality and Commitment together in the same conceptual page when practical.
- Include Exploration only when meaningful alternatives exist.
- Use diagrams where they communicate relationships more effectively than prose.
- Let each layer evolve its internal organization independently.
- Connect related subjects across layers through explicit links.
- Prefer a small navigable structure over a large rigid hierarchy.

**The directory structure is a navigation mechanism. The information model is the source of meaning.**

---

## 12. What USM Does Not Define
USM is an **information model**, not a development process or a filesystem convention.

It does not prescribe:

- Markdown folder structures.
- Specific documentation file names.
- Issue-tracking practices.
- Sprint planning or delivery workflows.
- Ticket or specification templates.
- Mandatory document-per-concept granularity.
- Particular implementation frameworks.
- A specific AI agent workflow.

These can be built on top of USM without changing its conceptual foundations.

---

## 13. Summary
The Unified System Model describes one software system through two independent dimensions.

**Abstraction**

1. Product — What the system must accomplish.
2. System Design — How the system is conceptually organized.
3. Technical Design — How the design is concretely realized.

**State**

1. Reality — What currently exists.
2. Commitment — What has been accepted.
3. Exploration — What may be possible.

Information is organized around concepts, with explicit relationships across abstraction levels and states.

Reality and Commitment are presented together so differences remain discoverable. Exploration is included when relevant without being confused with accepted intent.

**The central principle of USM is simple:**

> One system. Multiple abstraction levels. Explicit states. Connected concepts. No ambiguity between what exists and what is intended.

USM provides the conceptual foundation for building software documentation that is useful to humans and AI agents, supports domain-driven and specification-driven development, and remains coherent as software systems evolve.
