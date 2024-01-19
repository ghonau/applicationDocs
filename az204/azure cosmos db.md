#Cosmos DB
Fully managed NoSQL database
You get single-digit millisecond response time
Scales automatically based on demand

There are different API for cosmos db that you can choose when you create the services
1- SQL API: use similar query language to SQL database
2- MongoDB: MongoDb
3- Gremlin: Graph API
4- Cassandra: Cassandra (if you are already familiar with)
5- Table: Simple table

## How it works

The terminalogy will change as per your API selection

### Database account

### Database

### Container

### Items

## Partitions

Items in a container are divided into subsets called logical partitions, you will decide which property will be your partition key

each item also has an item id, it helps to identify individual item in a partition.

The combination of a partition key and the item id helps uniquely identify the item within the container

Each logical partition can grow up to 20 GB

there is no limit to the number of partitions with in a container

you can choose a property of the item that will never change as a partition key

you can chagne the partition key once the container is created

## Cost

The cost of the database operations is measured in terms of Request Units
The Request units is a blended measure of CPU, IOPS and memory to perform a specific operation, such as reading or writing a document.

When you create a Cosmos DB container you can provision a certain amount of throughput in terms of Request Units per second.

Cosmos DB also offers autoscale, where service automatically adjusts the provisioned throughput based on thee actual consumption, helping you optimise costs.

When you provision your database account you can choose the number of request units, and you can increment or decrement the number of request units provisioned

You are billed on hourly basis

You can provision throughput at the container or database level.

### Mode

#### Serverless

you do not provision any throughput
It will be managed by service it self if demand increase it will increase throughput
You will be billed based on the Request Units you consume.

#### Autoscale

Request units can automatically scale based on demand
The demand is checked both at database and container level
it is good for mission critical workloads.

#### Partition key

You need to use /partitionkey format to define your partition

#### Running some queries

If you are using SQL API and your data is in json format you can still use sql like query language to retrieve data.

#### .NET with cosmos db

you can create CosmosClient object with the help of cosmosEndpointUri and cosmosDBKey
and then you can use the cosmosDBClient to create Database
and you can get the database object by using GetDatebase on CosmosClient object with the name of the database
When you create a container you will need to pass the **PartitionKey** as well

##### Adding an item

You will need to create a class that represent the item data which means it will have the properties of the json object
1- Create CosmosClient
2- Call GetDatabase from CosmosClient
3- Call GetContainer from Database object
4- Create an object based on the entity class
5- Call CreateItemAsync<Order>(order, new PartitionKey(order.Category))
6- You will receive ItemResponse<Order> as a response
7- You can use the response and access to properties like RequestCharge which shows the Request Units

**You will need to generate your own Id in your entity class**

##### Reading items

1- You will be able to use Select Query like "Select o.orderId , o.category From Orders o"
2- You will pass the query string to the object based on QueryDefinition
3- You will use FeedIterator<Order> feedIterator = container.GetItemQueryIterator<Order>(queryDefinition)
4- You can use while(feedIterator.HasMoreResult) and get FeedResposne<Order> feedResponse = await feedIterator.ReadNextAsync()
5- foreach(var order in feedResponse) and you can use the properties of the order object

##### Replacing Item / Updating Item

We need to have the id and partition key of the item

1- You can use the select query and put the parameter like orderId in the query which returns id and partition key of the item
2- Once you have got the id and partition key using FeedIterator<Order> feedIterator = container.GetItemQueryIterator<Order>(queryDefinition) and then looping through FeedResponse<Order> objects from the feedIterator.ReadNext() method
3- Your container can read the item with id and partition key
4- Your ReadItem method with the id and partitionKey input parameters will return ItemResponse<Order> object
5- You can get var item = response.Resource;
6- Then you can change the quantity like item.Quantity = 150 ;
7- Call container.ReplaceItemAsync<Order>(item, id, partitionKey)

##### Deleting Item

