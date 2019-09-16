Suppose you are planning the architecture for your music-sharing application. You want to ensure that music files are uploaded to the web API reliably from the mobile app - we then want to deliver the details about new songs directly to the app when an artist adds new music to their collection. This is a perfect use of a message-based system and Azure offers two solutions to this problem:

- Azure Queue Storage
- Azure Service Bus

## What is Azure Queue Storage?
Queue storage is a service that uses Azure Storage to store large numbers of messages that can be securely accessed from anywhere in the world using a simple REST-based interface. Queues can contain millions of messages, limited only by the capacity of the storage account that owns it.

## What is Azure Service Bus Queues?
Service Bus is a message broker system intended for enterprise applications. These apps often utilize multiple communication protocols, have different data contracts, higher security requirements, and can include both cloud and on-premises services. Service Bus is built on top of a dedicated messaging infrastructure designed for exactly these scenarios.

Both of these services are based on the idea of a "queue" which holds sent messages until the target is ready to receive them.

## What are Azure Service Bus Topics?
Azure Service Bus topics are like queues, but can have multiple subscribers. When a message is sent to a topic instead of a queue multiple components can be triggered to do their work. Imagine in a music sharing application, a user listening to a song. The mobile app might send a message to the "Listened" topic. That topic will have a subscription for "UpdateUserListenHistory" and a different subscription for "UpdateArtistsFanList". Each of those functions is handled by a different component that receives its own copy of the message.

Internally, topics use queues. When you post to a topic, the message is copied and dropped into the queue for each subscription. The queue means that the message copy will stay around to be processed **by each subscription branch** even if the component processing that subscription is too busy to keep up.

## Benefits of queues
Queue infrastructures can support many advanced features that make them very useful in the following ways.

### Increased reliability
Queues are used by distributed applications as a temporary storage location for messages pending delivery to a destination component. The source component can add a message to the queue and destination components can retrieve the message at the front of the queue for processing. Queues increase the reliability of the message exchange because, at times of high demand, messages can simply wait until a destination component is ready to process them.

### Message delivery guarantees
Queuing systems usually guarantee delivery of each message in the queue to a destination component. However, these guarantees can take different approaches:

- **At-Least-Once Delivery:** In this approach, each message is guaranteed to be delivered to at least one of the components that retrieve messages from the queue. Note, however, that in certain circumstances, it is possible that the same message may be delivered more than once. For example, if there are two instances of a web app retrieving messages from a queue, ordinarily each message goes to only one of those instances. However, if one instance takes a long time to process the message, and a time-out expires, the message may be sent to the other instance as well. Your web app code should be designed with this possibility in mind.

- **At-Most-Once Delivery:** In this approach, each message is not guaranteed to be delivered, and there is a very small chance that it may not arrive. However, unlike At-Least-Once delivery, there is no chance that the message will be delivered twice. This is sometimes referred to as "automatic duplicate detection".

- **First-In-First-Out (FIFO):** In most messaging systems, messages usually leave the queue in the same order in which they were added, but you should consider whether this order is guaranteed. If your distributed application requires that messages are processed in precisely the correct order, you must choose a queue system that includes a FIFO guarantee.

### Transactional support
Some closely related groups of messages may cause problems when delivery fails for one message in the group.

For example, consider an e-commerce application. When the user clicks the **Buy** button, a series of messages might be generated and sent off to various processing destinations:

- A message with the order details is sent to a fulfillment center
- A message with the total and payment details is sent to a credit card processor. 
- A message with the receipt information is sent to a database to generate an invoice for the customer

In this case, we want to make sure _all_ messages get processed, or none of them are processed. We won't be in business long if the credit card message is not delivered, and all our orders are fulfilled without payment! You can avoid these kinds of problems by grouping the two messages into a transaction. Message transactions succeed or fail as a single unit - just like in the database world. If the credit card details message delivery fails, then so will the order details message.

## Which service should I choose?
Having understood that the communication strategy for this architecture should be a message, you must choose whether to use Azure Storage queues or Azure Service Bus, both of which can be used to store and deliver messages between your components. Each has a slightly different feature set, which means you can choose one or the other, or use both, depending on the problem you are solving.

#### Choose Service Bus Topics if

- you need multiple receivers to handle each message


#### Choose Service Bus queues if:

- You need an At-Most-Once delivery guarantee.
- You need a FIFO guarantee.
- You need to group messages into transactions.
- You want to receive messages without polling the queue.
- You need to provide a role-based access model to the queues.
- You need to handle messages larger than 64 KB but less than 256 KB.
- Your queue size will not grow larger than 80 GB.
- You would like to be able to publish and consume batches of messages.

Queue storage isn't quite as feature-rich, but if you don't need any of those features, it can be a simpler choice. In addition, it's the best solution if your app has any of the following requirements.

#### Choose Queue storage if:

- You need an audit trail of all messages that pass through the queue.
- You expect the queue to exceed 80 GB in size.
- You want to track progress for processing a message inside of the queue.

A queue is a simple, temporary storage location for messages sent between the components of a distributed application. Use a queue to organize messages and gracefully handle unpredictable surges in demand. 

Use Storage queues when you want a simple and easy-to-code queue system. For more advanced needs, use Service Bus queues. If you have multiple destinations for a single message, but need queue-like behavior, use topics.