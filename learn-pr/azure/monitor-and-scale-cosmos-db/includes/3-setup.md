In this unit, you'll create an Azure Cosmos DB account and use a console application to populate the database.

[!include[](../../../includes/azure-sandbox-activate.md)]

## Create your database account

A database account is a container for multiple Azure Cosmos DB databases.

1. Add a unique name for your database account. This name must be unique across all Azure Cosmos DB instances. Use the following command to generate a random database account name, by using the **Bash** `$RANDOM` variable, and store it in an environment variable to use later.

    ```bash
    export COSMOS_NAME=cosmos$RANDOM
    ```

1. Create an Azure Cosmos DB account by using the following command:

    ```bash
    az cosmosdb create --resource-group <rgn>Sandbox Resource Group</rgn> --name $COSMOS_NAME
    ```

The database account can take 4 to 5 minutes to provision. Keep reading while the account is being created.

## Azure Cosmos DB concepts

### Resources

An Azure Cosmos DB account is a container for one or more _databases_. An Azure Cosmos DB database is a container for one or more _collections_. A collection contains _documents_. A document is an unstructured set of key/value pairs, read and written in JSON format.

### Partitioning

_Partitioning_ is the distribution and grouping of your data across the underlying resources. Documents are grouped in a partition based on the value of the partition key. You specify the partition key when you create the collection. To better understand the concept of partition keys, let's review the property and values in the following JSON example document.

[!code-json[](../code/Order.json)]

Any of these properties, or a combination of them, can be a partition key. For example, where you defined the partition key as a combination of the properties **Category** and **Merchant**, any documents that have matching values for **Category** and **Merchant** are grouped in the same partition.

An effective partitioning strategy distributes data and access evenly across partitions, and across time. Querying documents from within the same partition is less expensive than querying across partitions.

You choose how to partition your data at design time. The partitioning configuration can't be changed after a collection is provisioned.

We examine partitioning concepts and examples in detail in units 4 and 5.

### Indexing

An index is a catalog of document properties and their values. It includes links to documents that contain properties equal to each property value. Indexing makes searching a collection more efficient. But the search efficiency is balanced with the resources required to insert or change a document. When a document is inserted or changed, Azure Cosmos DB has to update the index. The optimal indexing strategy for your collection depends on your workload.

Unlike partitioning, you can change indexing at runtime.

We look at indexing in units 6 and 7.

## Set environment variables for endpoint and keys

1. After the database is created, run the following command to store its endpoint in an environment variable.

    ```bash
    export ENDPOINT=$(az cosmosdb list --resource-group <rgn>Sandbox Resource Group</rgn> \
            --output tsv --query [0].documentEndpoint)
    ```

1. Run the following command to store the access key in an environment variable.

    ```bash
    export KEY=$(az cosmosdb list-keys --resource-group <rgn>Sandbox Resource Group</rgn>  \
            --name $COSMOS_NAME --output tsv --query primaryMasterKey)
    ```

## Create your database and collections

1. Create a database called `mslearn` in your Azure Cosmos DB account. We need only one database for these exercises.

    ```bash
    az cosmosdb database create --resource-group <rgn>Sandbox Resource Group</rgn> \
            --name $COSMOS_NAME --db-name mslearn
    ```

1. Create the first collection.

    We're going to create three collections to compare different partitioning strategies and workloads.

    We'll allocate a smaller capacity to this collection to demonstrate overloading it. The partition key for this collection is the unique identifier of the order. In this case, the partition isn't important because the collection is smaller than a single partition.

    ```bash
    az cosmosdb collection create --resource-group <rgn>Sandbox Resource Group</rgn> \
            --name $COSMOS_NAME --db-name mslearn --collection Small \
            --partition-key-path /id --throughput 400
    ```

1. Create the second collection.

    This collection uses an order item's product category as the partition key. We'll explore the consequences of this choice as we go through the exercises in this module.

    ```bash
    az cosmosdb collection create --resource-group <rgn>Sandbox Resource Group</rgn> \
            --name $COSMOS_NAME --db-name mslearn --collection HotPartition \
            --partition-key-path /Item/Category --throughput 7000
    ```

1. Create a third collection.

    This collection partitions the documents by the order item's unique product identifier.

    ```bash
    az cosmosdb collection create --resource-group <rgn>Sandbox Resource Group</rgn> \
            --name $COSMOS_NAME --db-name mslearn --collection Orders \
            --partition-key-path /Item/id --throughput 7000
    ```

## Populate your collections

We'll use an open-source C# console application to populate your collections. This application generates random order documents and inserts them into your collections. We'll also use this application in later units to query the collections.

1. Clone the console application repository from GitHub. Run the following command in the sandbox environment.

    ```bash
    git clone https://github.com/MicrosoftDocs/mslearn-monitor-azure-cosmos-db
    ```

1. Change into the application's directory.

    ```bash
    cd mslearn-monitor-azure-cosmos-db/ExerciseCosmosDB
    ```

1. Check your environment variables. The console application needs the environment variables to connect to the database. If Azure Cloud Shell times out, you need to set these and the `COSMOS_NAME` variable again. You can reset the `COSMOS_NAME` value by running the following command.

    ```bash
    export COSMOS_NAME=$(az cosmosdb list --output tsv --query [0].name)
    ```

    You can reset your `ENDPOINT` and `KEY` variables by running the following commands.

    ```bash
    export ENDPOINT=$(az cosmosdb list --resource-group <rgn>Sandbox Resource Group</rgn> \
            --output tsv --query [0].documentEndpoint)
    ```

    ```bash
    export KEY=$(az cosmosdb list-keys --resource-group <rgn>Sandbox Resource Group</rgn>  \
            --name $COSMOS_NAME --output tsv --query primaryMasterKey)
    ```

1. Populate the `Small` collection.

    ```bash
    dotnet run -- -c Small -o InsertDocument -n 4000 -p 10
    ```

    Again, the application takes a few minutes to run. We need to populate the database with enough data that we can discern metrics for different partitioning and indexing strategies. To populate this collection, the console application is running with the following options:

    Option|Value|Description
    ------|-----|-----------
    -c|Small|The name of the collection to use.
    -o|InsertDocument|The name of the task to run.
    -n|4000|The number of times to run.
    -p|10|The degree of parallelism to use. That's the number of threads used for the experiment. The higher this number, the greater the demand on the collection.

    The first time you run the application, it shows a welcome message.

    You can see the other options for this application by running `dotnet run -- --help`.

    While the console application runs, you see one line printed per second that shows the status and Request Units needed for the database writes.

1. Populate the `HotPartition` collection.

    ```bash
    dotnet run -- -c HotPartition -o InsertDocument -n 20000 -p 10
    ```

1. Populate the `Orders` collection.

    ```bash
    dotnet run -- -c Orders -o InsertDocument -n 20000 -p 10
    ```
