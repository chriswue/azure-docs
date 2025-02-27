---
title: Azure Cloud Shell Quickstart - PowerShell
description: Learn how to use the PowerShell in your browser with Azure Cloud Shell.
author: maertendmsft
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure-resource-manager
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 10/18/2018
ms.custom: devx-track-azurepowershell, ignite-fall-2021
---

# Quickstart for PowerShell in Azure Cloud Shell

This document details how to use the PowerShell in Cloud Shell in the [Azure portal](https://portal.azure.com/).

> [!NOTE]
> A [Bash in Azure Cloud Shell](quickstart.md) Quickstart is also available.

## Start Cloud Shell

1. Click on **Cloud Shell** button from the top navigation bar of the Azure portal

   ![Screenshot showing how to start Azure Cloud Shell from the Azure portal.](media/quickstart-powershell/shell-icon.png)

2. Select the PowerShell environment from the drop-down and you will be in Azure drive `(Azure:)`

   ![Screenshot showing how to select the PowerShell environment for the Azure Cloud Shell.](media/quickstart-powershell/environment-ps.png)

## Run PowerShell commands

Run regular PowerShell commands in the Cloud Shell, such as:

```azurepowershell-interactive
PS Azure:\> Get-Date
```

```output
# You will see output similar to the following:
Friday, July 27, 2018 7:08:48 AM
```

```azurepowershell-interactive
PS Azure:\> Get-AzVM -Status
```

```output
# You will see output similar to the following:
ResourceGroupName       Name       Location                VmSize   OsType     ProvisioningState  PowerState
-----------------       ----       --------                ------   ------     -----------------  ----------
MyResourceGroup2        Demo        westus         Standard_DS1_v2  Windows    Succeeded           running
MyResourceGroup         MyVM1       eastus            Standard_DS1  Windows    Succeeded           running
MyResourceGroup         MyVM2       eastus   Standard_DS2_v2_Promo  Windows    Succeeded           deallocated
```

### Interact with virtual machines

You can find all your virtual machines under the current subscription via `VirtualMachines` directory.

```azurepowershell-interactive
PS Azure:\MySubscriptionName\VirtualMachines> dir
```

```output
# You will see output similar to the following:
    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running
```

#### Invoke PowerShell script across remote VMs

 > [!WARNING]
 > Please refer to [Troubleshooting remote management of Azure VMs](troubleshooting.md#troubleshooting-remote-management-of-azure-vms).

  Assuming you have a VM, MyVM1, let's use `Invoke-AzVMCommand` to invoke a PowerShell script block on the remote machine.

  ```azurepowershell-interactive
  Enable-AzVMPSRemoting -Name MyVM1 -ResourceGroupname MyResourceGroup
  Invoke-AzVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)
  ```

  You can also navigate to the VirtualMachines directory first and run `Invoke-AzVMCommand` as follows.

  ```azurepowershell-interactive
  PS Azure:\> cd MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzVMCommand -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)
  ```

  ```output
  # You will see output similar to the following:

  PSComputerName                                          : 65.52.28.207
  RunspaceId                                              : 2c2b60da-f9b9-4f42-a282-93316cb06fe1
  WindowsBuildLabEx                                       : 14393.1066.amd64fre.rs1_release_sec.170327-1835
  WindowsCurrentVersion                                   : 6.3
  WindowsEditionId                                        : ServerDatacenter
  WindowsInstallationType                                 : Server
  WindowsInstallDateFromRegistry                          : 5/18/2017 11:26:08 PM
  WindowsProductId                                        : 00376-40000-00000-AA947
  WindowsProductName                                      : Windows Server 2016 Datacenter
  WindowsRegisteredOrganization                           :
   ...
  ```

#### Interactively log on to a remote VM

You can use `Enter-AzVM` to interactively log into a VM running in Azure.

  ```azurepowershell-interactive
  PS Azure:\> Enter-AzVM -Name MyVM1 -ResourceGroupName MyResourceGroup -Credential (Get-Credential)
  ```

You can also navigate to the `VirtualMachines` directory first and run `Enter-AzVM` as follows:

```azurepowershell-interactive
PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzVM -Credential (Get-Credential)
```

### Discover WebApps

By entering into the `WebApps` directory, you can easily navigate your web apps resources

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir .\WebApps\
```

```output
# You will see output similar to the following:
    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Stopped  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US
```

```azurepowershell-interactive
# You can use Azure cmdlets to Start/Stop your web apps
PS Azure:\MySubscriptionName\WebApps> Start-AzWebApp -Name mywebapp1 -ResourceGroupName MyResourceGroup1
```

```output
# You will see output similar to the following:
Name           State    ResourceGroup        EnabledHostNames                   Location
----           -----    -------------        ----------------                   --------
mywebapp1      Running  MyResourceGroup1     {mywebapp1.azurewebsites.net ...   West US
```

```azurepowershell-interactive
# Refresh the current state with -Force
PS Azure:\MySubscriptionName\WebApps> dir -Force
```

```output
# You will see output similar to the following:
    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Running  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US
```

## SSH

To authenticate to servers or VMs using SSH, generate the public-private key pair in Cloud Shell and
publish the public key to `authorized_keys` on the remote machine, such as `/home/user/.ssh/authorized_keys`.

> [!NOTE]
> You can create SSH private-public keys using `ssh-keygen` and publish them to `$env:USERPROFILE\.ssh` in Cloud Shell.

### Using SSH

Follow instructions [here](../virtual-machines/linux/quick-create-powershell.md) to create a new VM configuration using Azure PowerShell cmdlets.
Before calling into `New-AzVM` to kick off the deployment, add SSH public key to the VM configuration.
The newly created VM will contain the public key in the `~\.ssh\authorized_keys` location, thereby enabling credential-free SSH session to the VM.

```azurepowershell-interactive
# Create VM config object - $vmConfig using instructions on linked page above

# Generate SSH keys in Cloud Shell
ssh-keygen -t rsa -b 2048 -f $HOME\.ssh\id_rsa 

# Ensure VM config is updated with SSH keys
$sshPublicKey = Get-Content "$HOME\.ssh\id_rsa.pub"
Add-AzVMSshPublicKey -VM $vmConfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

# Create a virtual machine
New-AzVM -ResourceGroupName <yourResourceGroup> -Location <vmLocation> -VM $vmConfig

# SSH to the VM
ssh azureuser@MyVM.Domain.Com
```

## List available commands

Under `Azure` drive, type `Get-AzCommand` to get context-specific Azure commands.

Alternatively, you can always use `Get-Command *az* -Module Az.*` to find out the available Azure commands.

## Install custom modules

You can run `Install-Module` to install modules from the [PowerShell Gallery][gallery].

## Get-Help

Type `Get-Help` to get information about PowerShell in Azure Cloud Shell.

```azurepowershell-interactive
Get-Help
```

For a specific command, you can still do `Get-Help` followed by a cmdlet.

```azurepowershell-interactive
Get-Help Get-AzVM
```

## Use Azure Files to store your data

You can create a script, say `helloworld.ps1`, and save it to your `clouddrive` to use it across shell sessions.

```azurepowershell-interactive
cd $HOME\clouddrive
# Create a new file in clouddrive directory
New-Item helloworld.ps1
# Open the new file for editing
code .\helloworld.ps1
# Add the content, such as 'Hello World!'
.\helloworld.ps1
Hello World!
```

Next time when you use PowerShell in Cloud Shell, the `helloworld.ps1` file will exist under the `$HOME\clouddrive` directory that mounts your Azure Files share.

## Use custom profile

You can customize your PowerShell environment, by creating PowerShell profile(s) - `profile.ps1` (or `Microsoft.PowerShell_profile.ps1`).
Save it under `$profile.CurrentUserAllHosts` (or `$profile.CurrentUserCurrentHost`), so that it can be loaded in every PowerShell in Cloud Shell session.

For how to create a profile, refer to [About Profiles][profile].

## Use Git

To clone a Git repo in the Cloud Shell, you need to create a [personal access token][githubtoken] and use it as the username. Once you have your token, clone the repository as follows:

```azurepowershell-interactive
  git clone https://<your-access-token>@github.com/username/repo.git
```

## Exit the shell

Type `exit` to terminate the session.

[bashqs]: quickstart.md
[gallery]: https://www.powershellgallery.com/
[customex]: ../virtual-machines/extensions/custom-script-windows.md
[profile]: /powershell/module/microsoft.powershell.core/about/about_profiles
[azmount]: ../storage/files/storage-how-to-use-files-windows.md
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
