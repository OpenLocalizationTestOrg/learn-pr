Many applications use a publish-subscribe model to notify distributed components that something happened, or that some object changed. Suppose you have a music-sharing application with a Web API that runs in Azure. When a user uploads a new song, you need to notify all the mobile apps installed on user devices around the world who are interested in that genre.

In this architecture, the publisher of the sound file doesn't need to know about any of the subscribers interested in the shared music. Also, we want to have a one-to-many relationship where we can have multiple subscribers who can optionally decide whether they are interested in this new song. Azure Event Grid is a perfect solution for this sort of architecture.

## What is Azure Event Grid?
Azure Event Grid is a fully-managed event routing service running on top of Azure Service Fabric. Event Grid distributes _events_ from different sources, such as Azure Blob storage accounts or Azure Media Services, to different handlers, such as Azure Functions or Webhooks. Event Grid was created to make it easier to build event-based and serverless applications on Azure.

Event Grid supports most Azure services as a publisher or subscriber and can be used with third-party services. It provides a dynamically scalable, low-cost, messaging system that allows publishers to notify subscribers about a status change. The following illustration shows Azure Event Grid receiving messages from multiple sources and distributing them to event handlers based on subscription.

There are several concepts in Azure Event Grid that connect a source to a subscriber:

- **Events:** What happened.
- **Event sources:** Where the event took place.
- **Topics:** The endpoint where publishers send events.
- **Event subscriptions:** The endpoint or built-in mechanism to route events, sometimes to multiple handlers. Subscriptions are also used by handlers to filter incoming events intelligently.
- **Event handlers:** The app or service reacting to the event.

![An illustration showing an Azure Event Grid positioned between multiple event sources and multiple event handlers. The event sources send events to the Event Grid and the Event Grid forwards relevant events to the subscribers. Event Grid use topics to decide which events to send to which handlers. Events sources tag each event with one or more topics, and event handlers subscribe to the topics they are interested in.](../media/4-event-grid.png)

### What is an event?
**Events** are the data messages passing through Event Grid that describe what has taken place. Each event is self-contained, can be up to 64 KB, and contains several pieces of information based on a schema defined by Event Grid:

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

| Field | Description |
|-------|-------------|
| **topic** | The full resource path to the event source. Event Grid provides this value. |
| **subject** | Publisher-defined path to the event subject. |
| **id** | The unique identifier for event. |
| **eventType** | One of the registered event types for this event source. This is a value you can create filters against, e.g. `CustomerCreated`, `BlobDeleted`, `HttpRequestReceived`, etc. |
| **eventTime** | The time the event was generated based on the provider's UTC time. |
| **data** | Specific information that is relevant to the type of event. For example, an event about a new file being created in Azure Storage has details about the file, such as the `lastTimeModified` value. Or, an Event Hubs event has the URL of the Capture file. This field is optional. |
| **dataVersion** | The schema version of the data object. The publisher defines the schema version. |
| **metadataVersion** | The schema version of the event metadata. Event Grid defines the schema of the top-level properties. Event Grid provides this value. |

> [!TIP]
> Event Grid sends an event to indicate something has happened or changed. However, the _actual object_ that was changed is not part of the event data. Instead, a URL or identifier is often passed to reference the changed object.

### What is an event source?
Event sources are responsible for sending events to Event Grid. Each event source is related to one or more event types. For example, Azure Storage is the event source for blob created events. IoT Hub is the event source for device created events. Your application is the event source for custom events that you define. We'll look at event sources in more detail in a moment.

Azure Event Hub has the concept of an event publisher which is often confused with the event source. A publisher to Event Hub is the user or organization that decides to send events to Event Grid. For example, Microsoft publishes events for several Azure services. You can publish events from your own application. Organizations that host services outside of Azure can publish events through Event Grid. The event source is the publisher and the specific service generating the event for that publisher. In this unit, we use "publisher" and "event source" interchangeably to represent the entity sending the message to Event Hub.

