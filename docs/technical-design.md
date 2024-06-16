# Alkemio - Technical Design Introduction
This document provides an introduction to the technical design for Alkemio. 

It is assumed that the reader has already read the [Conceptual Design](./conceptual-design.md) document. 

The architecture is described along the following posts:
*   Design Principles
*   Logical Data Model
*   Logical Design, and core components
*   Security, Identities & Authorization
*   Identifiers
*   Templates
*   Lifecycles & states

In some cases this document will also provide a rationale for decisions that have been made - and of course the initial reference implementation of Alkemio also requires choices to be made regarding actual languages used, hosting, user management stacks etc. 


# Design Principles

The following high level choices guide the technical design:

*   **Build for a decentralized future, but expose in familiar ways**
    *   E.g. web 3 under the hood, but exposed using web 2.3 for the user
    *   Leverage latest proven technical solutions (e.g. SSI, Smart Contracts), but shield the end user from the technical complexity until ready
    *   This implies creating the platform with digital identities (SSI) for all entities in the system with associated wallets etc, but that this is all kept internal to the platform in first versions.
*   **Build for massive scale, with flexibility to evolve**
    *   Ensure that the core components chosen and how they are used needs to be chosen with massive scale in mind
    *   In practice this means efforts to minimise / isolate deployment dependencies
    *   The initiative is still learning and evolving, so it is deemed prudent to avoid establishing long term dependencies that may not be the right choice.
*   **Regulated value (e.g. money) out of band i.e. via familiar channels / units**
    *   In case the platform should enable the transfer of regulated value then a whole range of obligations fall on any organization deploying a Alkemio instance - e.g. KYC, AML, insurance etc.
    *   It is worth noting that the Host Organization for a Space could later play a role in this scenario as facilitator of the regulated value exchange e.g. hold money in escrow for stakeholders + then pay parties based on signals from the platform that a Project has completed successfully
*   **Formalise Trust**
    *   This implies making explicit how agreements are made between actors of the platform, and how the representation of those interactions can be trusted. 
    *   The shared context gives a base of understanding, defining the change to be achieved. For Users or Organizations joining a Subspace they can then as part of the application process confirm that they agree to any particular terms / agreements that are used by the parties collaborating around that Subspace. 
*   **Simple to operate**
    *   The platform should be easy to configure, deploy and run - also implying that the number of external dependencies needs to be very carefully managed. 

It is worth noting that some of these choices, especially regarding having an Organization in the Space Host role for facilitating Subspaces, is made in the context of getting a first version of Alkemio deployable. The expectation is fully that these posts can be decentralised as the platform and wider context within which it is used matures - and indeed the architecture is set up to ensure that this is feasible in an incremental manner.

# Logical Data Model

The goal of the Alkemio platform is to allow the creation of **Safe Spaces**, to facilitate collaboration within the context of those Spaces. To support this, the platform has a *logical data model* for storing the shared representation and for facilitating the collaboration. 

The following diagram shows at a high level the key entities in use within Alkemio:

<p align="center">
<img src="images/design-logical-data-model.png" alt="Logical Data Model" width="600" />
</p>

This logical data model attempts to keep to a minimum, at least initially, the set of entities that are represented in the platform, while still being able to reflect the types described in the conceptual design. The rationale for this is to keep the approach as general as possible, allowing further innovation how Subspaces are managed. 

The key entities in the model are:
*   **Space:**: The combination of the change to be achieved with the community contributing to that change, and collaboration towards the change. 
*   **Community**:
    *   **User**: The primary way of interacting with the platform    
    *   **Organization**: To reflect legal entities that interact with the platform via one or more users.   
    *   **Virtual Contributor**: To allow safe interaction with AI services.   
*   **Context**: The change to be achieved.
*   **Collaboration**: 
    * **Callouts**: How to engage the members of a Community to get their wisdom on issues faced, solutions, others to engage, network etc.  
    * **Posts**: The posts to allow members to contribute
*   **Agents**: representing an entity in the platform in interactions with other entities. Entities with Agents include: Users, Organizations, Subspaces, Space etc. 
    * **Credentials**: a list of credentials held by the Agent. Important to note is that there are two types of credentials that can be associated with an Agent (a) simple credentials, which are managed by the platform (b) verified credentials, which are familiar W3C Verified Credentials. 
*   **Authorization Policy**: representing the authorization rules that grant privileges to agents that are interacting with the entity.  
    * **Privilege**: a list of potential Privileges such as Create, Read, Update, Delete, Grant that can be required in any particular interaction. 

