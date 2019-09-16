In this unit, to configure a build agent that you can use in Azure Pipelines, you use a virtual machine that runs on Azure. We provide a virtual machine that you can use for the duration of this module.

In this unit, you will:

> [!div class="checklist"]
> * Create an Ubuntu virtual machine on Azure to serve as your build agent.
> * Create an agent pool in Azure DevOps.
> * Create an access token to authenticate your agent with Azure DevOps.
> * Configure your agent with the software that's required to build the _Space Game_ website.
> * Configure your agent to connect to Azure DevOps so that it can receive build jobs.
> * Verify that the agent is connected to Azure DevOps and ready to receive build jobs.

There are many ways to create a virtual machine on Azure. In this unit, you create an Ubuntu virtual machine by using an interactive terminal called Cloud Shell.

[!include[](../../../includes/azure-sandbox-activate.md)]

To configure your VM, you have several choices:
* For a Linux VM, you can connect directly over SSH and interactively configure your system. 
* You can automate the deployment by using an Azure Resource Manager template. 
* If you need to deploy many build agents, you can create a VM image that has all the software pre-installed.

Configuring a system interactively is a good way to get started, because it helps you understand the process and what's needed. To simplify the process, connect to your Ubuntu VM over SSH and run shell scripts to set up your build agent.

> [!NOTE]
> If you're unfamiliar with connecting to or configuring Linux systems, just follow along. You can apply the same concepts to Windows build agents.

## Create a Linux virtual machine

In this section, you create a VM that's running Ubuntu 16.04, which will serve as your build agent. The VM isn't yet set up to be a build agent or have any of the tools that are required to build the _Space Game_ web application. You'll set that up shortly.

To create your VM, in Cloud Shell (at right), run the following `az vm create` command:

```azurecli
az vm create \
    --name MyLinuxAgent \
    --resource-group <rgn>[Resource Group Name]</rgn> \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --location eastus \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --generate-ssh-keys
```

Your VM should take about two minutes to come up.