1- We will get the FeedResponse<Order> feedResponse = await feedIterator.ReadNextAsync();
2- You will be able to access to the Order object in feedResponse
3- call container.DeleteItemAsync<Order>(id, new PartitionKey(category))

##### Array of objects in an item

You can add your list of objects in you entity class which will be serialized to array in json format
1- Once you have created your entity which is a complex entity
2- You can call container.CreateItemAsync<Customer>(customer, new PartitionKey(customerCity))

### Table API

Table will have entities and you will need to provide PartitionKey and RowKey when you are entering new entity.

#### .NET

The same package we used for Table storage account can be used to insert entity into CosmosDB Table API

#### difference between CosmosDB Table API and Table Storage Account

with CosmosDB Table API you will use the reserved capcity and will be charged based on the provisioned capacity as soon as you have created the table
With Azure Table Storage you only pay for capcity that is used.

Table API offers 10 ms read and 15 ms write SLA at the 99th percentile while Azure Table Storage offers a 10-second SLA.

#### Choose API

1- SQL API ---> Data can be queried via SQL
2- Table API --- Key value pairs
3- MongoDB API ---> Some similaries with SQL API - json document
4- Cassandra API ---> Column-Friendly database Does not support joins or subqueries
5- Gremlin API ---> data represnts nodes and edges.

#### Stored Procedures

They are written in JavaScript
1- Create CosmosClient with consmosEndpointUri and CosmosDBKey
2- GetContainer with the help of CosmosClient
3- Call container.Scripts.ExecuteStoredProcedureAsync<string>("Display", new PartitionKey("") , null)
4- You will need to get the collection with the help of context object
5- Then you will need to getSelfLink object of the collection object
6- call collection.createDocument(collectionLink , item)

#### Change feed

deletes are not recorded in the feed

you can process the change feed with the use of Azure Functions or a change feed procesor.

The records for the chanage feed are written to another container

The change feed is sorted in the order of modification within each logical partition key value.

##### Change Feed Processor

1- Monitored Container
This has a data from which the change feed data is generated

2- Lease Container
This stores the state and coordinate the processing of change feed across multiple workers

3- Compute Instace
This hosts the change feed processor that is used to listen for changes

4- Delegate
This is the code that is used to process the batch of changes.

##### Change Feed Processor Library

The Library itself

The change feed processor library automatically checks for changes

if the changes are found, they are pushed to the client

if you have enough throughput and lot of changes that need to be read, you can have multiple clients to read the change feed

##### How to do

1- Start Change Processor
2- Get lease container with the help of cosmosClient
3- Get the monitored container with the help of cosmosClient
4- Then you can call GetChangeFeedProcessorBuilder<Order>(processorName: "ManageChanges", onChangeDelegate:ManageChanges).WithInstanceName("appHost").WithLeaseContainer(leaseContainer).Build();
ManageChanges is the logic to process the changes
5- call changeFeedProcessor.StartAsync() ;

##### Composite Index

You will need to create composite index if you want to run a query with order by two or more properties and it is a json object in the settings

##### Time to live

It will automatically delete items from the database, this can be set at the item level or the container level.

Items will be deleted based on the time to live that is set in seconds.

This will be run as a background task that uses the left-over request units.

If you have TTL to work at the item level, the setting should also be defined like 10M seconds at the container level.

##### Consistency

This is the feature under "Replicate data globally" section

You can have write region which is the region you have created you cosmos db account and then you can create some read regions that your users can read data from.

You will have different consistency level
1- Strong ---> Reads are guaranteed to return the most recent committed version
2- Bounded Stateless ---> reads will lag behind writes by at most "K" versions or "T" time interval
3- Session ---> Ensures client's read and write operations are always routed to the same replica set. it means once the client writes data in database it will always read its own writes. Here within a single client session, the reads are guaranteed to honor the consistent-prefix
4- Consistent Prefix ---> client will not see out of order writes changes will be in the right order for the reader clients
5- Eventual ---> Eventually the data will be consist
