# Tagset Templates + Classification Tagsets
This technical note covers
* The usage of TagsetTemplates in Alkemio 
* Extending Tagsets to also allow for their usage to classify content by selecting from a set of allowed values

## Roadmap drivers
The ability to filter content on the platform based on the value selected in a Tagset is useful in the following contexts:
* Specifying the display location for a Callout in a Space
* Specifying the display location for a Callout in a Subspace
* Specifying the state from an Innovation Flow to show the Callouts for
* Classifying content based on a nomenclature e.g. SDKs

## Tagset usage
This section describes the usage of Tagsets in the platform.

### Tagset Types
The Tagset entity supports the following modes:
* **Freeform**: allowing any set of tags to be specified
* **Single select**: choose a single value from a set of allowed tag values
* **Multiple select**: choose multiple values from a set of allowed values

As such the Tagset entity knows its type, the set of tags associated with it, the TagsetTemplate to use if the set of allowed values is contrained. 

### Tagset Templates
TagsetTemplates provide two key capabilities:
* **Shared set of allowedValues**: to provide a single shared definition that can be shared amongst multiple Tagsets of type single / multiple select. 
* **Knowing what Tagsets should be created in a given context**: for example the Tagsets to be created on Callouts in a Space are different to those on a Subspace. 

The TagsetTemplate entity is held in a containing TagsetTemplateSet entity. This is both to make it easier to use in multiple places in the domain model, and also avoids ORM linkages in the code base (technical).

The Collaboration entity uses a TagsetTemplateSet to hold the TagsetTemplates to be applied to all Callouts in that Collaboration. The following TagsetTemplates are used per Journey type:
* Space: 
    * Default: type = freeform, for allowing any tags to be added
    * DisplayLocation: type = singleSelect, for specifying the palcement of the Callout within the display of the Space
* Subspace: 
    * Default: type = freeform, for allowing any tags to be added    
    * DisplayLocation: type = singleSelect, for specifying the placement of the Callout within the display of the Space
    * States: type = singleSelect for the set of visible states from the InnovationFlow

Then each time a Callout is added to the Collaboration, its profile will have the above Tagsets created as well as the Default tagset.  

### **Mutations**
The Tagset entity is authorizable, so we can consider also having specific Privileges being required for updating particular Tagsets. To be explored more later. 

## **Design notes**

**Encapsulation**

Tagsets added to an entity to be specified by the parent entity via passing in the applicable list of TagsetTemplates. 
For example for Callouts that should be the Collaboration
This does mean that the Collaboration entity needs to know what "Tagset Templates" to apply to each newly created Callout, which it then manages via its TagsetTemplateSet.

**Updating of TagsetTemplates**

It is expected that the set of values in a single / multiple select Tagset do not change frequently. 

For example the set of displayLocation values is fairly fixed for the Space and Subspace. 
When the set of allowedValues does need to change, for example, if the InnovationFlow lifecycle being used changes, then the business logic will need to ensure that the relevant TagsetTemplate is updated - plus ensuring that the selected value is one of the values in the set of allowedValues. 