In addition there are some key shared entities that are re-used across the domain model:
* **Profile**: a shared entity across to represent Profiles in a consistent way. It manages tagsets, references, visuals, tagline, description of the entity, providing a flexible way to associated meta-information for the entity. 
  * E.g. tagsets giving meta-data about the entity (e.g. industry, skill sets, interests) and references for links related to the entity (e.g. website of an organization, linkedin profile for a user etc)

There is also a *physical data model* that is how the logical data model is stored in the underlying datastore. However for using the system that should be hidden from normal usage.

# Logical Design

The logical layers in the Alkemio architecture are as follows:
*   **User Interaction**: the user experiences being provided to Users as they interact with the platform.
*   **Platform**: providing the set of services to clients that are interacting with the platform. 

The layering is shown in the following diagram:

<p align="center">
<img src="images/design-layers.png" alt="Design layers" width="600" />
</p>

Note that the platform itself is designed to be able to be used in a "headless" fashion, thus enabling many different types of clients / integrations.

## User Interaction

Users and Organizations interact in many different ways over the lifecycle of the Subspace. As such the primary goal of the Interaction Layer is to ensure that many different types of interactions are feasible, while of course also allowing easy adoption of the platform via one or more reference user interfaces.

Examples types of interactions:
*   Web client (default)
*   Mobile clients
*   Immersive game / VR experience
*   Integrations into other platforms / tooling

The default web client does allow interacting with the full domain model and administrating the platform. However this should be seen in some sense as a default client, with many other types of clients possible and encouraged.  

## Platform
There are multiple components working together in the Alkemio platform, with the Alkemio server being at the heart of all interactions.

<p align="center">
<img src="images/design-platform-components.png" alt="Platform Components" width="600" />
</p>

The core sub-components for the platform are as follows:
*   **Server**: for managing the entities in the domain model, and facilitating the usage of other services
*   **Authentication**: for managing the authentication of Users that are interacting with the platform
*   **Sql Storage**: for storing of databases, including the domain model from the Alkemio server, wallets from SSI, Communications and Identities. 
*   **Communications**: for managing all communications happening between entities (e.g. community to user, user to user) in the platform
*   **Self Sovereign Identities (SSI)**: for managing digital identities and wallets for Agents

All interactions with the Server are via the GraphQL api, which in turn relies on the Authentication services to ensure the acting Agent is identified.


# Security, Decentralization & Identity

This section describes how users interact with the platform in a secure manner. 

The key design goals driving the setup below are:
* **Security**: security is critical for a platform that is facilitating the collaboration between multiple stakeholders: the trust is needed for interactions.
* **Decentralization ready**: the interaction pattern has to be able to support operating in a decentralized setup. By decentralized in this case we focus on having entities such as Subspaces, Opportunities, Users, Organizations being able to interaction *without* being on the same server. The initial implementation does have all entities on the same server, but the interaction pattern has to support being decentralized. 
* **Extendable**: there are many ways that entities (e.g. users) can authenticate themselves, so the design needs to be flexible in terms of how entities are authenticated.  

