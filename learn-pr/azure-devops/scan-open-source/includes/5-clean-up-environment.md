You're all done with the tasks for this module. Here, you'll move the work item to the **Done** state on Azure Boards and clean up your Azure DevOps environment.

> [!IMPORTANT]
> This page contains important cleanup steps. Cleaning up helps ensure that you don't run out of free build minutes. Be sure to run the cleanup steps if you ran the template earlier in this module.

## Move the work item to Done

Here, you'll move the work item you assigned to yourself earlier in this module, **Check open source code for vulnerabilities and licensing terms**, to the **Done** column.

In practice, the definition of "Done" often means working software in the hands of your users. For learning purposes, here you'll mark this work as complete because you're able to scan open-source components for license ratings and potential vulnerabilities in your build pipeline.

At the end of each Sprint, or work iteration, you and your team might hold a retrospective meeting, where you share the work you completed, what went well in the Sprint, and what could be improved.

To complete the work item:

1. From Azure DevOps, navigate to **Boards** and then select **Boards** from the menu.
1. Move the **Check open source code for vulnerabilities and licensing terms** work item from the **Doing** to the **Done** column.

    ![Azure Boards showing the card in the Done column](../media/5-azure-boards-wi5-done.png)

## Disable the pipeline or delete your project

> [!IMPORTANT]
> You can skip this part if you plan to continue to the next module using the same Azure DevOps project.

Each module in this learning path provides a template you can run to create a clean environment for the duration of the module.

Running multiple templates gives you multiple Azure Pipelines projects, each pointing to the same GitHub repository. This can trigger multiple pipelines to run each time you push a change to your GitHub repository, which can cause you to run out of free build minutes on our hosted agents. Therefore, it's important that you disable or delete your pipeline before moving on to the next module.

Choose one of the following options.

### Option 1 - Disable the pipeline

This option disables the pipeline so that it doesn't process further build requests. You can re-enable the build pipeline later if you want to. Choose this option if you want to keep your Azure DevOps project and your build pipeline for future reference.

To disable the pipeline:

1. From Azure Pipelines, go to your pipeline.
1. Select the **Edit** button.

    ![Azure Pipelines showing the location of the Edit button](../../shared/media/azure-pipelines-edit-button.png)
1. On the menu in the upper corner, select **Settings**.

    ![Azure Pipelines showing the Settings menu option](../../shared/media/azure-pipelines-settings-button.png)
1. Under **Processing of new run requests**, select **Disabled**. Then select **Save**.

    Your pipeline will no longer process build requests.

### Option 2 - Delete the Azure DevOps project

This option deletes your Azure DevOps project, including what's on Azure Boards and in your build pipeline. In future modules, you'll be able to run another template that brings up a new project in a state where this one leaves off. Choose this option if you don't need your DevOps project for future reference.

To delete the project:

1. From Azure DevOps, go to your project. Earlier, we recommended that you name this project **Space Game - web - WhiteSource**.
1. Select the gear icon next to the project name.

    The icon might not appear until you move your mouse over that area.

    ![Azure DevOps showing the gear icon](../../shared/media/azure-devops-project-gear.png)
1. From the **Project details** area, scroll to the bottom and click **Delete**.

    ![Azure DevOps showing the Delete button to delete your project](../../shared/media/azure-devops-delete-project.png)
1. From the window that appears, enter the project name and click **Delete** a second time.

    Your project is now deleted.