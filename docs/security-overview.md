# Alkemio Security Design

##	Main design principles
The main principles around the CT security design are:
* HTTPS for transport security on all traffic on public networks
* Minimal network surface exposure – all traffic goes through the Kubernetes Ingress controller
* Authorization is token-based via Identity Provider (at the moment Azure Active Directory)
* Routing to the Identity Provider endpoints is done via standard 3rd party libraries
    * For Alkemio Client.Web – Ory Kratos Client
    * For Alkemio Server – Passport.js
* Database is secured via user:password. The database endpoint is accessible only inside the Kubernetes network.
* Accessing 3rd party APIs requires obtaining API specific access token.

##	Authentication
As covered in the [Technical Design](./technical-design.md), Alkemio uses the concept of Authentication Providers to externalise the responsibility for Authentication. 


##	Authorization
Authorization within Alkemio happens at the level of the GraphQL api i.e. at the moment a client interacts with the data. It is applied for all queries and all mutations, and down to the lowest level of granularity (e.g. being able to access a particular field).

The Authorization Framework for the platform operates as follows:
* **Privileges**: For all actions that a User carries out (read, create, update etc) there is a particular _Privilege_ required. 
* **Credentials**: A User accessing the platform has a set of associated _Credentials_ that are held by the Agent acting on behalf of the User 
* **Authorization Policy**: The _Authorization Policy_ of the entity being accessed / operated on (e.g. Subspace, Community etc) determines which Privileges are granted to which Credentials.

There is an _Authorization Engine_ that determines if a particular action is allowed or not, given the required Privilege, the Credentials held by the User and the Authorization Policy of the Entity being operated on.

For full details please refer to the in-depth guide to [Credential Based Authorization](./credential-based-authorization.md).



