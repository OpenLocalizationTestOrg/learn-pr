Suppose you are planning the architecture of a distributed music-sharing application. You want to ensure that the application is as reliable and scalable as possible, and you intend to use Azure technologies to build a robust communication infrastructure.

Before you can choose the right Azure technology, you must understand each individual communication that the components of the application exchange. For each communication, you can choose a different Azure technology.

The first thing to understand about a communication is whether it sends **messages** or **events**. This knowledge will help you choose the appropriate Azure service to use.

#### Communication strategies in Azure (APIs)

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2yuaw]

## What is a message?
In the terminology of distributed applications, **messages** have the following characteristics:

- A message contains raw data, produced by one component, that will be consumed by another component.
- A message contains the data itself, not just a reference to that data.
- The sending component expects the message content to be processed in a certain way by the destination component. The integrity of the overall system may depend on both sender and receiver doing a specific job.

For example, suppose a user uploads a new song by using the mobile music-sharing app. The mobile app must send that song to the web API that runs in Azure. The song media file itself must be sent, not just an alert that indicates that a new song has been added. The mobile app expects that the web API will store the new song in the database and make it available to other users. This is an example of a message.

## What is an event?

**Events** are lighter weight than messages, and are most often used for broadcast communications. The components sending the event are known as **publishers**, and receivers are known as **subscribers**.

With events, receiving components will generally decide in which communications they are interested, and will "subscribe" to those events. The subscription is managed by an intermediary, like Azure Event Grid or Azure Event Hubs. When publishers send an event, the intermediary will route that event to interested subscribers. This pattern is known as a "publish-subscribe architecture." It's not the only way to deal with events, but it is the most common.

Events have the following characteristics:

- An event is a lightweight notification that indicates that something happened.
- The event may be sent to multiple receivers, or to none at all.
- Events are often intended to "fan out," or have a large number of subscribers for each publisher.
- The publisher of the event has no expectation about the action a receiving component takes.
- Some events are discrete units and unrelated to other events. 
- Some events are part of a related and ordered series.  

For example, suppose the music file upload has been completed, and the new song has been added to the database. In order to inform users of the new file, the web API must inform the web front end and mobile app users of the new file. The users can choose whether to listen to the new song, so the initial notification does not include the music file but only notifies users that the song exists. The sender does not have a specific expectation that the event receivers will do anything particular in responsiveness of receiving this event.

This scenario is an example of a discrete event.

## How to choose messages or events

A single application is likely to use events for some purposes and messages for others. Before you choose, you must analyze your application's architecture and all its use cases, to identify all the different purposes where its components have to communicate with each other.

Events are more likely to be used for broadcasts and are often ephemeral, meaning a communication might not be handled by any receiver if none is currently subscribing. Messages are more likely to be used where the distributed application requires a guarantee that the communication will be processed.

For each communication, consider the following question: **Does the sending component expect the communication to be processed in a particular way by the destination component?**

If the answer is _yes_, choose to use a message. If the answer is _no_, you may be able to use events.

Understanding how your components need to communicate will help you to choose how your components will communicate. Let's start with messages.