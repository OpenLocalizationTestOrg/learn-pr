The Public Information Officer (PIO) in your department maintains a non-public facing website for use by the local media. Your PIO uses her mobile device to update content on the media website so that local media outlets can stay informed about current events. To prevent unauthorized or incorrect information being presented to the media, this site must be as secure as possible. As the administrator, one approach you can take to improve security is to keep the site current with the latest updates.

Here, we'll introduce the Update Management solution for Azure.

## Update Management overview

 The Update Management solution allows you to manage and install operating system updates and patches for both Windows and Linux virtual machines that are deployed in Azure, on-premises, or even in other cloud providers. You can assess the status of available updates on computers and manage the process of installing required updates for servers.  

 There are several advantages to the Update Management solution:
  1. There are no agents or additional configuration within the virtual machine.
  1. You can run updates without logging into the VM. You also don't have to create passwords to install the update.
  1. The Update Management solution lists missing updates and provides information about failed deployments in an easy-to-read format.

Update Management can be used to natively onboard machines in multiple subscriptions in the same tenant. To manage machines in a different tenant, you must onboard them as non-Azure machines.

## Supported Operating Systems

Update Management solution supports Windows and Linux, specifically:
 - Windows Server (2008 and newer)
 - CentOS 6 (x86/X64) and CentOS 7
 - Red Hat Enterprise 6 (x86/x64) and 7 (x64)
 - SUSE Linux Enterprise Server 11 (x86/x64) and 12 (x64)
 - Ubuntu 14.04 LTS, 16.04 LTS and 18.04 (x86/x64)

In this module, we'll work with Windows Server 2016 virtual machine deployed in Azure.
 
## Components Used by Update Management

The following configurations are used to perform assessment and update deployments:

- Microsoft Monitoring Agent (MMA) for Windows or Linux.
- PowerShell Desired State Configuration (DSC) for Linux.
- Automation Hybrid Runbook Worker.
- Microsoft Update or Windows Server Update Services (WSUS) for Windows computers.

The following diagram shows a conceptual view of the behavior and data flow with how the solution assesses and applies security updates to all connected Windows Server and Linux computers in a workspace.

![Conceptual View of Data flow](../media/2-conceptual-view-data-flow50.png "Conceptual View of Data Flow")

### Hybrid Worker Groups

 Windows computers that are directly connected to your Log Analytics workspace are automatically configured as a Hybrid Runbook Worker to support the runbooks that are included in this solution. Each Windows computer that's managed by the solution is listed in the Hybrid worker groups pane as a System hybrid worker group for the Automation account. The solutions use the naming convention Hostname FQDN_GUID.

## Operations Manager Management Packs

If your System Center Operations Manager management group is connected to a Log Analytics workspace, the following management packs are installed in Operations Manager. These management packs are also installed on directly connected Windows computers after you add the solution. You don't need to configure or manage these management packs.
- Microsoft System Center Advisor Update Assessment Intelligence Pack 
- Microsoft.IntelligencePack.UpdateAssessment.Configuration
- Update Deployment MP
