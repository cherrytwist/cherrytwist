# Storage and Document Management in Alkemio

This document is created to capture the design requirements and decisions made for Document Management in Alkemio.


## Requirements
The following are high priority desired functionalities related to document management on Alkemio:
* **Secure Access**: Allow uploading and having links to documents that are protected by Alkemio's authorization framework
* **Management**: Allow admins to see documents that have been uploaded in their Space, as well as meta information about thos documents
* **Secure**: Be able to track who uploaded a file and when, plus potentially additional meta information (e.g. upload IP)
* **Uploading rights per context**: Allow users to upload documents where they have relevant authorization privileges
* **Accessible via a URL**: the files need to be requestable via a http request whereby the response is the actual document
* **Quotas**: there should be limits on the amount of storage space used within a given context. This can then later be controlled by license rights. 
* **Single protocol**: it should be possible to disable the external ipfs protocol usage

The following non-functional requirements apply:
* **Performant**: The files access should be as fast as feasible given we are serving the content from our own servers. 
  
The following security requirements should be designed for:
* **Encryption when stored**: allowing the files to be encrypted when stored in the external storage.

### Out of Scope
An important consideration is that File **contents** are *not modified* after they are uploaded. This means that we do not consider in the work 
stream the need to be able to collaborate with other users on Files via Alkemio. Meta information about files, such as the title / description / tags can be modified. 


## Design
The core of the envisaged solution is a new concept called a `Storage Bucket` which contains `Documents`.

### Storage Bucket

A Storage Bucket has the following properties:
* It is authorizable, so that there can be a policy specified for who is allowed to upload Documents to it, who can manage the contents in it etc.
* It contains a set of Documents (more detail shortly)
* It specifies the mime types supported for storing in the Storage Bucket. 
* It specifies the maximum file size that can be uploaded
* It can optionally have a "parent" Storage Bucket, whose resources are then used by the child Storage Bucket

In addition there are then derived properties such as:
* Total Bucket taken up by the content in the StorageBucket
* Amount of Documents
* ...

The Storage Bucket is also then the place where there can be both quotas on the total amount of storage being used, as well as allowing control to Space admins 
for a given Storage Bucket to adjust:
* What file types can be stored 
* The maximum file size that can be uploaded

Note also each "community scope" will essentially need its own Storage Bucket. In particular, a Subspace will need to have its own StorageBucket as the Documents 
contained in it will be subject to different access rules than Documents in the containing Space. 


### Document
The second core concept is that of a Document. 

A Document has the following properties:
* ExternalID: the identifier for the Document in the actual storage system being used
* CreatedBy: The user that uploaded the Document
* CreatedOn: When it was uploaded
* DisplayName
* Description
* Tags (to allow filtering)
* Size
* Mime type (required so that requests know how to handle it)
* ...

Other properties that can be added to expand the capabilities of storage / security around storage include:
* Encrypted when stored in the extrenal storage or not
* Uploading source IP
* ...

Important to note is that the platform in essence adds a bunch of meta data around the underlying storage of the file. 

## Design
Each Profile has a StorageBucket. That is what is used for actually storing Documents. 

Each StorageBucket on a Profile then has a aggregating (parent) StorageBucket, which is where its storage usage is aggregated. 

This does mean that there is a separate hierarchy of relations between the StorageBuckets, but this was chosen as the most effective way to ensure storage usage could be aggregarded while still being able to store the Documents in a Storage Bucket tied to the actual entity that the content is for. 

## Aggregating Storage Buckets
The following entities in the Alkemio domain model are then used to act as aggregators for the StorageBuckets on Profiles:
* Space
* Subspace: essentially a child of the parent Space Storage Bucket.
* Platform
* Library
* Organization
* User

Currently there are no quotes applied to Storage Buckets, but as the documents are tracked with meta data this can be later enforced. In essence as we now have an audit trail of who uploaded each file we can also easily aggregate to see how much content each user is uploading if so desired.

There are limits on the size of files that can be uploaded as well as the types of files that can be uploaded.  

The rational for having a StorageBucket on each Profile is so that access to content, such as images in a Whiteboard, can be controlled using an AuthorizationPolicy that is derived from the authorization policy of the entity whose Profile is being used.

### Accessing the Files
The files will be accessed via Rest requests.

The Rest api for documents will then check the request matches the 
## Known issues
* Deletion of files: a side effect of content addressed storage is that if two users upload the same document then it will get the same externalID. 
  * This raises the potential issue of two users uploading the same Document, and one deletes it. If we immediately deleted the file on IPFS then the second user's document contents would no longer be accessible
  * For now we do *not* delete the file directly on IPFS if a Document is deleted, but would need to add in a check if another Document has the same externalID or not before removing it. Once we have ensured there is no direct access to 
  the file via IPFS (directly) then we can consider also removig the file on IPFS after a check. 
  * Note: if the file is encrypted when stored in IPFS then this will also impact this, meaning the risk would not go across StorageBuckets - but the real solution is to move to another storage mechanism than IPFS.
  
### Encryption at rest (later)
For files that are to be protected via the API, the contents of the files should:
* be encrypted at rest
* decrypted as part of returning the response to the http request

Key management for this encryption is a topic to be taken carefully.

So the Document itself stores the identifier in IPFS, but that would never be exposed via the API or otherwise. Then the access to the document goes through the REST API which retrieves the document from IPFS and serves it out. A follow on step would then be to have the document encrypted / decrypted by the containing Storage Bucket. 

### Storage of Documents
In the first version of Storage Buckets it is expected that the content will simply be stored on IPFS.

This has the following benefits:
* Allows the implementation to re-use existing proven uploading
* Allows to immediately benefit from having the newly uploaded files backed up (via existing ipfs backup mechanism)
* Allows the team to focus on understanding the full implications of the new setup.

It is expected that it is a fairly incremental task to move to storing documents that are uploaded in another storage location - but that is not critical for the first version.


## Client
Client will need to:
* be able to choose the right Storage Bucket to upload to.
* be able to check that the user has the privilege to upload before attempting to do so

Otherwise the existing functionality should be not that much impacted.

Plus to provide a management interface for working with the files in a given Storage Bucket.

Note that we can consider having a "virtual" folder structure displayed based on tags that are added to the Documents. 


### Future Work
The following future work streams have been identified: 
* Moving to another storage mechanism that IPFS
* Migrate Whiteboard storage to be via Documents
    * A potential middle ground here is to have the images in a Whiteboard stored as Documents, and then the JSON itself is fine to keep storing in the database.
* Encrypting the documents when stored
    * Key management is clearly a topic that requires due attention; in a first instance the suggestion is to have an encryption / decryption key per Storage Bucket. 
* Putting limits on each Storage Bucket that the platform enforces
    * This is obviously also a key subscription adjustment point
* Removing the contents from the external store when the containing Document is deleted. 