### What is an event topic?
Event topics categorize events into groups. Topics are represented by a public endpoint and are where the event source sends events _to_. When designing your application, you can decide how many topics to create. Larger solutions will create a custom topic for each category of related events, while smaller solutions might send all events to a single topic. For example, consider an application that sends events related to modifying user accounts and processing orders. It's unlikely any event handler wants both categories of events. Create two custom topics and let event handlers subscribe to the one that interests them. Event subscribers can filter for the event types they want from a specific topic.

Topics are divided into **system** topics, and **custom** topics.

#### System topics
System topics are built-in topics provided by Azure services. You don't see system topics in your Azure subscription because the publisher owns the topics, but you can subscribe to them. To subscribe, you provide information about the resource you want to receive events from. As long as you have access to the resource, you can subscribe to its events.

#### Custom topics
Custom topics are application and third-party topics. When you create or are assigned access to a custom topic, you see that custom topic in your subscription.

### What is an event subscription?
Event Subscriptions define which events on a topic an event handler wants to receive. A subscription can also filter events by their type or subject, so you can ensure an event handler only receives relevant events.

### What is an event handler?
An event handler (sometimes referred to as an event "subscriber") is any component (application or resource) that can receive events from Event Grid. For example, Azure Functions can execute code in response to the new song being added to the Blob storage account. Subscribers can decide which events they want to handle and Event Grid will efficiently notify each interested subscriber when a new event is available - no polling required.

## Types of event sources
Events can be generated by the following Azure resource types:

- **Azure Subscriptions and Resource Groups:** Subscriptions and resource groups generate events related to management operations in Azure. For example, when a user creates a virtual machine, this source generates an event.
- **Container registry:** The Azure Container Registry service generates events when images in the registry are added, removed, or changed.
- **Event Hub:** Event Hub can be used to process and store events from a variety of data sources - typically logging or telemetry related. Event Hub can generate events to Event Grid when files are captured.
- **Service Bus:** Service bus can generate events to Event Grid when there are active messages with no active listeners.
- **Storage accounts:** Storage accounts can generate events when users add blobs, files, table entries, or queue messages. You can use both blob accounts and General-purpose V2 accounts as event sources.
- **Media Services:** Media Services hosts video and audio media and provides advanced management features for media files. Media Services can generate events when an encoding job is started or completed on a video file.
- **Azure IoT Hub:** IoT Hub communicates with and gathers telemetry from IoT devices. It can generate events whenever such communications arrive.
- **Custom events:** Custom events can be generated using the REST API, or with the Azure SDK on Java, GO, .NET, Node, Python, and Ruby. For example, you could create a custom event in the Web Apps feature of Azure App Service. This can happen in the worker role when it picks up a message from a storage queue.

This deep integration with diverse event sources within Azure ensures that Event Grid can distribute events that relate to almost any Azure resource.

## Event handlers
The following object types in Azure can receive and handle events from Event Grid:

- **Azure Functions:** Custom code that runs in Azure, without the need for explicit configuration of a host virtual server or container. Use an Azure function as an event handler when you want to code a custom response to the event.
- **Webhooks:** A webhook is a web API that implements a push architecture.
- **Azure Logic Apps:** An Azure logic app hosts a business process as a workflow.
- **Microsoft Flow:** Flow also hosts workflows, but it is easier for non-technical staff to use.

## Should you use Event Grid?
Use Event Grid when you need these features:

- **Simplicity:** It is straightforward to connect sources to subscribers in Event Grid.
- **Advanced filtering:** Subscriptions have close control over the events they receive from a topic.
- **Fan-out:** You can subscribe to an unlimited number of endpoints to the same events and topics.
- **Reliability:** Event Grid retries event delivery for up to 24 hours for each subscription.
- **Pay-per-event:** Pay only for the number of events that you transmit.

Event Grid is a simple but versatile event distribution system. Use it to deliver discrete events to subscribers, which will receive those events reliably and quickly. We have one more messaging model to examine - what if we want to deliver a large _stream_ of events? In this scenario, Event Grid isn't a great solution because it's designed for one-event-at-a-time delivery. Instead, we need to turn to another Azure service: Event Hubs.
