Azure Data Box family devices aren't the only ways to get data into Azure. For your autonomous vehicle data, make sure that Data Box Disk is your best choice before you place an order.

In this unit, you'll learn when to use a Data Box device and when to use some other solution.

## Offline transfer

To get data into Azure, you can load your data onto physical devices and send them to an Azure datacenter. There, the data is copied into your Azure subscription. As you saw in unit 2, three Data Box products work this way:

- Data Box
- Data Box Disk
- Data Box Heavy

If you're not in a region supported by Azure Data Box family, consider using Azure Import/Export to import data into Azure. Azure Data Box family doesn't support export of data from Azure.

### Azure Import/Export

The other service that uses physical devices to transfer data into Azure is Azure Import/Export. Azure Import/Export allows you to use your own disks. You can do offline transfers of data from Blob storage to disks. Azure Import/Export also exports data from Azure back to your own datacenters and allows data to cross international borders. 

The setup procedure to import data using Azure Import/Export is more difficult than Azure Data Box family. You have to handle all the shipping details and device preparation. We recommend using Azure Data Box, a done-for-you service that minimizes the effort required to move huge volumes of data inexpensively and securely into Azure.

## Network transfer

Another general method to get data into Azure is to send it across the internet. As you saw in unit 2, two Data Box products work this way:

- Data Box Edge
- Data Box Gateway

There are several other tools you can use to transfer data across the network.

### Azure Storage Explorer

Azure Storage Explorer is a free, downloadable application that you can run on Windows, Mac, or Linux. It provides a graphical user interface (GUI) that you can use to explore blobs, files, queues, and tables in Azure storage accounts. If you transfer only a few files at a time and you don't need to automate transfers, Storage Explorer is an ideal solution.

If you don't want to install an application, but you have a browser, you can use the Storage Explorer page in the Azure portal to transfer files.

### Azure Data Factory

Azure Data Factory is a service that enables you to organize, move, and transform large quantities of data from many different sources. In Data Factory, you create data pipelines that ingest data from relational databases, NoSQL databases, and other systems. You can use Azure Machine Learning, Hadoop, Spark, and other services to process and transform that data. Then, at the end of the pipeline, you can publish the transformed data to Azure SQL Data Warehouse, Azure SQL Database, Azure Cosmos DB, and Azure Storage.

Use this service, if you have complex data transformation needs but don't want to write scripts or compile code.

### Scripted or programmatic transfer

Microsoft provides a range of optimized command-line tools, which you can call with individual commands or assemble into scripts, and which can move data into Azure. These tools include:

- **AzCopy.** You can call this command-line program from any script to transfer blobs or files to an Azure storage account. 
- **Azure PowerShell.** PowerShell is a scripting interface and language designed to help administrators automate complex tasks by running commands called **cmdlets**. The Azure PowerShell module provides a range of cmdlets to work with Azure. These include cmdlets that you can use to upload data into Azure storage accounts, Azure SQL Database, Azure Cosmos DB, and other destinations. For example, to upload a file to an Azure Storage blob, use the `Set-AzStorageBlobContent` cmdlet.
- **Azure CLI.** The Azure CLI is a command-line tool that you can call from PowerShell and Bash scripts to work with Azure. It includes options that can move data into Azure locations. For example, use the command `az storage blob upload` to upload a file to an Azure Storage blob.

Scripting tools are easy for administrators and non-developers to use. If you have people with developer skills who are able to write compiled code, you can consider using programming languages to orchestrate data transfer into Azure. Your code must call the Azure Storage Representational State Transfer (REST) application programming interfaces (APIs). The REST APIs are a set of web services that you can call from many different programming languages. Developers can use .NET languages, Java, Python, Node.js, C++, PHP, among other languages, to call these APIs. They'll choose the one they're most familiar with.

## Choosing a data transfer method

To choose a transfer method from these systems, consider the following questions:

- What's the size of the data? 
- How often do I want to transfer data?
- How much network bandwidth is available?

If you want to transfer a few files occasionally, choose Azure Storage Explorer or use the Azure portal.

If network bandwidth is low or expensive to use, and you have a large volume of data, use a physical device to transfer data. If you want to use your own disks, choose Azure Import/Export. If you prefer the convenience of using Microsoft hardware, choose an Azure Data Box product. Choose from Data Box, Data Box Disk, and Data Box Heavy according to their maximum data volumes.

If you have plenty of available network bandwidth and you want to transfer data regularly or continuously, choose a network transfer method. If you want to transform the data as you transfer it, without using code, consider Azure Data Factory. If you can invest the time and have the skills to write code, use scripting tools or developed compiled code against the REST APIs. If you want to transfer large datasets over the network and you have high available bandwidth, choose Data Box Edge or Data Box Gateway.

## Data transfer for the autonomous vehicles

In your organization, you want to upload autonomous vehicle telemetry data to Azure in a fast, relatively cheap, and secure way without hogging the network. That's a key use case for Azure Data Box Disk.

You can use Azure Data Box Disk for different kinds of data transfer scenarios. Azure Data Box Disk is suitable for one-time migrations like moving up to 35 TB of data from offline tapes to Azure cool storage. If there's more than 35 TB to upload, you can create extra orders. It's also worth reviewing the other Data Box family options that you explored in the first unit, as there might be a more suitable candidate. Incremental transfers also work well. Data Box Disk is used to provide the initial, large seed, after which further increments of data are copied by using standard network techniques.
