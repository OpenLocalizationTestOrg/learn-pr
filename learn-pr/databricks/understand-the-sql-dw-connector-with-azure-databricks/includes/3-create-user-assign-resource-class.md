To add a SQL Server authenticated user to SQL Data Warehouse, you need to complete these steps:

1. Create a server login.
1. Add a user to the master database by using the new server login.
1. Add a user to SQL Data Warehouse by using the new server login.

Let's start by creating the server login that we'll use for the exercises in this module.

## Add a user to the master database

1. In Azure Data Studio, make sure that you are still connected to the master database located under the SQL Server instance you created in the previous unit (in the section "Create new connections in Azure Data Studio").

1. Go to the **Servers** list in the menu on the left side of Azure Data Studio. Under the SQL Data Warehouse connection you made to the master database, expand **Databases** and **System Databases**. Right-click **master** and select **New Query**.

    ![Right-click master and select New Query](../media/azure-data-studio-new-query-master.png)

1. Check whether the **master** database is selected for the query window.

1. Create a new server login by running this statement in the window:

    ```sql
    CREATE LOGIN LabUserLogin WITH PASSWORD = 'Str0ng_password';
    ```

1. Select the **Run** button to execute the query. You should see a message telling you that the commands completed successfully.

    ![Execute the query](../media/azure-data-studio-new-query.png)

1. Create a user in the master database by using the login you created in step 4:

    ```sql
    CREATE USER LabUser FOR LOGIN LabUserLogin;
    ```

## Add a user to SQL Data Warehouse

1. Go to the Azure portal and select your data warehouse from the resource group that you created for this module.

    Use these values to make the connection to the SQL Data Warehouse database:

    - *SQL Server:* The SQL Server instance that you created for this module.
    - *Authentication:* **SQL Login**
    - *User name:* **Server Admin**
    - *Database:* The SQL Data Warehouse database that you created for this module.

1. Go to the **Servers** list in the menu on the left side of Azure Data Studio. Right-click the connection you made to the SQL Data Warehouse database and select **New Query**.

    ![Right-click the SQL Data Warehouse connection and select New Query](../media/azure-data-studio-new-query-dw.png)

1. Check whether **SQL Data Warehouse** (`sql-dw-exercise`) is selected for the query window.

1. To create a user in SQL Data Warehouse using the login you created earlier, run this statement:

    ```sql
    CREATE USER LabUser FOR LOGIN LabUserLogin;
    ```

1. Run this statement to allow LabUser to read data from SQL Data Warehouse:

    ```sql
    EXEC sp_addrolemember 'db_datareader', 'LabUser';
    ```

## Assign a resource class

Resource classes manage resource allocation for query execution in SQL Data Warehouse. They help you control memory allocation, CPU cycles, and concurrency slots given to each query.

[Learn more about resource classes.](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-concurrency#resource-classes)

To assign a resource class to the user added to SQL Data Warehouse, run this statement in same query window that you used for the previous step:

```sql
EXEC sp_addrolemember 'largerc', 'LabUser';
```

This statement assigns the `'largerc'` resource class to the newly added user.
