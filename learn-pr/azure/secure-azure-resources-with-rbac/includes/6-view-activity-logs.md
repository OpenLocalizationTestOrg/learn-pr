First Up Consultants reviews role-based access control (RBAC) changes quarterly for auditing and troubleshooting purposes. You know that changes get logged in [Azure Activity Log](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). Your manager has asked if you can generate a report of the role assignment and custom role changes for the last month.

## View activity logs

The easiest way to get started is to view the activity logs with the Azure portal.

1. Click **All services** and then find **Activity log**.

    ![Screenshot of the Azure portal showing the location of Activity logs option](../media/6-all-services-activity-log.png)

1. Click **Activity log** to open the activity log.

    ![Screenshot of the Azure portal showing the Activity logs](../media/6-activity-log-portal.png)

1. Set the **Timespan** filter to **Last month**.

1. Add an **Operation** filter and type **role** to filter the list.

1. Select the following RBAC operations:

    - Create role assignment (roleAssignments)
    - Delete role assignment (roleAssignments)
    - Create or update custom role definition (roleDefinitions)
    - Delete custom role definition (roleDefinitions)

    ![Screenshot showing a list of Operation filter with the four filters selected](../media/6-operation-filter.png)

    After a few moments, you'll see all the role assignment and role definition operations for the last month. It also includes a link to download the activity log as a CSV file.

1. Click one of the operations to see the activity log details.

    ![Screenshot show the details for an activity log](../media/6-activity-log-details.png)

In this unit, you learned how to use Azure Activity Log to list RBAC changes in the portal and generate a simple report.