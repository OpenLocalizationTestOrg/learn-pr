In this unit, you will continue with the example of a company that makes Linux admin tools. Recall that you plan to use Linux VMs to let potential customers test your software. You have a resource group ready and now it is time to create the VMs.

Your company has paid for a booth at a big Linux trade show. You plan a demo area containing three terminals each connected to a separate Linux VM. At the end of each day, you want to delete the VMs and recreate them, so they start fresh every morning. Creating the VMs manually after work when you are tired would be error prone. You want to write a PowerShell script to automate the VM creation process.

## Write a script that creates Virtual Machines

Follow these steps in the Cloud Shell on the right to write the script:

1. Switch to your home folder in the Cloud Shell.

    ```powershell
    cd $HOME\clouddrive
    ```

1. Create a new text file named **ConferenceDailyReset.ps1**.

    ```powershell
    touch "./ConferenceDailyReset.ps1"
    ```

1. Open the integrated editor and select the **ConferenceDailyReset.ps1** file.

    ```powershell
    code "./ConferenceDailyReset.ps1"
    ```

    > [!TIP]
    > The integrated Cloud Shell also supports vim, nano, and emacs if you'd prefer to use one of those editors.

1. Start by capturing the input parameter in a variable. Add the following line to your script:

    ```powershell
    param([string]$resourceGroup)
    ```

    > [!NOTE]
    > Normally, you'd have to authenticate with Azure using your credentials using `Connect-AzAccount` and this could be done in the script. However, in the Cloud Shell environment you will already be authenticated so this is unnecessary.

1. Prompt for a username and password for the VM's admin account and capture the result in a variable:

    ```powershell
    $adminCredential = Get-Credential -Message "Enter a username and password for the VM administrator."
    ```

1. Create a loop that executes three times:

    ```powershell
    For ($i = 1; $i -le 3; $i++) 
    {

    }
    ```

1. In the loop body, create a name for each VM and store it in a variable and output it to the console:

    ```powershell
    $vmName = "ConferenceDemo" + $i
    Write-Host "Creating VM: " $vmName
    ```

1. Next, create a VM using the `$vmName` variable:

   ```powershell
   New-AzVm -ResourceGroupName $resourceGroup -Name $vmName -Credential $adminCredential -Image UbuntuLTS
   ```

1. Save the file. You can use the "..." menu at the top right corner of the editor. There are also common accelerator keys for Save.

The completed script should look like this:

```powershell
param([string]$resourceGroup)

$adminCredential = Get-Credential -Message "Enter a username and password for the VM administrator."

For ($i = 1; $i -le 3; $i++)
{
    $vmName = "ConferenceDemo" + $i
    Write-Host "Creating VM: " $vmName
    New-AzVm -ResourceGroupName $resourceGroup -Name $vmName -Credential $adminCredential -Image UbuntuLTS
}
```

## Execute the script

Close the editor through the "..." context menu.

1. Execute the script.

    ```powershell
    .\ConferenceDailyReset.ps1 <rgn>[sandbox resource group name]</rgn>
    ```

The script will take several minutes to complete. When it is finished, verify that it ran successfully by looking at the resources you now have in your resource group:

```powershell
Get-AzResource -ResourceType Microsoft.Compute/virtualMachines
```

You should see three VMs, each with a unique name.

You wrote a script that automated the creation of three VMs in the resource group indicated by a script parameter. The script is short and simple but automates a process that would take a long time to complete manually with the portal.