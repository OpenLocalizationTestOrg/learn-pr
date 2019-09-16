In your first week on your new job, you've  looked through the existing resources in your company's Azure subscription. There are a number of resource groups that contain many different resources, but they aren't organized into a coherent structure. You've worked on Azure before, but aren't entirely sure how resource groups work and what their role is. You've guessed (correctly) that they can play a role in how you organize your resources. Let's look at what they are, and how they can be used.

[!INCLUDE [azure-free-trial-note](../../../includes/azure-free-trial-note.md)]

## What are resource groups?

Resource groups are a fundamental element of the Azure platform. A resource group is a logical container for resources deployed on Azure. These resources are anything you create in an Azure subscription like virtual machines, Application Gateways, and CosmosDB instances. All resources must be in a resource group and a resource can only be a member of a single resource group. Many resources can be moved between resource groups with some services having specific limitations or requirements to move. Resource groups can't be nested. Before any resource can be provisioned, you need a resource group for it to be placed in.

### Logical grouping

Resource groups exist to help manage and organize your Azure resources. By placing resources of similar usage, type, or location, you can provide some order and organization to resources you create in Azure. Logical grouping is the aspect that we're most interested in here, since there's a lot of disorder among our resources.

![Conceptual image showing a resource group box with a Function, VM, database, and app included](../media/2-rg.PNG)

### Life cycle

If you delete a resource group, all resources contained within are also deleted. Organizing resources by life cycle can be useful in non-production environments, where you might try an experiment, but then dispose of it when done. Resource groups make it easy to remove a set of resources at once.

### Authorization

Resource groups are also a scope for applying role-based access control (RBAC) permissions. By applying RBAC permissions to a resource group, you can ease administration and limit access to allow only what is needed.

## Create a Resource Group

Resource groups can be created by using the following methods:

- Azure portal
- Azure PowerShell
- Azure CLI
- Templates
- Azure SDKs (like .NET, Java)

Let's walk through the steps you'd take to create a resource group in the Azure portal. If you'd like to follow along in your own subscription, you may.

