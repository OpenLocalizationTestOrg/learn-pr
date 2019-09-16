The **AzCopy** command is a command-line utility specially created and optimized for moving data in and out of Azure storage. Using AzCopy, you can bulk-transfer from the local file system on an on-premises computer to the cloud. 

In this unit, you'll learn how to use the AzCopy utility to upload, download, and transfer blob data between storage accounts.

## What is AzCopy?

The AzCopy utility is designed and optimized for performing large-scale bulk transfers of data to and from Azure storage. You use AzCopy to transfer entire directory structures from your local computer into blob storage, and back again.

AzCopy performs its operations asynchronously, and can use multiple concurrent threads to read and write data. Additionally, it's fault-tolerant. If a transfer is interrupted for some reason and fails, AzCopy can resume the operation from where it left off, once the situation is resolved.

The latest version of AzCopy enables you to take incremental backups of blobs, and can keep the contents of blob storage accounts synchronized so that they contain the same version of data.

## Upload data

Use the `AzCopy copy` command to upload data to Azure storage. The simplest form of this command copies a file to a blob. The example below uploads the contents of a file named *myfile.txt* to a blob, also named *myfile.txt*, to the *mycontainer* container in the *myaccount* blob storage account. The storage account and container must already exist.

```bash
azcopy copy myfile.txt https://myaccount.blob.core.windows.net/mycontainer/
```

The AzCopy command requires you are authenticated and authorized to access the storage account. If your organization uses ADFS, you can authenticate by using the `azcopy login` command and signing in. Alternatively, the owner of a storage account can create a Shared Access Signature (SAS) token that provides access to the storage account. The SAS token is time-limited, and can be restricted to only allowing certain operations. You provide the SAS token as a query string to the Azure storage URL when you upload or download data:

```bash
azcopy copy myfile.txt https://myaccount.blob.core.windows.net/mycontainer/?<sas token>
```

You can upload multiple files and complete folder structures with AzCopy. The `--recursive=true` option will walk the folder structure starting at the specified point, and will create a corresponding structure of blobs in your container in Azure storage. You can also specify that only files whose names match a particular pattern are uploaded, by using wildcard pattern matching. This example uploads all files  under the *myfolder* folder and subfolders:

```bash
azcopy copy myfolder https://myaccount.blob.core.windows.net/mycontainer/?<sas token> --recursive=true
```

## Monitor and manage jobs

Remember that AzCopy runs asynchronously. If you are transferring a large number of files, or even a small number of large files, AzCopy will start your upload, and will report an initial summary. The command will then continue running in the background. You can use the `AzCopy jobs list` command to view the status of running and recently completed commands. Each job has an ID, and you can view the details of a job with the `AzCopy jobs show <id>` command.

If an AzCopy transfer is interrupted, it will appear in the jobs list. You can restart the job with the `azcopy jobs resume <id>` command.

## Download data

The `AzCopy copy` command can also download data from blob storage. The next example transfers the  data in the blob *myblob* to a local folder named *myblobdata*:

```bash
azcopy copy https://myaccount.blob.core.windows.net/mycontainer/myblob?<sas token> myblobdata
```

As with uploads, you can use wildcards to download multiple blobs with matching names, and iterate through the blob container hierarchy with the `--recursive=true` flag.

## Copy data between storage accounts, and synchronize storage

The final use of the `AzCopy copy` command is to transfer blobs between storage accounts. This example copies the blobs from *sourcecontainer* in the *sourceaccount* storage account to *destcontainer* in *destaccount*. This example requires two SAS tokens; one for the source account and another for the destination account.

```bash
azcopy copy https://sourceaccount.blob.core.windows.net/sourcecontainer/*?<source sas token> https://destaccount.blob.core.windows.net/destcontainer/*?<dest sas token>
```

The `--recursive=true` flag is available if your container has a hierarchical set of blobs.

When the source and destination of the AzCopy command are both Azure storage accounts, the command performs the transfer using the Azure storage service. It doesn't download and upload blobs via your computer.

Another way to copy blobs between storage accounts is to use the `AzCopy sync` command. This command synchronizes the contents of a destination container with a source container, copying blobs if they aren't found in the destination, or if the last modified time of a blob in the destination is earlier than that of the corresponding blob in the source. This command also provides the `--delete-destination` flag. If you set this flag to true, AzCopy will delete blobs in the destination that don't exist in the source; use this option with caution.

## Manage blobs

You can view the blobs available in a container with the `AzCopy list` command. The following example displays the blobs in the *sourcecontainer* container:

```bash
./azcopy list https://sourceaccount.blob.core.windows.net/sourcecontainer?<sas token>
```

You can create a new container in a blob storage account using the `AzCopy make` command, like this:

```bash
./azcopy make https://myaccount.blob.core.windows.net/newcontainer?<sas token>
```

To remove a blob, run the `AzCopy remove` command. You can remove all blobs with names that match a specific pattern with the `--include` flag, and you can walk through a hierarchical container with the `--recursive` flag. The next example removes all blobs with the *txt* suffix from *mycontainer*.

```bash
./azcopy remove https://myaccount.blob.core.windows.net/mycontainer?<sas token> --include "*.txt" --recursive=true
```

## Configure concurrency

You can control the performance and resource utilization of the AzCopy command by setting the *AZCOPY_CONCURRENCY_VALUE* environment variable. AzCopy uses the value of this variable to specify the number of concurrent threads it will utilize for transferring data. By default, it's set to 300. You can reduce this value if you need to control the bandwidth and CPU utilization of your copy operations.