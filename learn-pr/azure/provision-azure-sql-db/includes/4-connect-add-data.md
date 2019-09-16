Before you connect the database to your app, you want to check that you can connect to it, add a basic table, and work with sample data.

We maintain the infrastructure, software updates, and patches for your Azure SQL database. Beyond that, you can treat your Azure SQL database like you would any other SQL Server installation. For example, you can use Visual Studio, SQL Server Management Studio, Azure Data Studio, or other tools to manage your Azure SQL database.

How you access your database and connect it to your app is up to you. But to get some experience working with your database, here you'll connect to it directly from the portal, create a table, and run a few basic CRUD operations. You'll learn:

- What Cloud Shell is and how to access it from the portal.
- How to access information about your database from the Azure CLI, including connection strings.
- How to connect to your database using `sqlcmd`.
- How to initialize your database with a basic table and some sample data.

## What is Azure Cloud Shell?

Azure Cloud Shell is a browser-based shell experience to manage and develop Azure resources. Think of Cloud Shell as an interactive console that runs in the cloud.

Behind the scenes, Cloud Shell runs on Linux. But depending on whether you prefer a Linux or Windows environment, you have two experiences to choose from: Bash and PowerShell.

Cloud Shell is accessible from anywhere. Besides the portal, you can also access Cloud Shell from [shell.azure.com](https://shell.azure.com/), the Azure mobile app, or from Visual Studio Code. The panel on the right is a Cloud Shell terminal for you to use during this exercise.

Cloud Shell includes popular tools and text editors. Here's a brief look at `az`, `jq`, and `sqlcmd`, three tools you'll use for our current task.

- `az` is also known as the Azure CLI. It's the command-line interface for working with Azure resources. You'll use this to get information about your database, including the connection string.
- `jq` is a command-line JSON parser. You'll pipe output from `az` commands to this tool to extract important fields from JSON output.
- `sqlcmd` enables you to execute statements on SQL Server. You'll use `sqlcmd` to create an interactive session with your Azure SQL database.

## Get information about your Azure SQL database

Before you connect to your database, it's a good idea to verify it exists and is online.

Here, you use the `az` utility to list your databases and show some information about the **Logistics** database, including its maximum size and status.

1. The `az` commands you'll run require the name of your resource group and the name of your Azure SQL logical server. To save typing, run this `azure configure` command to specify them as default values.
    Replace `<server-name>` with the name of your Azure SQL logical server. Note that depending on the blade you are on in the portal this may show as a FQDN (servername.database.windows.net), but you only need the logical name without the .database.windows.net suffix.

    ```azurecli
    az configure --defaults group=<rgn>[sandbox resource group name]</rgn> sql-server=<server-name>
    ```

1. Run `az sql db list` to list all databases on your Azure SQL logical server.

    ```azurecli
    az sql db list
    ```
    You see a large block of JSON as output.

1. Since we want just the database names, run the command a second time. This time, pipe the output to `jq` to print out only the name fields.
   
     ```azurecli
    az sql db list | jq '[.[] | {name: .name}]'
    ```
    
    You should see this output.
    
    ```json
    [
      {
        "name": "Logistics"
      },
      {
        "name": "master"
      }
    ]
    ```

    **Logistics** is your database. Like SQL Server, **master** includes server metadata, such as sign-in accounts and system configuration settings.

1. Run this `az sql db show` command to get details about the **Logistics** database.

    ```azurecli
    az sql db show --name Logistics
    ```

    As before, you see a large block of JSON as output.

1. Run the command a second time. This time, pipe the output to `jq` to limit output to only the name, maximum size, and status of the **Logistics** database.

    ```azurecli
    az sql db show --name Logistics | jq '{name: .name, maxSizeBytes: .maxSizeBytes, status: .status}'
    ```

    You see that the database is online and can hold around 2 GB of data.

    ```json
    {
      "name": "Logistics",
      "maxSizeBytes": 2147483648,
      "status": "Online"
    }
    ```

## Connect to your database

Now that you understand a bit about your database, let's connect to it using `sqlcmd`, create a table that holds information about transportation drivers, and perform a few basic CRUD operations.

Remember that CRUD stands for **create**, **read**, **update**, and **delete**. These terms refer to operations you perform on table data and are the four basic operations you need for your app. Now's a good time to verify you can perform each of them.

1. Run this `az sql db show-connection-string` command to get the connection string to the **Logistics** database in a format that `sqlcmd` can use.

    ```azurecli
    az sql db show-connection-string --client sqlcmd --name Logistics
    ```

    Your output resembles this.

    ```output
    "sqlcmd -S tcp:contoso-1.database.windows.net,1433 -d Logistics -U <username> -P <password> -N -l 30"
    ```

1. Run the `sqlcmd` statement from the output of the previous step to create an interactive session. Remove the surrounding quotes and replace `<username>` and `<password>` with the username and password you specified when you created your database. Here's an example.

    ```console
    sqlcmd -S tcp:contoso-1.database.windows.net,1433 -d Logistics -U martina -P "password1234$" -N -l 30
    ```

    > [!TIP]
    > Place your password in quotes so that "&" and other special characters aren't interpreted as processing instructions.

1. From your `sqlcmd` session, create a table named `Drivers`.

    ```sql
    CREATE TABLE Drivers (DriverID int, LastName varchar(255), FirstName varchar(255), OriginCity varchar(255));
    GO
    ```

    The table contains four columns: a unique identifier, the driver's last and first name, and the driver's city of origin.

    > [!NOTE]
    > The language you see here is Transact-SQL, or T-SQL.

1. Verify that the `Drivers` table exists.

    ```sql
    SELECT name FROM sys.tables;
    GO
    ```

    You should see this output.

    ```output
    name
    --------------------------------------------------------------------------------------------------------------------------------
    Drivers

    (1 rows affected)
    ```

1. Run this `INSERT` T-SQL statement to add a sample row to the table. This is the **create** operation.

    ```sql
    INSERT INTO Drivers (DriverID, LastName, FirstName, OriginCity) VALUES (123, 'Zirne', 'Laura', 'Springfield');
    GO
    ```

    You see this to indicate the operation succeeded.

    ```output
    (1 rows affected)
    ```

1. Run this `SELECT` T-SQL statement to list the `DriverID` and `OriginCity` columns from all rows in the table. This is the **read** operation.

    ```sql
    SELECT DriverID, OriginCity FROM Drivers;
    GO
    ```

    You see one result with the `DriverID` and `OriginCity` for the row you created in the previous step.

    ```output
    DriverID    OriginCity
    ----------- --------------------------
            123 Springfield

    (1 rows affected)
    ```

1. Run this `UPDATE` T-SQL statement to change the city of origin from "Springfield" to "Boston" for the driver with a `DriverID` of 123. This is the **update** operation.

    ```sql
    UPDATE Drivers SET OriginCity='Boston' WHERE DriverID=123;
    GO
    SELECT DriverID, OriginCity FROM Drivers;
    GO
    ```

    You should see the following output. Notice how the `OriginCity` reflects the update to Boston.

    ```output
    DriverID    OriginCity
    ----------- --------------------------
            123 Boston

    (1 rows affected)
    ```

1. Run this `DELETE` T-SQL statement to delete the record. This is the **delete** operation.

    ```sql
    DELETE FROM Drivers WHERE DriverID=123;
    GO
    ```

    ```output
    (1 rows affected)
    ```

1. Run this `SELECT` T-SQL statement to verify the `Drivers` table is empty.

    ```sql
    SELECT COUNT(*) FROM Drivers;
    GO
    ```

    You see that the table contains no rows.

    ```output
    -----------
              0

    (1 rows affected)
    ```

Now that you have the hang of working with Azure SQL Database from Cloud Shell, you can get the connection string for your favorite SQL management tool &ndash; whether that's from SQL Server Management Studio, Visual Studio, or something else.

Cloud Shell makes it easy to access and work with your Azure resources. Because Cloud Shell is browser-based, you can access it from Windows, macOS, or Linux &ndash; essentially any system with a web browser.

You gained some hands-on experience running Azure CLI commands to get information about your Azure SQL database. As a bonus, you practiced your T-SQL skills.

Finally, let's wrap up and see how to tear down your database.
