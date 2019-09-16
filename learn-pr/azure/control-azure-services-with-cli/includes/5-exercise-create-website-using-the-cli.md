Next, let's use the Azure CLI to create a resource group, and then to deploy a web app into this resource group.

[!include[](../../../includes/azure-sandbox-activate.md)]

### Using a resource group

When you're working with your own machine and Azure subscription, you'll need to first sign in to Azure using the `az login` command. However, signing in is unnecessary when you are using the browser-based Cloud Shell environment.

Next, you would normally create a resource group for all your related Azure resources with an `az group create` command, but for this exercise the following resource group has been created for you: **<rgn>[sandbox resource group name]</rgn>**.

1. Your first step in this exercise will be to create several variables that you will use in later commands.

   ```bash
   export RESOURCE_GROUP=<rgn>[sandbox resource group name]</rgn>
   export AZURE_REGION=[region]
   export AZURE_APP_PLAN=popupappplan-$RANDOM
   export AZURE_WEB_APP=popupwebapp-$RANDOM
   ```

   For the `[region]` parameter, use one of the regions from the following list.

   [!include[](../../../includes/azure-sandbox-regions-first-mention-note.md)]

1. You can ask the Azure CLI to list all your resource groups in a table. There should just be one while you are in the free Azure sandbox.

   ```azurecli
   az group list --output table
   ```

   [!include[](../../../includes/azure-cloudshell-copy-paste-tip.md)]

1. As you do more Azure development, you can end up with several resource groups. If you have several items in the group list, you can filter the return values by adding a `--query` option. Try the following command:

   ```azurecli
   az group list --query "[?name == '$RESOURCE_GROUP']"
   ```

   The query is formatted using **JMESPath**, which is a standard query language for JSON requests. You can learn more about this powerful filter language at <http://jmespath.org/>. We also cover queries in more depth in the **Manage VMs with the Azure CLI** module.

### Steps to create a service plan

When you run Web Apps using the Azure App Service, you pay for the Azure compute resources that are used by the app, and the resource costs depend on the App Service plan associated with your Web Apps. Service plans determine the region used for the app datacenter, number of VMs used, and pricing tier.

1. Create an App Service plan to run your app. The following command specifies the free pricing tier, but you can run `az appservice plan create --help` to see the other pricing tiers.

   > [!NOTE]
   > The name of the app and plan must be _unique_ in all of Azure. The variables that you created earlier will assign random values as suffixes to make sure they're unique. However, if you receive an error when you are creating any resources, you should run the commands listed earlier to reset all of the variables with new random values.

   ```azurecli
   az appservice plan create --name $AZURE_APP_PLAN --resource-group $RESOURCE_GROUP --location $AZURE_REGION --sku FREE
   ```

   This command can take several minutes to complete.

1. Verify that the service plan was created successfully by listing all your plans in a table.

   ```azurecli
   az appservice plan list --output table
   ```

   You'll see a response like the following example.

   ```output
   Kind    Location    MaximumNumberOfWorkers    Name                NumberOfSites    ResourceGroup                               Status
   ------  ----------  ------------------------  ------------------  ---------------  ------------------------------------------  --------
   app     Central US  3                         popupappplan-54321  0                Learn-12345678-1234-1234-1234-123456789abc  Ready
   ```

### Steps to create a web app

Next, you'll create the web app in your service plan. You can deploy the code at the same time, but for our example, we'll create the web app and deploy the code as separate steps.

1. To create the web app, you'll supply web app name and the name of the app plan you created above. Just like the app plan name, the web app name must be unique, and the variables that you created earlier will assign random values that should be sufficient for this exercise.

   ```azurecli
   az webapp create --name $AZURE_WEB_APP --resource-group $RESOURCE_GROUP --plan $AZURE_APP_PLAN
   ```

1. Verify that the app was created successfully by listing all your apps in a table.

   ```azurecli
   az webapp list --output table
   ```

   You'll see a response like the following example.

   ```output
   Name               Location    State    ResourceGroup                               DefaultHostName                      AppServicePlan
   -----------------  ----------  -------  ------------------------------------------  -----------------------------------  ------------------
   popupwebapp-12345  Central US  Running  Learn-12345678-1234-1234-1234-123456789abc  popupwebapp-12345.azurewebsites.net  popupappplan-54321
   ```

   Make a note of the **DefaultHostName** listed in the table; this address is the URL for the new website. Azure will make your website available through the unique app name in the `azurewebsites.net` domain. For example, if my app name was "popupwebapp-mslearn123", then my website URL would be: `http://popupwebapp-mslearn123.azurewebsites.net`.

1. Your site has a "quickstart" page created by Azure that you can see either in a browser, or with CURL, just use the **DefaultHostName**:

   ```bash
   curl $AZURE_WEB_APP.azurewebsites.net
   ```

   You'll see the default HTML for the sample app returned.
    
### Steps to deploy code from GitHub

1. The final step is to deploy code from a GitHub repository to the web app. Let's use a simple PHP page available in the Azure Samples GitHub repository that displays "Hello World!" when it executes. Make sure to use the web app name you created.

   ```azurecli
   az webapp deployment source config --name $AZURE_WEB_APP --resource-group $RESOURCE_GROUP --repo-url "https://github.com/Azure-Samples/php-docs-hello-world" --branch master --manual-integration
   ```

1. Once it's deployed, hit your site again with a browser or CURL.

   ```bash
   curl $AZURE_WEB_APP.azurewebsites.net
   ```
    
   The page displays "Hello World!"

   ```output
   Hello World!
   ```

This exercise demonstrated a typical pattern for an interactive Azure CLI session. You first used a standard command to create a new resource group. You then used a set of commands to deploy a resource (in this example, a web app) into this resource group. This set of commands could easily be combined into a shell script, and executed every time you need to create the same resource.
