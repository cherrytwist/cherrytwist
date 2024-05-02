# Community Membership
The Alkemio platform is intended to make it much easier for contributors (users and organizations) to work together on shared goals. The set of contributors to a Space / Subspace is called a Community.

In this document the term "user" can be taken to refer to both organizations as well as users. The primary focus is on users, but most if not all of the requirements also directly apply to organizations as the platform allows organizations to join Spaces / Subspaces similar to how a user does.

## Members of a Community
A Community thus has members.

Being a member of a Community allows users to  carry out certain actions e.g. start an Aspect, join a Discussion etc. They are also able to turn on notifications for certain events in that Community e.g. receive Community Updates, get notified of new Discussions etc.

Each member of a Community will have their membership of that Community visible on their profile.

## Accepting Membership Obligations

Community membership also implies accepting and agreeing to be bound by the rules of that community. This is a key design for Alkemio, even if not currently enabled.

This means the the flow for joining a community **must** include the user explicitly accepting the rules of the Community being joined.

## Why do users join a community?
As part of designing the expanded version of community membership, the following are the key benefits we believe users would be after:
* To contribute to the goals of the community
* To be updated as to developments in that community
* To connect and interact with other users in that community
* To have their contributions visible, both in terms of profile and to make a statement
* To see what is happening within a private Space / Subspace
* To be inspired by what is happening

# Becoming a Community Member
The key question then arises of "how" a User joins a Community?

Currently there are three options:
* **Applications**: The User applies to join a Community, and their application is then approved / rejected by an admin of that Community
    * *Why not enough?* This works well for highly curated communities, but is too high a threshold for a situation where you want users to much easier engage with a Space.
* **Directly Join**: allowing users based on held credentials to directly join a community. So without an application + approve / reject flow.
    * Example 1: all Users associated with a particular organization
        * This is for example the case with UWV where they would like to grant all users with an email address (verified!) matching the UWV domain to be member of the UWV organization, and thus to be able to directly join the UWV Space (private)
    * Example 2: Members of another community
        * This would be the case whereby once someone is a member of a Space that they can then join Subspace communities hosted within that Space.
* **Direct addition**: The Community admin directly adds the User
    * This option **will** be removed, as the User does not get a Chance to accept the conditions for joining that Community. It will be replaced by Invitations (see below).


## Additional routes to Membership
The following additional routes to Community membership should later be supported:
* **Invitation**: Allowing Community admins to invite Users to join a Community. Again no approval / rejection would be needed.
    * In this scenario a Community admin could "invite" via email or a special link Users to join a Community. Similar to invitations in GitHub or a link in Discord.

## Leaving a Community
Users also need to be able to leave a community. This is currently only available to Community admins; it should also be exposed to Users.


## Administering Community Membership
The Community Admin should be able to control how membership of that Community happens.

For example:
* Allowing all Users from a particular Organization to be able to Join the Community
* Allowing members of a Space to join child Subspaces directly or that they should apply / be invited
* Not allowing any applications

# Design
The options available to a user regarding a community depend on the preferences of that community, the credentials held by that user that then ultimately get translated into a set of privileges.

## Privileges
The following privileges are used for Community Membership:
* **COMMUNITY_APPLY**: user is allowed to apply to join a Community
* **COMMUNITY_JOIN**: user is allowed to directly join a community

## Authorization Policy
It is the combination of the credentials held by the user togethr with the AUthorization Policy Rules that determine the privileges that are granted to a user. For more details please see the [credential based authorization design](https://github.com/alkem-io/alkemio/blob/develop/docs/credential-based-authorization.md).

The above design is very powerful, but also is not "familiar" to most users - so it is not exposed via the api. Instead the domain model maintains a number of "settings" to administrators that then get translated by the server into the authorization policy rules to be applied.

## Space Preferences
The Space Administrator is able to set preferences related to Community membership on the Space.

The following are the currently supported preferences for community membership:
* *Allow users to Apply for Membership*
    * Type: boolean, defaults to true
    * Privileges granted if true: grant the COMMUNITY_APPLY privilege to users with RegisteredUser credential. It is not inherited.
* *Allow users to directly Join*
    * Type: boolean, defaults to false
    * Privileges granted if true: grant the COMMUNITY_JOIN privilege to users with RegisteredUser credential. It is not inherited.
* *Allow members from the Host organization to directly Join*
    * Type: boolean, defaults to false
    * Privileges granted if true: Grant the COMMUNITY_JOIN privilege to all users that have the OrganizationMember credential for the Host organization(s). It is not inherited.
## Client Logic
There is a set of states / actions that the client needs to understand to be able to guide the user on the appropriate actions the user can take.

This is in particular visible via the button that is available for each Community. This button can have the following text displayed:
* Login to continue
* Member (disabled button)
* Application Pending (disabled button)
* Join
* Apply
* Join Parent
* Apply to parent
* Membership Not Available (disabled button)

The inputs to determine the text to display, as well as the action per text are:
* If the user is logged in
* If the user is a member of the community
* If the user has an application for membership pending
* The privilege held by the user on the Community (myCommunityPrivileges)
* If the Community has a parent community

If there is a parent community the following additional information is needed:
* If the user is a member of the parent community
* The privileges held by the user on the parent Community (myParentCommunityPrivileges)

### Space Logic
1. Authenticated = n ==> "Login to Continue"
2. isMember = y ==> "Member"
3. applicationPending = y ==> "Application pending
4. joinPrivilege ==> "Join"
5. applyPrivilege ==> "Apply"
6. "Membership not available"
The client currently has some fairly complex logic that determines when users can apply, what message is shown to them. That needs to be adapted / extended to deal with the above.

### Subspace Logic
1. Authenticated = n ==> "Login to Continue"
2. isMember =y ==> "Member"
3. applicationPending = y ==> "Application pending"
4. joinPrivilege ==> "Join"
5. applyPrivilege ==> "Apply"
6. parentCommunityMember = Y "==> "Membership not available"
7. parentCommunityJoinPrivilege ==> "Join parent" + Popup + inform user to first join the parent + advise user to then come back
8. parentCommunityApplyPrivilege ==> "Apply to parent" + Popup + apply on parent (existing)
9. "Membership not available"



