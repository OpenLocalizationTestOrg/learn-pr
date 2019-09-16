In this module, you learned about two different types of APIs that you can use in order to create applications that access the metrics which Azure Storage accounts send to Azure Monitor when you use Blob storage:

- The Azure Storage REST API is a web service that you can access through HTTP requests through port 80. In unit 3, you learned how to use the **curl** tool to send such requests from the Cloud Shell and view the results.

- In unit 5, you learned how to use the Azure Storage .NET API to make a similar request. You can use this API to interact with Azure Monitor and access storage metrics for display in mobile apps, web applications, and many other types of custom project.

## Clean up

It's important that you clean up any unused resources. You're charged for the configured capacity not how much of the database is used. For this module, we make these resources available to you free of charge. But you should get into the habit of deleting resources when you're done with them.

1. Go to the [Azure portal](https://portal.azure.com/).
1. In the navigation menu on the left, click **Resource Groups**, and then click the resource group that you created as part of the optional exercise in unit 3.
1. In the **Overview** pane, click **Delete resource group**.
1. Enter the name of the resource group that you created to confirm, and then click **Delete**.

## Learn More

- [Azure Storage metrics in Azure Monitor](https://docs.microsoft.com/azure/storage/common/storage-metrics-in-azure-monitor)
- [Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)
- [Azure Monitor REST API Reference](https://docs.microsoft.com/rest/api/monitor/)
- [Azure Storage Metrics Migration](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration)