1. Open a web browser and sign into the [Azure portal](https://portal.azure.com/?azure-portal=true).

    > [!IMPORTANT]
    > Make sure to use your _own_ subscription. When you are in the free sandbox environment, it will not allow you to create resource groups. You can tell which subscription you are using by looking at the tenant name under your profile picture. You can switch tenants by clicking on your profile picture and selecting **Switch Directory** from the options menu.

1. On the left pane, select **+ Create a resource**

1. Type **Resource Group** in the search box and hit Enter.

1. The first item in the list should be the resource group resource. Select it and then click the **Create** button.

    ![Search results searching the marketplace for a new resource group](../media/2-create-search-resource-group.png)

1. Enter your resource group name, let's use **msftlearn-core-infrastructure-rg**. Select the subscription it should be in, and select the region for the resource group. Click **Review + Create** and then **Create** to create the resource group.

    ![Fill in the required fields to create the resource group](../media/2-create-resource-group.png)

That's it, you've created a resource group that you can now use when you deploy Azure resources. Let's take a closer look at this resource group and some important things to consider.

## Explore a resource group and add a resource

In the portal, select **Resource groups** on the left menu, and select your newly created resource group. Note that you may also see a resource group called **NetworkWatcherRG**. You can ignore this resource group, it's created automatically to enable Network Watcher in Azure virtual networks.

![Overview panel of resource group](../media/2-rg-overview.png)

On the Overview panel, there's the basic information about the resource group like the subscription it's in, the subscription ID, any tags that are applied, and a history of the deployments to this resource group. We'll cover tags in the next unit. The deployments link takes you to a new panel with the history of all deployments to this resource group. Anytime you create a resource, it's a deployment, and you see that history for the resource group here.

Across the top you can add more resources, change the columns in the list, move the resource group to another subscription, or delete it entirely.

On the left menu, there are a number of options

We don't have any resources in this resource group yet, so the list at the bottom is empty. Let's create a couple resources inside the resource group.

1. Click **+ Add** at the top or click the **Create resources**, either will work.

1. Search for **Virtual network**. The first result should be the virtual network resource. Click it, and on the next screen, make sure **Select a deployment model** is set to **Resource Manager**. Click **Create**.

1. Name the virtual network **msftlearn-vnet1**. For the **Resource group** drop-down, select the resource group that you created earlier. Enter **192.168.0.0/24** for both the **Address space** and subnet **Address range**. Leave the defaults for all other options, and click **Create**.

1. Repeat the steps again to create one more VNet, where both the **Address space** and subnet **Address range** are for a different network than your previous network, (e.g. **192.168.100.0/24**). Name it **msftlearn-vnet2**, and make sure to place it in the resource group that you created earlier.

1. Go back to your resource group, and on the **Overview** panel you should see the two VNets you created.

    ![Overview panel of resource group showing the VNet](../media/2-rg-with-vnet.png)

Our resource group now contains two virtual network resources because we specified in our deployment (when we created the resources) which resource group we wanted the VNet to be placed in. We could create additional resources inside this resource group, or we could create additional resource groups in the subscription to deploy resources into.

When creating resources, you usually have the option to create a new resource group as an alternative to using an existing resource group. This simplifies the process a bit, but as you see in your new organization, can lead to resources spread across resource groups with little thought as to how to organize them.

## Use resource groups for organization

So how can you use resource groups to your advantage in your new organization? There are some guidelines and best practices that can help with the organization.

### Consistent naming convention

You can start with using an understandable naming convention. We named our resource group **msftlearn-core-infrastructure-rg**. We've given some indication of what it's used for (**msftlearn**), the types of resources contained within (**core-infrastructure**), and the type of resource it is itself (**rg**). This descriptive name gives us a better idea of what it is. If we had named it **my-resource-group** or **rg1**, we have no idea on a glance of what the usage may be. In this case, we can deduce that there are probably core pieces of infrastructure contained within. If we created additional VNets, storage accounts, or other resources the company may consider _core infrastructure_, we could place them here as well, to improve the organization of our resources. Naming conventions can vary widely between and even within companies, but some planning can help.

### Organizing principles

Resource groups can be organized in a number of ways, let's take a look at a few examples. We might put all resources that are _core infrastructure_ into this resource group. But we could also organize them strictly by resource type. For example, put all VNets in one resource group, all virtual machines in another resource group, and all Azure Cosmos DB instances in yet another resource group.

![Image of resources organized by type](../media/2-resource-type-rg.png)

We could organize them by environment (prod, qa, dev). In this case, all production resources are in one resource group, all test resources are in another resource group, and so on.

![Image of resources organized by environment](../media/2-environment-rg.png)

We could organize them by department (marketing, finance, human resources). Marketing resources go in one resource group, finance in another resource group, and HR in a third resource group.

![Image of resources organized by department](../media/2-department-rg.png)

We could even use a combination of these strategies and organize by environment and department. Put production finance resources in one resource group, dev finance resources in another, and the same for the marketing resources.

![Image of resources organized by environment and department](../media/2-env-dept-rg.png)

There are a few factors that can play into the strategy you use to organize resources: authorization, resource life cycle, and billing.

#### Organizing for authorization

Since resource groups are a scope of RBAC, you can organize resources by _who_ needs to administer them. If your database administration team is responsible for managing all of your Azure SQL Database instances, putting them in the same resource group would simplify administration. You could give them the proper permissions at the resource group level to administer the databases within the resource group. Similarly, the database administration team could be denied access to the resource group with virtual networks, so they don't inadvertently make changes to resources outside the scope of their responsibility.

#### Organizing for life cycle

We mentioned earlier that resource groups serve as the life cycle for the resources within it. If you delete a resource group, you delete all the resources in it. Use this to your advantage, especially in areas where resources are more disposable, like non-production environments. If you deploy 10 servers for a project that you know will only last a couple of months, you might put them all in a single resource group. One resource group is easier to clean up than 10 or more resource groups.

#### Organizing for billing

Lastly, placing resources in the same resource group is a way to group them for usage in billing reports. If you're trying to understand how your costs are distributed in your Azure environment, grouping them by resource group is one way to filter and sort the data to better understand where costs are allocated.

## Summary

The bottom line is that you have flexibility in how to organize resources in your resource groups. Put some thought into it so that you have a coherent approach to how you use resource groups in your Azure environment.
