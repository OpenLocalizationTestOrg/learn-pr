Just like your on-premises equipment costs, there are several elements that will affect your monthly costs when using Azure services. Let's look at a few of the primary factors including resource type, services, the user's location, and the billing zone.

### Resource type
Costs are resource-specific, so the usage that a meter tracks and the number of meters associated with a resource depend on the resource type.

> [!NOTE] 
> Each meter tracks a *particular kind of usage*.  For example, a meter might track bandwidth usage (ingress or egress network traffic in bits-per-second), the number of operations, size (storage capacity in bytes), or similar items.

The usage that a meter tracks correlates to a number of billable units. Those are charged to your account for each billing period, and the rate per billable unit depends on the resource type you are using.

### Services

:::row:::
  :::column span="3":::
Azure usage rates and billing periods can differ between Enterprise, Web Direct, and Cloud Solution Provider (CSP) customers. Some subscription types also include usage allowances, which affect costs.

The Azure team develops and offers first-party products and services, while products and services from third-party vendors are available in the [Azure Marketplace](https://azuremarketplace.microsoft.com?azure-portal=true). Different billing structures apply to each of these categories.
   :::column-end:::
   :::column:::

![Image depicting a billing period, with a calendar, computer, and meter linked to illustrate the correlation between the three](../media/2b-billing-period-graphic.png)

   :::column-end:::
:::row-end:::

### Location
Azure has datacenters all over the world. Usage costs vary between locations that offer particular Azure products, services, and resources based on popularity, demand, and local infrastructure costs.

For example, you might want to build your Azure solution by provisioning resources in locations that offer the lowest prices, but this would require transferring data between locations if dependent resources and their users are located in different parts of the world. If there are meters tracking the volume of data that moves between the resources you provision, any potential savings you make from choosing the cheapest location could be offset by the additional cost of transferring data between those resources.

### Azure billing zones
Bandwidth refers to data moving in and out of Azure datacenters. Most of the time inbound data transfers (data going _into_ Azure datacenters) are free. For outbound data transfers (data going _out_ of Azure datacenters), the data transfer pricing is based on **Billing Zones**.

![Image that shows internet traffic traveling between two datacenters around a globe](../media/1b-azure-regions-globe.png)

A **Zone** is a geographical grouping of Azure Regions for billing purposes. The following zones exist and include the listed countries (regions) listed.

| Zone | Areas |
|------|---------|
| Zone 1 | United States, Europe, Canada, UK, France | 
| Zone 2 | Asia Pacific, Japan, Australia, India, Korea |
| Zone 3 | Brazil |
| DE Zone 1 | Germany |

In most zones, the first outbound 5 GB per month is free. After that, you are billed a fixed price per GB.

> [!NOTE] 
> Billing zones aren't the same as an _Availability Zone_. In Azure, the term *zone* is for billing purposes only, and the full term *Availability Zone* refers to the failure protection that Azure provides for datacenters.