## Actor Model & Agents
The target interaction pattern is the Actor Model, whereby entities are represented as independent actors that interact via the exchange of messages. This is a [well known approach](https://arxiv.org/vc/arxiv/papers/1008/1008.1459v8.pdf) that matches well to entities interacting in a decentralized manner. 

Core autonomous entities in the platform have an **Agent** that represents that entity in platform interactions. 

Entities with Agents currently are: User, Organization, Space, Subspace & Subspace. 

Each Agent has _Credentials_ which can interact on behalf of the user / entity with other elements of the platform. 

The usage of Agents is currently largely invisible to Users - but it is critical to setup the platform from the start with Agent based interaction patterns to enable moving more decentralized later.

Note: the text below primarily talks about Users for Authentication and Authorization, but the approach is also applicable for other entities with Agents e.g. Subspaces, Organizations etc. 

## Authentication Providers
The Authentication of users is handled by Authentication Providers, a pluggable mechanism whereby multiple types of Authentication can be supported by the platform. After successful Authentication, the platform retrieves the Agent for the User and that Agent is then used to carry out actions on behalf of the User. 

The platform currently supports [Ory Kratos and Ory Oathkeeper](https://ory.sh) for Authentication. The Ory platform is an open source, enterprise class Identity Provider. Note that the platform supports OIDC so that it is straightforward to integrate external authentications, e.g. GitSpace, Google etc, into the platform. 

<p align="center">
<img src="images/security-authentication-ory.png" alt="Security: Ory Authentication" width="600" />
</p>

This design is chosen as it essentially isolates the Authentication process from the rest of the platform, so that it can be later extended / adjusted if needed with additional Authentication Providers. In particular one clear goal is to allow users to their own SSI, a topic of [very active development](https://identity.foundation/did-siop/) in the SSI community. 

In essence the Server is protected by Oathkeeper, which then relies on Kratos for the management of the identities (including trusted external authentications) that are allowed to authenticate. This is represented by the Authentication Zone in the diagram - currently only the Alkemio Server works with Oathkeeper / Kratos but as the platform gets more decentralized other services will likely need to also leverage Oathkeeper / Kratos for authenticating incoming requests. 

## Authorization: Credentials
All Authorization within the Alkemio platform is based on Credential Based Authorization. This section will provide a high level overview of this approach. For additional details please [read this document](./credential-based-authorization.md).

<p align="center">
<img src="images/security-authorization-framework.png" alt="Credential Based Authorization" width="600" />
</p>

Essentially upon a User authenticating, the following happens:
* The platform retrieves the Agent that represents that User
* The Credentials held by that Agent are used to decide if the requested action from the User (e.g. Read a Subspace details, update their profile, update a Subspace definition etc) is Authorized. 

Worth noting that this approach also allows for Credentials to be later held outside of the platform e.g. the user holds their own wallet and manages their own credentials. The ideal scenario for Users is that ultimately they can have their own SSI that can then control the Agents managed by the platform - or replace the platform Agents. 

## Licensing: Credentials
All Licensing within the Alkemio platform is also based on Credentials. This section will provide a high level overview of this approach. For additional details please [read this document](./licensing.md).

<p align="center">
<img src="images/licensing-framework.png" alt="Licensing Framework" width="600" />
</p>

Essentially when a User wishes to carry out an action / access functionality that is Licensed, the following happens:
* The platform retrieves the Agent for the Account within whose scope the action is taking place
* The Credentials held by that Agent are used to decide if the requested action (e.g. Add another member, create a virtual contributor etc) is Licensed. 

# Templates
A key design goal for Alkemio is the sharing of best practices, so the platform needs to be customizable. This is achieved with Templates.

Templates support is high up the backlog for the platform, as it is important that key entities (e.g. Space, Subspace, Subspace, Project etc) can be instantiated based on a particular template. 

Key areas where we expect that templates support will be useful include:

* Posts: what are the key posts for a Subspace / Subspace that need to be captured within a given Subspace / Subspace?
* Community: what type of community do you intend to create? What User groups make sense?
* Whiteboards: what collaboration canvases should be available and what is the goal of each canvas?
* Innovation Flows: what is the lifecycle (also sometimes referred to as process) that your entities should follow? What do each of the states mean? What actions should be triggered e.g. if a Subspace is approved?
* (later) what are custom credentials / actions that can be carried out within a particular context?

The management of Templates needs to be both at the global level (global catalog), as well as locally controllable i.e. that a Space can provide a defined set of options (e.g. what Lifecycles can be used).

Currently there is only limited global templates support; if you have suggestions or wish to work with us on this post please engage!

# Identifiers + Names

## UUID
Every entity within the platform has a [Universally Unique Identifier (UUID)](https://en.wikipedia.org/wiki/Universally_unique_identifier). 

This is the primary means for identifying entities within the platform.

## NameID
In addition, the platform also uses a human readable identifier for certain entities. This is in addition to the UUID for the entity. 

The NameID is used for the following purposes:
* **Client URLs**: it is used to generate the URL that uniquely can navigate to that entity e.g. https://Space.alkem.io/space1/challenge1
* **Human readable**: making it easier to refer to particular entities
* **Automation**: it makes populating of data on the platform easier

Each NameID is unique within a certain scope. The following table shows which entities have a NameID and what is the uniqueness scope.

| Entity      | Uniqueness scope (namespace) |
| ----------- | ----------- |
| User      | Global amongst Users       |
| Organization   | Global amongst Organizations        |
| Space   | Global amongst Spaces        |
| Subspace   | Within containing Space         |
| Subspace   | Within containing Space         |

Notes:
* This implies that within a particular Space that the NameIDs for all Subspaces, Opportunities and Projects need to be unique. 
* The combination of (Space.NameID, Subspace.NameID) is sufficient to universally identify a particular Subspace using human friendly names. 

The following rules apply to the creation / usage of a NameID:
* 25 character limit
* allowed characters: a-z, A-Z, 0-9, -
* Must start with a letter (to create regex rule)
NameIDs logically **can** be updated (not initially), but with a warning re URLs being affected etc.


# Innovation Flows
As described in the Conceptual Design, the collaboration around a Subspace starts with a shared understanding ('Context'). A key element of that shared understanding is understanding where the Subspace (or other entity) is in terms of its maturity. 

The term used within the Alkemio platform for the managing the maturity of Subspaces (+ other entities), as well as to enforce processes / workflows, is **Innovation Flow**. 

State based representations are widely used, with Finite State Machines (FSMs) being a formalism whereby a system can be in exactly one from a defined set of states, with clearly defined criteria / rules for when a transition can take place. Examples include Kanban boards, RFCs as well as more formally business process modeling approaches. 

A state based representation is a natural match for collaboration on Subspaces – allowing clarity for the multiple parties interacting as it is always clear where the Entity is (exactly one state) as well as the allowed transitions with guards that determine how an Entity moves between the potential States. 

## State Machine
Alkemio uses the [XState](https://xstate.js.org) engine for managing the lifecycle of entities. The embedded XState engine is extremely capable and is widely used. It provides the core state representation syntax, logic and execution.

The XState engine is then wrapped by a *Lifecycle* entity that provides the integration with the Alkemio platform.

<p align="center">
<img src="images/design-lifecycle.png" alt="Lifecycles" width="600" />
</p>

Each Lifecycle is instantiated with a particular `machine definition` that defines the set of states plus associated logic for that entity. 

It can then accept incoming events, and upon receiving an event, it restores the state of the XState engine and passes the event to the engine with additional context. It also provides a set of hooks for commands and guards:
* The commands are capable of carrying out actions within the wider platform e.g. adding a user to a community. 
* The guards are capable of working with the Authorization Framework to see if a particular command is allowed. 

After executing the command, the Lifecycle then stores the machine state ready for the next incoming event. 

## Declarative Specification

Critically, the separation of the Lifecycle definition using XState allows for the definition of the states  to be declaratively specified. 


For example, consider the following lifecycle definition, used for a very simple Subspace:

```
{
  id: 'challenge-lifecycle-default',
  initial: 'new',
  states: {
    new: {
      on: {
        REFINE: 'beingRefined',
        ABANDONED: 'abandoned',
      },
    },
    beingRefined: {
      on: {
        ACTIVE: 'inProgress',
        ABANDONED: 'abandoned',
      },
    },
    inProgress: {
      on: {
        COMPLETED: 'complete',
        ABANDONED:'abandoned',
      },
    },
    complete: {
      on: {
        ARCHIVE: 'archived',
        ABANDONED: 'abandoned',
      },
    },
    abandoned: {
      on: {
        REOPEN: 'inProgress',
        ARCHIVE: 'archived',
      },
    },
    archived: {
      type: 'final',
    },
  },
}
```
This is visualized in the platform as follows:

<p align="center">
<img src="images/design-lifecycle-visual.png" alt="Lifecycle visualized" width="600" />
</p>

If you are curious you can also cut and paste the above definition into the [official XState Visualization service](https://xstate.js.org/viz/) and see their visualization of the same definition. 

## Usage
The platform currently uses Innovation Flows on the following entities:
* Subspaces
* Opportunities

The usage is currently fairly basic as the focus has being on ensuring that the concept is properly embedded within the platform and that it provides a solid foundation for future expansion (as per design principles at the start of this document).

However the expectation is to expand the usage significantly as soon as resources / demand allows:
* Custom Lifecycle definitions
* Attaching guards / commands to state transitions
* Expanding the set of Commands / guards e.g. to allow notifications
* ...

In addition, the underlying state machine representation ('Lifecycle') is also used on:
* Applications: to control the states of the application process
* OrganizationVerification: to control whether Organizations are verified or not
* Whiteboard Lock: to control whether the Whiteboard is checked out or not    

## SSI Demonstrator
There is already a demonstrator to illustrate the power of this approach, and how it can be integrated with SSI / VCs.

For this scenario the following was demonstrated:
* An Agent acting on behalf of a Subspace issued a VC for "LifecycleStateManagement" to a particular User
* The Agent acting on behalf of the User then stores that VC into its digital wallet (SSI)
* The logic for the Lifecycle for the Subspace was updated to include a new guard that was executed on all state transitions for a Subspace Lifecycle. 
* The new guard specified that only Agents with an assigned VC of the above type and tied to the particular Subspace were allowed to update the Subspace state.

# Communications
The Alkemio Platform supports communications in the following way:
* **Community-User**: whereby all members of a Community can see messages 

Later the platform will support the following interaction:
* **User-User**: whereby Users can directly communicate with each other 

The functionality is provided using an embedded [Matrix Protocol](https://matrix.org/) server. The choice for basing communications on top of Matrix Protocol is primarily due to it being designed from the ground up as a decentralized communications platform.

<p align="center">
<img src="images/communications-matrix.png" alt="Communications using Matrix Protocol" width="600" />
</p>

One of the core concepts in Matrix is the notion of a Room, which can have any number of participants. The concept of Rooms is directly exposed on the Alkemio api.

## Community-User Messaging
Each Community within Alkemio has two Rooms available:
* **Updates Room**: whereby a User who is authorized can send updates to a community
* **Discussion Room**: whereby members of the community can broadcast messages to the whole community

Note that there is a Community entity associated with each Space, Subspace and Subspace - ensuring that each of these entities can have Community based messaging.

## User-User Messaging
Each User can also have a private Room with any other User on the platform. 

This is then a secure private channel for their direct communications.

## Current Usage
The Matrix Protocol provides a rich set of functionality, only a small fraction of which is currently leveraged / exposed by Alkemio. However the choice was made to embed / wrap Matrix in order to ensure that there is a powerful base for further expanding the communications capabilities of the platform. 

The current setup implies that the embedded Matrix Server has rooms for each Community, as well as direct messaging rooms.

<p align="center">
<img src="images/communications-matrix-rooms.png" alt="Communications using Matrix Protocol" width="600" />
</p>

The Matrix Server is **not** directly exposed currently outside of the cluster, so it can only be accessed via the GraphQL api exposed by the Alkemio platform. 

## Future Usage
The Matrix  ecosystem offers multiple messaging clients and integrations, and it is clearly highly interesting to investigate how best to allow Users to integrate their communications from the Alkemio platform within other messaging clients. 

Further, the conceptual design of Matrix, with the notion of HomeServer and replicated synchronized copies of the communications data is also one that is worth exploring moving forward in terms of how the data held by the Alkemio platform is stored in a decentralized manner.


# Communities & User On-boarding
Each Space, Subspace and Subspace has a Community. This Community represents the set of Users and Organizations that are contributing.

Membership of a Community is curated, meaning that there is an explicit step required to become a member of a Community.

Membership is hierarchical, meaning that to be a member of a Subspace the User also needs to be a member of the containing Space.

Only registered Users can apply i.e. a User needs to have a Profile on the platform to be able to apply (logically enough!).

## User Membership
There are currently two ways for a User to become a member of a Community:
* Directly added by an administrator for the Community
* Apply to become a member

For the latter, the user can fill out a simple form to apply to become a member. The set of questions users are requested to fill out is currently fixed per level (i.e. the same set of questions is used for all Spaces, and for all Subspaces) - however once Templates are available these questions will be configurable per Community.

The high level flow for applications is shown below.

<p align="center">
<img src="images/community-applications.png" alt="Community Applications Flow" width="600" />
</p>

Once a User has applied to join a Community, the administrator for the Community will see a list of open applications on their administration page for that community. The Administrator uses the combination of the Users profile and responses to the application questions to grant / deny the application.

Finally worth highlighting that the Applications process also makes use of the Lifecycle capabilities of the platform highlighted earlier i.e. there is a Lifecycle defined with a set of states / commands etc that each Application has associated with it.

# Cluster
The Alkemio platform is deployed as a cluster. A high-level overview of the cluster is shown below.

<p align="center">
<img src="images/design-cluster.png" alt="Alkemio Cluster" width="600" />
</p>

Key is that there are two primary end points offered:
* Public Graphql api: for non-secured access to information about the platform. For example, meta-data, public configuration data.
* Private Graphql api: for authenticated access to the platform. This is the api used for all interactions with the domain model.
## Future plans
Applications are currently visible only via the Administration interface for a Community; ideally a range of notification options including email to all registered administrators would be added. 

Near term the platform can hopefully also support Invitations, so that a Community administrator can directly send out invitations to Users. 
