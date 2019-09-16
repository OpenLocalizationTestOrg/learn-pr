Let's start by taking a quick look at Azure storage services, data styles, and accounts. 

Microsoft Azure Storage is a managed service that provides durable, secure, and scalable storage in the cloud. Let's break these terms down.

| | |
|-|-|
| **Durable** | Redundancy ensures that your data is safe in the event of transient hardware failures. You can also replicate data across datacenters or geographical regions for additional protection from local catastrophe or natural disaster. Data replicated in this way remains highly available in the event of an unexpected outage. |
| **Secure** | All data written to Azure Storage is encrypted by the service. Azure Storage provides you with fine-grained control over who has access to your data. |
| **Scalable** | Azure Storage is designed to be massively scalable to meet the data storage and performance needs of today's applications. |
| **Managed** | Microsoft Azure handles maintenance and any critical problems for you. |

A single Azure subscription can host up to 200 storage accounts, each of which can hold 500 TB of data. If you have a business case, you can talk to the Azure Storage team and get approval for up to 250 storage accounts in a subscription, which pushes your max storage up to 125 Petabytes!

## Azure data services

Azure storage includes four types of data:

- **Blobs**: A massively scalable object store for text and binary data.
- **Files**: Managed file shares for cloud or on-premises deployments.
- **Queues**: A messaging store for reliable messaging between application components.
- **Tables**: A NoSQL store for schemaless storage of structured data. This service has been replaced by Azure Cosmos DB and will not be discussed here.

All of these data types in Azure Storage are accessible from anywhere in the world over HTTP or HTTPS. Microsoft provides SDKs for Azure Storage in a variety of languages, as well as a REST API. You can also visually explore your data right in the Azure portal.

### Blob storage

Azure Blob storage is an object storage solution optimized for storing massive amounts of unstructured data, such as text or binary data. Blob storage is ideal for:

- Serving images or documents directly to a browser, including full static websites.
- Storing files for distributed access.
- Streaming video and audio.
- Storing data for backup and restoration, disaster recovery, and archiving.
- Storing data for analysis by an on-premises or Azure-hosted service.

Azure Storage supports three kinds of blobs:

| Blob type | Description |
|-----------|-------------|
| **Block blobs** | Block blobs are used to hold text or binary files up to ~5 TB (50,000 blocks of 100 MB) in size. The primary use case for block blobs is the storage of files that are read from beginning to end, such as media files or image files for websites. They are named block blobs because files larger than 100 MB must be uploaded as small blocks, which are then consolidated (or committed) into the final blob. |
| **Page blobs** | Page blobs are used to hold random-access files up to 8 TB in size. Page blobs are used primarily as the backing storage for the VHDs used to provide durable disks for Azure Virtual Machines (Azure VMs). They are named page blobs because they provide random read/write access to 512-byte pages. |
| **Append blobs** | Append blobs are made up of blocks like block blobs, but they are optimized for append operations. These are frequently used for logging information from one or more sources into the same blob. For example, you might write all of your trace logging to the same append blob for an application running on multiple VMs. A single append blob can be up to 195 GB. |

### Files

Azure Files enables you to set up highly available network file shares that can be accessed by using the standard Server Message Block (SMB) protocol. This means that multiple VMs can share the same files with both read and write access. You can also read the files using the REST interface or the storage client libraries. You can also associate a unique URL to any file to allow fine-grained access to a private file for a set period of time. File shares can be used for many common scenarios:

- Storing shared configuration files for VMs, tools, or utilities so that everyone is using the same version.
- Log files such as diagnostics, metrics, and crash dumps.
- Shared data between on-premises applications and Azure VMs to allow migration of apps to the cloud over a period of time.

### Queues

The Azure Queue service is used to store and retrieve messages. Queue messages can be up to 64 KB in size, and a queue can contain millions of messages. Queues are generally used to store lists of messages to be processed asynchronously.

You can use queues to loosely connect different parts of your application together. For example, we could perform image processing on the photos uploaded by our users. Perhaps we want to provide some sort of face detection or tagging capability, so people can search through all the images they have stored in our service. We could use queues to pass messages to our image processing service to let it know that new images have been uploaded and are ready for processing. This sort of architecture would allow you to develop and update each part of the service independently.

## Azure storage accounts

To access any of these services from an application, you have to create a _storage account_. The storage account provides a unique namespace in Azure to store and access your data objects. A storage account contains any blobs, files, queues, tables, and VM disks that you create under that account.

### Creating a storage account

You can create an Azure storage account using the Azure portal, Azure PowerShell, or Azure CLI. Azure Storage provides three distinct account options with different pricing and features supported.

> [!div class="mx-tableFixed"]
> | Account type | Description |
> |--------------|-------------|
> | **General-purpose v2 (GPv2)** | General-purpose v2 (GPv2) accounts are storage accounts that support all of the latest features for blobs, files, queues, and tables. Pricing for GPv2 accounts has been designed to deliver the lowest per gigabyte prices. |
> | **General-purpose v1 (GPv1)** | General-purpose v1 (GPv1) accounts provide access to all Azure Storage services but may not have the latest features or the lowest per gigabyte pricing. For example, cool storage and archive storage are not supported in GPv1. Pricing is lower for GPv1 transactions, so workloads with high churn or high read rates may benefit from this account type. |
> | **Blob storage accounts** | A legacy account type, blob storage accounts support all the same block blob features as GPv2, but they are limited to supporting only block and append blobs. Pricing is broadly similar to pricing for general-purpose v2 accounts. |

If you are interested in learning more about creating storage accounts, make sure to go through the **Create an Azure storage account** module in the learning portal.
