The app and Azure Functions are now complete and running locally. In this unit, you'll publish the function to Azure to run in the cloud.

You'll publish your function from Visual Studio. This is a great way to get started for proof-of-concepts, prototypes, and learning, but for a production-quality app you should **not** use this method. You should use some form of CI-based deployment. You can read more about doing this in the [Azure Functions Deployment docs](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment?azure-portal=true).

## Publishing your app to Azure

Azure Functions can be published to Azure from inside Visual Studio.

1. Stop the local Azure Functions runtime if it's still running from the previous unit.

1. Right-click on the `ImHere.Functions` app in the solution explorer and select *Publish...*.

    ![Right-click publish on the Functions app](../media/8-right-click-publish.png)

1. From the **Pick a publish target** dialog, select *Azure Function App*, and for **Azure App Service**, select *Create New*. Click **Publish**.

    ![Creating a new Azure App Service to publish to](../media/8-pick-publish-target.png)

1. Sign in to your Azure account.

1. Enter a name for you app.

1. Select your _Subscription_, _Resource Group_, _Hosting Plan_, and _Storage Account_.

1. Click **Create** to provision all the resources on Azure and publish your Azure Functions app.

    ![Create the App Service](../media/8-create-app-service.png)

1. You may be asked if you want to update the functions version on Azure. If this dialog appears, select **Yes** to ensure your function app is published with the latest Azure Functions runtime version.
    ![The update Azure Functions dialog](../media/8-update-functions-on-azure.png)

Provisioning will take a couple of minutes to complete. The following resources will be provisioned:

- A storage account to store the files needed for the Azure Functions app
- An App Service plan to manage the compute resources needed by the Azure Functions app
- The App Service that runs the Azure Functions

The function will now be published and available to call at **https://\<your-app-name\>.azurewebsites.net/api/SendLocation**.

## Configuring your app

When the Azure Functions was running locally, it was using Twilio credentials that were stored in a `local.settings.json` file. As the name suggests, this file is for local settings, not Azure settings. Before the Azure Functions can be called inside Azure, the `TwilioAccountSid` and `TwilioAuthToken` settings need to be configured.

1. From the Publish tab, click the **Manage Application Settings** option.

    ![The Manage Application Settings option](../media/8-application-settings-option.png)

1. The **Application Settings** dialog will show application settings with both a local and remote value - the local coming from your `local.settings.json` file, and the remote value is the one your function will use when it is hosted in Azure. Copy the values from the *Local* to the *Remote* boxes for the **TwilioAccountSid** and **TwilioAuthToken** values.

    ![Setting the Twilio credentials in the application settings](../media/8-set-creds-in-app-settings.png)

1. Click **OK**. This will publish the values to the Azure Functions app.

## Pointing the mobile app to Azure

1. From the Publish tab, copy the **Site URL** using the **Copy to clipboard** button next to the value.

    ![Copy the site URL from the publish tab](../media/8-copy-site-url.png)

1. Open the `MainViewModel` from the `ImHere` project.

1. Update the value of the `baseUrl` field to be the site URL copied from the Publish tab.

## Test it out

1. Set the `ImHere.UWP` app as the startup app and run it.

1. Enter a phone number and click the **Send Location** button.

1. You should receive the location as an SMS message.

1. If you get back a *Service Unavailable* error, check what version of the "Microsoft.Azure.WebJobs.Extensions.Twilio" NuGet package your functions app is using, it should be 3.0.0-rc1, NOT 3.0.0.
1. If you get back a *Service Unavailable* error, check what version of the "Microsoft.Azure.WebJobs.Extensions.Twilio" NuGet package your functions app is using, it should be 3.0.0-rc1, **NOT** 3.0.0.

## Summary

In this unit, you learned how to publish an Azure Functions project to Azure from inside Visual Studio and configure application settings.