You've investigated the metrics that your video storage account sends to Azure Monitor, and you know that you want to display some of these metrics in the .NET mobile application that your administrators use to monitor the behavior of your video management system. Now you need to know how to use the Azure Monitor .NET API to retrieve metrics from Azure Monitor.

## Overview of the Azure Monitor .NET SDK

The Azure Monitor SDK is a set of .NET classes and interfaces that make it simple to query for metrics in Azure Monitor from any .NET project. Using the NuGet package manager, you can add this SDK to desktop application projects, mobile apps, web apps, and other types of custom projects. You could, for example, display in a mobile app how some of those metrics vary over time, or respond if a metric exceeded some critical value.

> [!NOTE] 
> If you want to view metrics from a storage account, you must use the SDK version 0.18.0-preview or later.

## Security Requirements

Azure carefully controls access to metrics by authenticating users and authorizing them to access different resources. In unit 3, when you used **curl** to request storage metrics from Azure Monitor, you used the **az** utility to authenticate as the user you were logged into the Azure portal with. The result of this authentication was an access token, which you passed with your **curl** request the Azure Monitor REST API.

For an application that uses the Azure .NET API to call Azure services, you can create a service principal in your Azure Active Directory. This service principal includes credentials that the app can supply to prove its identity before it makes a request.

You must also assign a role to the service principal, which authorizes that app to access a resource. For example: if you assign the **Contributor** role to an app for a resource group, that app can read and write data to all the resources in that group, including Azure Monitor.

## How to create a service principal

To create a service principal in Azure:

1. Go to the [Azure portal](https://portal.azure.com/) and in the navigation on the left, click **Azure Active Directory**.
1. Click **App registrations**, and then click **New application registration**.
1. Give the service principal a name, choose **Web App/API** for the application type, and specify a sign-on URL where users go for authentication.
1. Create a new key for the app and make a note of it; your app must supply this key as part of its credentials.
1. Make a note of the **Directory ID**; this is a GUID that uniquely identifies the Active Directory that contains the service principal and is also referred to as the **Tenant ID**.
1. Assign the new service principal to a role that has sufficient permissions to access the resources you need. For example, the **Contributor** role on a resource group grants access to all the resources in that group, including any Azure Monitor resources.

If you complete the optional exercise in the next unit, you'll create a service principal by following these steps.

## How to authenticate the application

When the service principal is ready, you can use it in your code to authenticate with Azure. To do this, your code must:

1. Use the **ApplicationTokenProvider.LoginSilentAsync** method to create a new set of service credentials. When you call this method, provide the tenant ID, application ID, and key that you made a note of when you created the service principal.
1. Create a new **MonitorClient** object by calling the **MonitorClient** constructor with the service credentials you created. This **MonitorClient** object will be used to submit queries for metrics.
1. Set the **MonitorClient.SubscriptionID** property to identify which Azure subscription to connect to. This is the same Subscription ID that you used in unit 3.

## How to list metric definitions and metrics values

Once you have created the **MonitorClient** class, you can use it to query for Azure Storage metrics in Azure Monitor. The first step is to discover which metrics are available by listing their definitions.

To do so, call the **MetricDefinitions.ListAsync** method on your **MonitorClient** object, and specify the storage account service's resource ID. This resource ID is the same value as you used in unit 3 to specify the metrics you wanted to list.

>[!NOTE] 
> The resource ID that you specify must correspond to correct service within your storage account; there are different resource IDs for the Blob, File, Table, and Queue services within the same storage account.

Once you have identified the name of a metric to use, you can retrieve its values by calling the **Metrics.ListAsync** method and passing the metric's name and the time period of interest; this will return a collection of the values for that metric within the specific period. You can loop through this collection of values and display them to the user or take other action.
