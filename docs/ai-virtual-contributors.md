# AI + Virtual Contributors 
This document provides a conceptual explanation of how AI is leveraged on the Alkemio Platform, including for the creation of Virtual Contributors (TM).

## Virtual Contributors

The high level conceptual model for usage of AI to power Virtual Contribuotrs on Alkemio is shown below. 
<p align="center">
<img src="images/ai-virtual-contributors-conceptual-model.png" alt="AI Virtual Contributors Conceptual Model" width="600" />
</p>

A **Virtual Contributor** allows usage of an AI service as a contributor on Alkemio. 

An **AI Service** provides access to an AI powered capability e.g. Legal expert, community manager etc. 

Logically the AI service should be thought of as being outside of Alkemio. However Alkemio also does provide the ability to easily create and work with AI services that are specific to the type of data or roles on Alkemi. 

## Benefits of Virtual Contributors 
* Provide transparency on the remote AI service
    * What capability is it providing?
    * What type of LLM is behind it?
    * What organization is providing it?
    * What knowledge does it have?
    * What type of interaction modes, as well as agency, can it have?
    * What awareness does it need to have when used?
* Allow for reputation and attestions on the VC
* Allow for many-many interactions with VCs
    This is in sharp contrast to the typical one to one interaction that happens with an AI service (e.g. ChatGPT)

Essentially a Virtual Contributor can build up a reputation. 

The underlying model to keep in mind is that of a normal user. What knowledge does that person have? What can they do? 

To support this mental model, many aspects of a VC **cannot** be modified after the VC is made available.

## **Creating a Virtual Contributor (VC)**
To create a VC, access needs to be provided to an AI service.

To make getting started with Virtual Contributors straightforward, Alkemio also provides the ability to easily create AI services.

Each VC is created within an Account on Alkemio. Each Account on Alkemio has either a Host user or organization. The Host User or Organization is then the **provider** of the VC.


## **Alkemio AI Services**
The following are AI services provided by Alkemio.

### Space Expert
This AI service allows you to interact with the contents of a Space, including files that are uploaded as links. In essence it is allowing the easy creation of an AI expert on a body of knowledge you control.

The Space Expert is created by specifying a particular Space on Alkemio as the source body of knowledge. It hopefully goes without saying that the user creating the Space Expert needs to have the rights / permissions to be able to share the contents of the specified Space.  

This allows a) creation of a new body of knowledge b) treating communities and their interactions as a body of knowledge. 

The latter makes a powerful route to allowing the collective wisdom of a community to be shared easily and safely with other communities. 

### Alkemio Help

## Using a VC
Once a VC is created, the next step is to use that VC as a contributor in one or more communities.

A VC has the following visibility modes:
* **Private**: whereby it is only usable with the same account where it is hosted
* **Public**: all other Spaces on Alkemio can invite the VC to join their community.

A VC has a lifecycle associated with it, going through the following phases:
* **Draft**: a VC in this state is being defined and populated. It can only have Private visibility when in this state.
* **Active**: The VC has being published, and core information about the VC can no longer be edited. Later there may be a submission / approval flow for turning a VC active on the Platform. 
* **Inactive**: The provider of a VC can at any point decide to turn a VC Inactive, which then results in the Store listing being disabled, no new interactions with VC being possible and its visibility mode is turned to Private. 



In addition a VC can also optionally choose, when Public, to be listed in the Alkemio Store. This makes it easier for other Spaces on Alkemio to find and invite the VC.

If a VC is not listed in the Alkemio Store, then the admin who wishes to invite the VC will need to have the full URL to the profile of the VC.

## Roadmap
There are many dimensions to expand the VC capability on Alkemio. 



