#Storage Account
Based on the type of storage account you create you can have access to different services in the storage account

General type V2 will give you access to the following services
1- Blob storage
2- Table
3- Queue
4- File

## Different Type of Storage Accounts

1- Standard General purpose V2: you will access to blob, table, file share, queue
2- Premium block blobs: This is supported for block and append blobs. this is fast access to your blobs and high transaction rates.
3- Premium Page blobs: This is supported for page blobs when you need fast storage for you VMs, best for random read and write operations.
4- Premium File Shares: support file share and provide fast access to your file share.

**Storage account name needs to be globally unique.**

## Create Storage Account

Once you create storage account you will be able to create blob service and it will have container as a root directory and then you can upload files , images , videos

### Different type of blobs

1- Block blobs: made of blocks and good individually managed files
2- Append blobs : made of block blobs and optimized for append operations good for logging
3- Page blobs: This is used for virtual hard drive files for vms

### Authorization types

we can have public access, azure account and portal

#### Using Access key

You can get the keys via **Access Keys** section on the storage account

#### Share Access Signature - At the blob level

You can make a blob object not accessible to annonymous users but generate SAS (Shared Access Signature which is a URI that grants limitted access to Azure Storage blob) it can be used for some specific time or you can define allowed IP addresses .

You can have Shared Access Signature at Storage Account Level as well.

#### Stored Access Policy

You can go to storage account's **Access Policy** and selected stored policy and then Add policy

You can create access policy and assign the policy to shared access signature then if you want to void shared access signature you can modiy the access key associated with the shared access signature with no permissions.

When the client / user wants to access to the container via shared access signature they will prompted to select access policy they will get sahred access signature which is basically a URL and when they create connection in the client application they will be asked to provide the connection info like shared access signature which has got access policy linked to it

#### Different types of SAS

Account SAS - at account level
Service SAS - at blob storage service level
User Delegation SAS : SAS will be secured with Azure AD Credentials

### Azure AD Authentication

you can create a user and then assign a storage account contributor role and that user will be able to download / upload blobs in storage account

## Access Tiers

we have two factors to consider when it comes to the pricing of the storage account first is the amount storage you will need and second the access speed to your blobs

you will have three access types
1- Hot when accessed frequently. Enabled at Storage account level
2- Cool When accessed infrequently and stored at least 30 days. at storage account level
3- Archive when rarely accessed and stored for at least 180 days. at blob level.

Hot and Cool Access tier is only available for blob storages.
Archive access tier only avaialble for individual blobs and they will not be accessible in Archive Access tier and you will need to rehydrate to Hot or Cool Access tier which might take serverl hours.

There is a feature called lifecycle management rules to change access tiers
or delete an object

### Lifecycle management

It will happen at storage account

1- Rule Filters: You can define filter for blob types like blockBlob, appendBlob.
2- Rule Actions: You will have actions such as tierToCool, tierToArchive and delete
3- Support: Rules are supported for blob and append blobs in General-Purpose V2 Accounts, Premium Block blob and Blob Storage accounts. Append Blob is only have delete action supported.
4- This feature is available in all regions.

### Archive Tier

Standard priority : up to 15 hours
High priority : less than one hour for objects less than 15 GB in size.

## Blob Versioning

1- Versions allow you to maintain previous version of the blob.
2- Restore: you can restore an ealier version of the blob
3- Version ID: each blob gets an initial version ID.A new version is set when the blob is updated
4- You can enable this feature any point in time.

Versioning and Snapshosts are supported in general-purpose v2 and blob storage accounts

Snapshots provide a point-in-time, read only copy of the data in a specifc blob.

Versioning is at container level.
Snapshots are point-in-time copies of the individual blobs.

Versioning is automatic and occurs with every modification
Snapshots are created explicitly by the user

## Soft Delete

1- You can specify retention period for deleted objects, between 1 and 365 days
2- Depending on the retention period, the data will be available after it has been deleted or overwritten.

3- You can restore a deleted blob along with its snapshots during retention period
4- You can chagne the soft delete retention period at any time.

## Lease (Lock)

you can get lease from the blob client object like the following code
1- Create BlobClient with connection string , container name and blob name
2- Create BlobLeaseClient you can call GetBlobLeaseClient method on blob client
3- Then you specify lease time
4- BlobLeaseClient object can call AcquireAsync method with lease time

## AzCopy tool

command tools you can copy or transfer blobs between storage accounts it is a executable tool.
Some of the commands in AzCopy tools
1- azcopy make _url including storageaccount/containername/sv=SAS_ to create a container you will need to pass a uri with shared access signature
2- azcopy copy _local file path_ _url including storage account/containername/newblobname/?sv=SAS_
3- azcopy copy _url including storage account/containername/blobname?sv=SAS_ _downloaded file name_

## Moving Storage account to another region

1-Create a new storage account
2-Copy data from source storage account to destination storage account
3-Delete storage account from the source region

## Change feed

1- Purpose: Provides and ordered, guaranteed, durable, immutable, read-only log of changes
2- Operations: Audit log of events to blob data - Create, Update, Delete
3- Records: The change feed data is stored in a container name $blobchangefeed
4- The records are stored in Apache Avro format.

# Table Storage

This is used for non-relational structured data
Based on structured NoSQL data
you will follow a key/attribute store with shemaless design

## Elements

A table is a collection of entities
The entities do not abide by any schema
Each entity can have different set of properties
An entity is made up of properites
Each property is name / value paird

### Entity

1- Partition Key: this is a string value and identifies the partition that the entity belongs to
2- Row Key: this is a string value, this uniquely identifies each entity within the partition
3- The partition key along with the Row Key helps to uniquely identify the entity with the table

### Add entity to table stroage with .NET

1- Create TableClient with connection string of the storage account and table name
2- Create TableEntity with partition key and row key values
3- Call AddEntity method on the table client object

### Reading entities

you can use table client and call Query method with Table entity and similar expression systax and get Pagable<TableEntity> returned
In order to get the properites values you can run TableEntity.GetInt32("quantity") format

### Delete Entity

You will need partition key and row key to identify the entity
Then you can use table client and call DeleteEntity with those partiton key and row key values.
