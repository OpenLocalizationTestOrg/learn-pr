The Azure Search service needs to have JSON documents loaded into an index. It's these documents that are searched.

As part of the drive to establishing your organization's online presence, you need to implement an Azure Search indexer to enable rich search for the online video catalog. You'll be loading data into an existing Azure Search instance so customers can search the catalog by title, difficulty, length, and publication date.

Now that the Azure Search service has been created, you'll look at how to load data into an index.

## Loading data into Azure Search

Azure Search lets you create empty indexes, in order for indexes to be queried data must be loaded. The search queries run over the content loaded and saved to an index. There are two approaches for loading data into an index:

- **Pushing data**: JSON data is pushed into an Azure Search Index via either the REST API or the .Net SDK. Pushing data has the most flexibility as it has no restrictions on the data source type, or frequency of execution.

- **Pulling data**:  Search service indexers can be created using either the Azure Data Import wizard, or programmatically. Data sources are crawled to load JSON documents into the index. An indexer maps source fields to their matching fields in the index. If necessary, an indexer will transform the imported data into a JSON format.

### Methods for importing data to Azure Search

**Azure portal**

Contained within the Azure Search Service dashboard in the Azure portal is the Import Data wizard. The wizard imports data into the index by connecting to an external data source in your Azure subscription. The wizard crawls the data source for content, and converts it into JSON documents to be imported into the index.

The supported data sources are:

- Existing data source from another indexer
- Azure SQL Database
- SQL Server on an Azure VM
- Cosmos DB
- Azure Blob storage
- Azure Table storage (cognitive search pipelines aren't supported)

**REST API or .NET SDK**

Data can be pushed or pulled into Azure Search via the Azure Search REST API. The API can be used to create an indexer (**pulling**), or load documents individually, or upload batches up to 1000 documents (**pushing**).

The .NET SDK can be thought of as a wrapper around the REST API, with the exposed classes using the REST API endpoints under the covers. The .NET SDK features match the currently available version of the Azure Search REST API.

To push documents using the REST API, write a POST request to the index's URL endpoint, using a JSON array in the body containing each documents data.  

```json
{
    "value": [
        {
            "id": "d3a9f34f-d8a1-421b-bdd3-adbf826",
            "Title": "Crunches and Curls",
            "Difficulty": "4",
            "Length": "00:4:12",
            "Publication": "2019-04-22 09:56:26",
            "Tags": ["cardio","floor","burn"],
            "Size": "532"
        },...
```

The `@search.action` parameter tells the REST API how the document should be processed. You can use `upload`, `merge`, `mergeOrUpload`, or `delete`.

To push documents into the index in C#, after importing the `Microsoft.Azure.Search` package, write C# code that:

- Creates a `SearchIndexClient` object class for connecting to the search index
- Creates an `IndexBatch` object to contain JSON search documents
- Calls the `Documents.Index` method to upload the batch of documents

### Data Source Inputs

If you are programmatically generating your search documents, the data sources are only limited to your ability to generate JSON documents.

If you want to use the Data Import wizard in the Azure portal, you are restricted to the list of supported sources above. There are some caveats for each data source:

- For the Azure SQL databases and SQL Server databases in a VM, only a flattened dataset is allowed, so create a table, or view, for use by the wizard.
- For a Cosmos DB you need to specify the collection to be imported, and all the documents in that collection will be included.
- For Blob storage, there is a specified list of supported document types. You can restrict which files are indexed using the `indexedFileNameExtensions` property of your indexer.
- For Table storage, you can restrict the import to a specific subset of tables

### Index attributes

![Example of how documents are stored in the search index, along with other options](../media/what-is-an-index.png)

An Azure Search index can be thought of as a container, of searchable documents. In database terms, the index is a table in the database, and each document is a row. Tables have columns, the columns can be thought of as equivalent to the fields in a document. Columns have data types, just as the fields do on the documents.

Azure Search needs to know how you'd like to search and display the fields in the documents, and you specify that by assigning attributes, or behaviors, to these fields.

An index can also offer additional features, like suggesters, or allow cross-site requests. All of these components are stored together. Azure Search also creates the physical structures it needs to support these behaviors, like inverted indexes on searchable fields.

### Data import monitoring and verification

The Search services overview page has a dashboard that lets you quickly see the health of the Search service. On the dashboard you can see how many documents are in the Search service, how may indexes have been used, and how much storage is in use.

![text](../media/search-dashboard.png)

When loading new documents into an index, the progress can be monitored by clicking on the indexes associated indexer. The document count will grow as documents are loaded into the index. In some instances, the portal page can take a few minutes to display up-to-date document counts. Once the index is ready for querying, you can then use Search Explorer to verify the results. An index is ready when the first document is successfully loaded.

![Screenshot of the portal, showing the indexer status](../media/indexer-status.png)

Indexers only import new or updated documents, so it is normal to see zero documents indexed.

The Search Explorer can be used to perform quick searches to check the contents of an index, and that you are getting expected search results. Having this tool available in the portal allows you to easily check the index by reviewing the results that are returned as JSON documents.

### Rebuilding indexes

There are only a few ways an index can be edited after it's been created. The REST API and SDK do offer *create or update* methods, but realistically these methods delete an existing index, and then recreates it. You can't change the name of an existing field, change its type, or add additional behaviors like sortable or filterable.

The changes that can be made are limited to adding new fields, or changing the options on the index like setting up CORS, or changing scoring profiles.

Programmatically rebuilding an index will take it offline, with response times possibly being affected for several minutes after the index has been updated.

An approach to updating an index without effecting your end users is to create an entirely new Search service. Create the new version of the index, and indexer. Import the existing search data set. Then switch your app to point the new Search service endpoint.  

### Handling large data volumes

When you create a Search service on the standard pricing tier, by default you only get a single replica (used for data ingestion) and partition (used for data storage). There are quota limits on the size of storage, number of indexes and indexers, and data sources.

If you start to hit the quota limits, or require better guarantees on availability, the portal, and REST API allow you to scale up the number of replicas and partitions. Azure will start to guarantee a 99.9% uptime after you've increased the replicas to at least 3.

Increasing the partitions, each gives you an extra 25 GB of storage, not only increases the total storage available for indexes, it also allows you to set up indexers to run in parallel. You will need to create an indexer per partition, and partition the data set by the number of those partitions. For example, if your video catalog contained 1000 videos, you could split the single JSON document into five separate documents containing 200 videos each, storing the files in their own blob containers. Then set up five indexers and schedule them to start at the same time. The indexers will run and import the documents into the search index.

It isn't necessary to set up parallel indexers for the JSON data used in the examples for this module. As indexing  text files is quick, and has relatively low CPU requirements. However, it's a recommended approach to improve the time to complete data ingestion into your index. For example, if your company in the future wanted to do some image processing, which is more CPU intensive.


## Enhancing Azure Search indexes with AI

Azure Search has AI features embedded, it works by using Azure Cognitive Services (Cognitive Search) to add skills that include image processing, content extraction, or natural language processing (NLP). This makes it possible to index previously unsearchable, or unstructured content.

Processing images and extracting text representations, so that customers can search for text in images. Sentiment analysis on text comments, to look for customers having negative experiences. There's also skill that can enrich a search document if you need to check the language of text.