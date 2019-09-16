Accessing and processing data are key tasks in many software solutions. Consider some of these scenarios:

* You've been asked to implement a way to move incoming data from Blob storage to Azure Cosmos DB.
* You want to post incoming messages to a queue for processing by another component in your enterprise.
* Your service needs to grab gamer scores from a queue and update an online scoreboard.

All of these examples are about moving data. The data source and destinations differ from scenario to scenario, but the pattern is similar. You connect to a data source, and you read and write data. Azure Functions helps you integrate with data and services by using bindings. 

## What is a binding?

In Azure Functions, bindings provide a declarative way to connect to data from within your code. They make it easier to integrate with data streams consistently in a function. You can have multiple bindings providing access to different data elements. This is powerful because you can connect to your data sources without having to code specific connection logic (like database connections or web API interfaces).

### Types of bindings

There are two kinds of bindings you can use with functions:

1. **Input binding**
    An input binding is a connection to a data **source**. Our function can read data from these inputs.

1. **Output binding**
    An output binding is a connection to a data **destination**. Our function can write data to these destinations.

There are also triggers. Triggers are special types of input bindings that cause a function to execute. For example, an Azure Event Grid notification can be configured as a trigger. When an event occurs, the function will run.

### Types of supported bindings

The *type* of binding defines where we are reading or sending data. There is a binding to respond to web requests and a large selection of bindings to interact directly with various Azure services as well as third-party services.

A binding type can be used as an input, an output or both. For example, a function can write to Azure Blob Storage output binding, but a blob storage update could trigger another function.

Some common binding types are listed below:
- Blob Storage
- Azure Service Bus Queues
- Azure Cosmos DB
- Azure Event Hubs
- External Files
- External Tables
- HTTP endpoints

These types are just a sample. There are more, plus functions have an extensibility model to add more bindings.

### Binding properties

Three properties are required in all bindings. You may have to supply additional properties based on the type of binding and storage you are using.

1. **Name**
    Defines the function parameter through which you access the data. For example, in a queue input binding, this is the name of the function parameter that receives the queue message content. 

1. **Type**
    Identifies the type of binding, i.e., the type of data or service we want to interact with.

1. **Direction**
    Indicates the direction data is flowing, i.e., is it an input or output binding?

Additionally, most binding types also need a fourth property: 

4. **Connection**
    Provides the name of an app setting key that contains the connection string. Bindings use connection strings stored in app settings to keep secrets out of the function code. This makes your code more configurable and secure.

## Create a binding

Bindings are defined in JSON. A binding is configured in your function's configuration file, which is named *function.json* and lives in the same folder as your function code.

 Let's examine a sample *input binding*:

```json
    ...
    {
      "name": "headshotBlob",
      "type": "blob",
      "path": "thumbnail-images/{filename}",
      "connection": "HeadshotStorageConnection",
      "direction": "in"
    },
    ...
```

To create this binding, we:

1. Create a binding in our *function.json* file.

1. Provide the value for the `name` variable. In this example, the variable holds the blob data.

1. Provide the storage `type`. In the preceding example, we are using Blob storage.

1. Provide the `path`, which specifies the container and the item name that goes in it. The `path` property is required when using the Blob trigger, and should be provided in the style shown here, with curly braces around the filename portion of the path. This creates a *binding expression* that allows you to reference the blob's name in other bindings and in your function's code. In this example, a parameter on the function named *filename* would be populated with the filename of the blob that triggered the function.

1. Provide the `connection` string setting name defined in the application's settings file. It's used as a key to find the connection string to connect to your storage account.

1. Define the `direction` as `in`. It reads data from the blob.

Bindings are used to connect to data in your function. In this example, we used an input binding to connect user images to be processed by our function as thumbnails.
