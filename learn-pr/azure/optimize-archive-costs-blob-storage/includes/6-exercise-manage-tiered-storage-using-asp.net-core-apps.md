You're now going to test out some sample .NET code to retrieve and modify tier levels for some example Azure Storage blobs. You'll create a new BlobStorage account, and set the account to Cool. Then you'll upload some data, and then use the app to modify the storage tiers.

## Download the code and sample data

In this first step, you'll download source code and sample data to your Cloud Shell storage.

1. Run the following commands in the Cloud Shell to download source code and sample data to the storageapp folder in your Cloud Shell storage:

   ```azurecli
   git clone https://github.com/MicrosoftDocs/mslearn-optimize-blob-storage-costs storageapp
   cd storageapp
   ```

## Use the CLI to create a new blob storage account

You'll now create a new BlobStorage account, and retrieve the account storage key.

1. Run the following commands in the Cloud Shell to create a new BlobStorage account set to Cool:

   ```azurecli
   export STORAGE_GROUP=`az group list --query [0].name | tr -d \"`
   
   export AZURE_STORAGE_ACCOUNT=<your name or initials>storageaccount
   
   az storage account create --resource-group $STORAGE_GROUP --name $AZURE_STORAGE_ACCOUNT --kind BlobStorage --access-tier Cool
   ```

1. Run the following command in the Cloud Shell to retrieve the storage key for the account:

   ```azurecli
   export AZURE_STORAGE_KEY=`az storage account keys list --account-name $AZURE_STORAGE_ACCOUNT --query [0].value | tr -d \"`
   ```

## Create container and upload data

In this step, you'll create a blob container and three blobs, each containing some sample data.

1. Run the following command in the Cloud Shell to create a new container:

   ```azurecli
   az storage container create --name blobcontainer
   ```

1. Run the following commands in the Cloud Shell to blobs with sample data:

   ```azurecli
   cd ManageStorageTiers
   az storage blob upload --file testdata.txt --container-name blobcontainer --name blob1
   az storage blob upload --file testdata.txt --container-name blobcontainer --name blob2
   az storage blob upload --file testdata.txt --container-name blobcontainer --name blob3
   ```

## Set the tier for each blob

In this step, you'll set the access tier for each blob.

1. Run the following command in the Cloud Shell to set blob1 as Archive:

   ```azurecli
   az storage blob set-tier --container-name blobcontainer --name blob1 --tier Archive
   ```

1. Run the following command in the Cloud Shell to set blob2 as Cool:

   ```azurecli
   az storage blob set-tier --container-name blobcontainer --name blob2 --tier Cool
   ```

1. Run the following command in the Cloud Shell to set blob3 as Hot:

   ```azurecli
   az storage blob set-tier --container-name blobcontainer --name blob3 --tier Hot
   ```

## Manage storage tiers in code

You're now ready to deploy and test out some code. First you'll set the environment variables required by the sample app. You'll then build and run the sample app (ignore the warnings about the Microsoft.Azure.KeyVault.Core package).

1. Run the following commands in the Cloud Shell to store the environment variables used by the app:

   ```bash
   export CONNECTION_STRING=`az storage account show-connection-string --name $AZURE_STORAGE_ACCOUNT --query connectionString | tr -d \"`
   
   export CONTAINER_NAME=blobcontainer
   ```

1. Run the following command in the Cloud Shell to build and run the **ManageStorageTiers** app:

   ```azurecli
   cd ManageStorageTiers
   dotnet build
   dotnet run
   ```

1. The ManageStorageTiers app now connects to your blob storage, queries the three blobs named blob1, blob2, and blob3. It will then changes the storage tier of all three blobs, and query the storage tier of each blob again to verify the change. The Archive tier will not have changed, due to rehydration latency.

## Review the app code

<!-- REVIEW: I could not check this part since the sample repro has not been set up -->

In this final step, you'll take a look at the code used by the ManageStorageTiers app to manage and change access tiers.

1. In the Cloud Shell, use the editor to open ManageStorageTiers\Program.cs.

1. The key parts of the app are the lines of code that look like this:

   ```csharp
   CloudBlockBlob blob1 = cloudBlobContainer.GetBlockBlobReference("blob1");
   blob1.SetStandardBlobTier(StandardBlobTier.Cool);
   ```

1. This code sets the storage tier for a block blob.

1. The code in the **DisplayBlobTiers** method retrieves the storage tier for blobs. The important line is:

   ```csharp
   Console.WriteLine($"Blob name {item.Name}: Tier {item.Properties.StandardBlobTier}");
   ```

<!-- REVIEW: Shouldn't this syntax be Console.WriteLine($"Blob name {0}: Tier {1}", item.Name, item.Properties.StandardBlobTier); -->

1. This code obtains the storage tier from the Properties collection of the blob.