[Standard_DS2_v2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general?azure-portal=true#dsv2-series) specifies the VM's size. A VM's size defines its processor speed, amount of memory, initial amount of storage, and expected network bandwidth. This is the same size that's provided by Microsoft-hosted agents. In practice, you can choose a size that provides more compute power or additional capabilities, such as graphics processing.

The `--resource-group` argument specifies the _resource group_ that holds all the things that we need to create. A resource group enables you to administer all the VMs, disks, network interfaces, and other elements that make up our solution as a unit. Normally, you would create your own resource group before you create Azure resources. Because you're in the free Azure sandbox environment, you can skip this step. Instead, you use the pre-created resource group <rgn>[Resource Group Name]</rgn>.

> [!IMPORTANT]
> The Azure sandbox gives you temporary access to Azure resources. When your session expires, the VM you create here will no longer be accessible to you as a build agent. In practice, you would set up a build agent by using your own Azure subscription or a system that's running in your datacenter.

## Create the agent pool

Recall that an agent pool organizes build agents. In this section, you create the agent pool in Azure DevOps. Later, you'll specify the name of the agent pool when you configure your agent so that it can register itself to the correct pool.

1. In Azure DevOps, go to the **Space Game - web - Agent** project.
1. Select **Project settings**.
1. Under **Pipelines**, select **Agent pools**.

    ![Locating Agent pools in the menu](../media/4-project-settings-agent-pools.png)
1. Select **Add pool**.
1. In the **Add agent pool** window, select **New** and then, in the text box, enter **MyAgentPool**.

    In practice, you would choose a more descriptive name for the purpose of your pool.
1. Select **Create**.
    The new agent pool appears in the list.

## Create a personal access token

For your build agent to register itself with Azure DevOps, you need a way for it to authenticate itself.

To do that, you create a personal access token. A personal access token, or PAT, is an alternative to a password. You can use the PAT to authenticate with services such as Azure DevOps.

> [!IMPORTANT]
> As you would with a password, be sure to keep your access token in a safe place. In this section, you store your access token as an environment variable so that it doesn't appear in your shell script.

1. In Azure DevOps, open your profile, and then select **Security**.

    ![Locating Security in the menu](../media/4-settings-security.png)
1. Select **New Token**.
1. Enter a name for your token, such as **Build agent**.
1. Under **Scopes**, select **Show all scopes**.
1. Look for **Agent Pools**, and then select **Read & manage**.
1. Look for **Build**, and then select **Read & execute**.
1. Select **Create**.
1. Copy the token to a safe place.

    Shortly, you'll use your token to enable your build agent to authenticate access to Azure Pipelines.

### Connect to your VM

In this section, you connect to your Linux VM over SSH so that you can configure it.

Recall that you can't interactively sign in to a Microsoft-hosted agent. Because a private build agent is your own, you can sign in to and configure it however you'd like.

The ability to connect to your build agent enables you to configure it with the tools you need to build your software. It also enables you to troubleshoot issues as you build out your pipeline configuration.

1. To get your VM's IP address, in Cloud Shell, run `az vm show`:

    ```azurecli
    IPADDRESS=$(az vm show \
      --name MyLinuxAgent \
      --resource-group <rgn>[Resource Group Name]</rgn> \
      --show-details \
      --query [publicIps] \
      --output tsv)
    ```

    This command stores the IP address in a Bash variable named `IPADDRESS`.

1. Print the VM's IP address to the console.

    ```bash
    echo $IPADDRESS
    ```

1. Create an SSH connection to your VM. At the prompt, enter **yes** to continue connecting.

    ```bash
    ssh azureuser@$IPADDRESS
    ```

    You're now connected to your VM over SSH.

    This command works because you provided the `--generate-ssh-keys` option when you ran `az vm create` earlier. This option creates an SSH key pair, which enables you to sign in to the VM.

## Install build tools on your VM

In this section, you configure your VM with the tools that are required to build the _Space Game_ website.

Recall that your existing build process uses these tools:

* .NET Core
* Node.js, which is used to perform build tasks
* npm, the package manager for Node.js
* gulp, a Node.js package that's used to minify JavaScript and CSS files

These are the primary tools that the build process requires. To install them, you download and run a shell script from GitHub.

> [!NOTE]
> The build process uses other tools, such as node-sass, to convert Sass (.scss) files to CSS (.css) files. However, Node.js installs these tools when the build runs.

Let's start by updating the Ubuntu package manager, named *apt*. This action fetches the latest information from the package repositories and is ordinarily the first thing you do when you set up a new Ubuntu system.

1. In your SSH connection, update the apt package manager cache.

    ```bash
    sudo apt-get update
    ```

    `sudo` runs the command with administrator, or root, privileges.

1. To download a shell script named *build-tools.sh* from GitHub, run the following `curl` command:

    ```bash
    curl https://raw.githubusercontent.com/MicrosoftDocs/mslearn-azure-pipelines-build-agent/master/build-tools.sh > build-tools.sh
    ```

1. Print the script to the terminal so that you can examine its contents.

    ```bash
    cat build-tools.sh
    ```

    The following result is displayed:

    [!code-bash[](code/build-tools.sh)]

    The script installs Node.js, npm, gulp, and .NET Core.

    By setting the `DOTNET_VERSION` environment variable, you can specify the .NET version to install. If you don't set this variable, the script installs the version that your existing build configuration uses. For learning purposes, you don't set this variable. You allow the script to use the default version.

1. Make the script executable, and then run the script.

    ```bash
    chmod u+x build-tools.sh
    sudo ./build-tools.sh
    ```

    In practice, you could now run commands to verify that each software component was successfully installed.

## Install agent software on your VM

Now it's time to install the agent software on your VM. This software enables the VM to act as a build agent and receive build jobs from Azure Pipelines.

The registration process checks for installed software before it registers the agent with Azure Pipelines. Therefore, it's important to set up the agent after you install all other software. In practice, you can register the agent a second time if you need to install additional software.

The documentation explains how to manually set up [self-hosted Linux agents](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-linux?view=azure-devops&azure-portal=true) as well as macOS and Windows agents. You run a shell script to configure your agent, much like the way you set up build tools in the preceding section.

> [!IMPORTANT]
> The script that you run here is for learning purposes. In practice, you should first understand how each command in the scripts you build affect the overall system. At the end of the module, we'll point to documentation that more completely describes your options.

1. To download a shell script named *build-agent.sh* from GitHub, run the following `curl` command:

    ```bash
    curl https://raw.githubusercontent.com/MicrosoftDocs/mslearn-azure-pipelines-build-agent/master/build-agent.sh > build-agent.sh
    ```

1. Print the script to the terminal so that you can examine its contents.

    ```bash
    cat build-agent.sh
    ```

    The following result is displayed:

    [!code-bash[](code/build-agent.sh)]

    You don't need to understand how each line works, but here's a brief summary of what this script does:

    * It downloads the agent package as a *.tar.gz* file and extracts its contents.
    * In the extracted files, the script:
      * Runs a shell script named *installdependencies.sh* to install the agent software.
      * Runs a shell script named *config.sh* to configure the agent and register the agent with Azure Pipelines.
      * Runs a shell script named *svc.sh* to install and start the agent service.

    The script uses environment variables to enable you to provide details about your Azure DevOps organization. Here's a summary:

    | Bash variable       | Description                          | Default |
    |---------------------|--------------------------------------|---------|
    | `AZP_AGENT_VERSION` | The [version of the agent software](https://github.com/Microsoft/azure-pipelines-agent/releases?azure-portal=true) to install | The version we last used to test this module |
    | `AZP_URL`           | The URL of your Azure DevOps organization | (None) |
    | `AZP_TOKEN`         | Your personal access token           | (None) |
    | `AZP_AGENT_NAME`    | Your agent's name as it appears in Azure DevOps | The system's hostname |
    | `AZP_POOL`          | The name of your agent pool          | **Default** |
    | `AZP_WORK`          | The working directory for the agent to perform build tasks | **_work** |

    If the script doesn't provide a default value for a variable that's not set, the script prints an error message and immediately exits.

    In the steps that follow, set these environment variables:

    * `AZP_URL`
    * `AZP_TOKEN`
    * `AZP_AGENT_NAME`
    * `AZP_POOL`

    For now, we recommend that you leave the other variables unset.

1. Set the `AZP_AGENT_NAME` environment variable to specify your agent's name. We recommend **MyLinuxAgent**.

    ```bash
    export AZP_AGENT_NAME=MyLinuxAgent
    ```

1. Set the `AZP_URL` environment variable to specify the URL to your Azure DevOps organization.

    Replace *\<organization>* with yours. You can get the name from the browser tab that displays Azure DevOps.

    ```bash
    export AZP_URL=https://dev.azure.com/organization
    ```

1. Set the `AZP_TOKEN` environment variable to specify your personal access token.

    Replace *\<token>* with your token.

    ```bash
    export AZP_TOKEN=token
    ```

1. Set the `AZP_POOL` environment variable to specify the name of your agent pool. Earlier, you created a pool named *MyAgentPool*.

    ```bash
    export AZP_POOL=MyAgentPool
    ```

1. Make the script executable, and then run it.

    ```bash
    chmod u+x build-agent.sh
    sudo -E ./build-agent.sh
    ```

    `sudo` enables the script to run as the root user. The `-E` argument preserves the current environment variables, including the ones you set, so that they are available to the script.

    As the script runs, you can see the agent connect to Azure DevOps, see it added to the agent pool, and see the agent connection be tested.

## Verify that the agent is running

You've successfully installed the build tools and the agent software on your VM. As a verification step, go to Azure DevOps and see your agent in the agent pool.

1. In Azure DevOps, go to the **Space Game - web - Agent** project.
1. Select **Project settings**.
1. Under **Pipelines**, select **Agent pools**.

    ![Locating Agent pools in the menu](../media/4-project-settings-agent-pools.png)
1. Select **MyAgentPool**.
1. Select the **Agents** tab.

    You can see that your agent is online and ready to accept build jobs.

    ![Azure DevOps showing the private agent's status](../media/4-project-settings-agent-details.png)

    > [!TIP]
    > If your build agent shows as **Offline**, try waiting a few moments and then refreshing the page.

1. Select your agent, **MyLinuxAgent**.
1. Select the **Capabilities** tab.

    During setup, the configuration process scanned your build agent for tool capabilities. You see that `npm` is listed as one of them. Recall that your original build configuration specified that `npm` must be installed on the agent.

    ![Locating npm in the list of capabilities](../media/4-project-settings-agent-capabilities.png)

    When you specify which agent pool to use, you can include any of these entries in your `demands` section. Including them ensures that Azure Pipelines chooses a build agent that has the software you need to build your application. It also enables you to create agent pools with various software configurations. Azure Pipelines will select the correct configuration based on your requirements.