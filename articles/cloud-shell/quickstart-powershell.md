---
title: Azure Cloud Shell（预览版）中的 PowerShell 快速入门 | Microsoft Docs
description: Cloud Shell 中的 PowerShell 快速入门
services: Azure
documentationcenter: ''
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/27/2018
ms.author: damaerte
ms.openlocfilehash: adae7ea79ada9247382c88e58f1ba5331007985b
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39324487"
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell-preview"></a>PowerShell in Azure Cloud Shell（预览版）快速入门

本文档详细介绍如何在 [Azure 门户](https://aka.ms/PSCloudPreview)中使用 Cloud Shell 中的 PowerShell。

> [!NOTE]
> 此外，还提供了 [Azure Cloud Shell 中的 Bash](quickstart.md) 快速入门。

## <a name="start-cloud-shell"></a>启动 Cloud Shell

1. 在 Azure 门户的顶部导航栏中单击“Cloud Shell”按钮

  ![](media/quickstart-powershell/shell-icon.png)

2. 在下拉列表中选择 PowerShell 环境，随后会进入 Azure 驱动器 `(Azure:)`

  ![](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>运行 PowerShell 命令

在 Cloud Shell 中运行普通的 PowerShell 命令，例如：

```azurepowershell-interactive
PS Azure:\> Get-Date

# Expected Output
Friday, July 27, 2018 7:08:48 AM

PS Azure:\> Get-AzureRmVM -Status

# Expected Output
ResourceGroupName       Name       Location                VmSize   OsType     ProvisioningState  PowerState
-----------------       ----       --------                ------   ------     -----------------  ----------
MyResourceGroup2        Demo        westus         Standard_DS1_v2  Windows    Succeeded           running
MyResourceGroup         MyVM1       eastus            Standard_DS1  Windows    Succeeded           running
MyResourceGroup         MyVM2       eastus   Standard_DS2_v2_Promo  Windows    Succeeded           deallocated
```

## <a name="navigate-azure-resources"></a>浏览 Azure 资源

 1. 列出你在 `Azure` 驱动器中的所有订阅

    ```azurepowershell-interactive
    PS Azure:\> dir
    ```

 2. 运行 `cd` 可切换到首选的订阅

    ```azurepowershell-interactive
    PS Azure:\> cd MySubscriptionName
    PS Azure:\MySubscriptionName>
    ```

 3. 查看当前订阅下的所有 Azure 资源

    键入 `dir` 可列出 Azure 资源的多个视图。

    ```azurepowershell-interactive
    PS Azure:\MySubscriptionName> dir

        Directory: azure:\MySubscriptionName

    Mode Name
    ---- ----
    +    AllResources
    +    ResourceGroups
    +    StorageAccounts
    +    VirtualMachines
    +    WebApps
    ```

### <a name="allresources-view"></a>“所有资源”视图

在 `AllResources` 目录下键入 `dir` 可查看 Azure 资源。

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir AllResources
```

### <a name="explore-resource-groups"></a>浏览资源组

 可以转到 `ResourceGroups` 目录，并在特定的资源组中查找虚拟机。

```azureowershell-interactive
PS Azure:\MySubscriptionName> cd ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines

PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines> dir


    Directory: Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines


VMName    Location   ProvisioningState VMSize          OS            SKU             OSVersion AdminUserName  NetworkInterfaceName
------    --------   ----------------- ------          --            ---             --------- -------------  --------------------
TestVm1   westus     Succeeded         Standard_DS2_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo371
TestVm2   westus     Succeeded         Standard_DS1_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo271
```

> [!NOTE]
> 可以发现，在第二次键入 `dir` 时，Cloud Shell 能够更快地显示项。
> 这是因为，子项已缓存在内存中，以提供更好的用户体验。
但是，始终可以使用 `dir -Force` 来获取全新数据。

### <a name="navigate-storage-resources"></a>浏览存储资源

进入 `StorageAccounts` 目录可以轻松浏览你的所有存储资源

```azureowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> dir

    Directory: Azure:\MySubscriptionNameStorageAccounts\MyStorageAccountName\Files

Name          ConnectionString
----          ----------------
MyFileShare1  \\MyStorageAccountName.file.core.windows.net\MyFileShare1;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare2  \\MyStorageAccountName.file.core.windows.net\MyFileShare2;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare3  \\MyStorageAccountName.file.core.windows.net\MyFileShare3;AccountName=MyStorageAccountName AccountKey=<key>
```

借助连接字符串，可以使用以下命令装载 Azure 文件共享。

```azurepowershell-interactive
net use <DesiredDriveLetter>: \\<MyStorageAccountName>.file.core.windows.net\<MyFileShareName> <AccountKey> /user:Azure\<MyStorageAccountName>
```

有关详细信息，请参阅[在 Windows 中装载 Azure 文件共享并对其进行访问][azmount]。

还可以按如下所示浏览 Azure 文件共享下的目录：

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> cd .\MyFileShare1\
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files\MyFileShare1> dir

Mode  Name
----  ----
+     TestFolder
.     hello.ps1
```

### <a name="interact-with-virtual-machines"></a>与虚拟机交互

可以通过 `VirtualMachines` 目录查找当前订阅下的所有虚拟机。

```azurepowershell-interactive
PS Azure:\MySubscriptionName\VirtualMachines> dir

    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running
```

#### <a name="invoke-powershell-script-across-remote-vms"></a>跨远程 VM 调用 PowerShell 脚本

 > [!WARNING]
 > 请参阅 [Azure VM 的远程管理故障排除](troubleshooting.md#troubleshooting-remote-management-of-azure-vms)。

  假设有一个名为 MyVM1 的 VM，现在让我们使用 `Invoke-AzureRmVMCommand` 在远程计算机上调用 PowerShell 脚本块。

  ```azurepowershell-interactive
  Invoke-AzureRmVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -EnableRemoting
  ```

  还可以先导航到 VirtualMachines 目录，然后按如下所示运行 `Invoke-AzureRmVMCommand`。

  ```azurepowershell-interactive
  PS Azure:\> cd MySubscriptionName\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzureRmVMCommand -Scriptblock {Get-ComputerInfo}

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

#### <a name="interactively-log-on-to-a-remote-vm"></a>以交互方式登录到远程 VM

可以使用 `Enter-AzureRmVM` 以交互方式登录到 Azure 中运行的 VM。

  ```azurepowershell-interactive
  PS Azure:\> Enter-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup -EnableRemoting
  ```

还可以先导航到 `VirtualMachines` 目录，然后按如下所示运行 `Enter-AzureRmVM`

  ```azurepowershell-interactive
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzureRmVM
 ```

### <a name="discover-webapps"></a>发现 WebApps

进入 `WebApps` 目录可以轻松浏你的 Web 应用资源

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir .\WebApps\

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Stopped  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US

# You can use Azure cmdlets to Start/Stop your web apps
PS Azure:\MySubscriptionName\WebApps> Start-AzureRmWebApp -Name mywebapp1 -ResourceGroupName MyResourceGroup1

Name           State    ResourceGroup        EnabledHostNames                   Location
----           -----    -------------        ----------------                   --------
mywebapp1      Running  MyResourceGroup1     {mywebapp1.azurewebsites.net ...   West US

# Refresh the current state with -Force
PS Azure:\MySubscriptionName\WebApps> dir -Force

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Running  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US
```

## <a name="ssh"></a>SSH

[Win32-OpenSSH](https://github.com/PowerShell/Win32-OpenSSH) 在 PowerShell Cloud Shell 中可用。
若要使用 SSH 对服务器或 VM 进行身份验证，请在 Cloud Shell 中生成公钥/私钥对并将公钥发布到远程计算机上的 `authorized_keys`，如 `/home/user/.ssh/authorized_keys`。

> [!NOTE]
> 可以使用 `ssh-keygen` 创建 SSH 私钥/公钥并将其发布到 Cloud Shell 中的 `$env:USERPROFILE\.ssh`。

### <a name="using-ssh"></a>使用 SSH

按照[此处](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-powershell)的说明使用 AzureRM cmdlet 创建新的 VM 配置。
在调用 `New-AzureRmVM` 以启动部署之前，请将 SSH 公钥添加到 VM 配置。
新创建的 VM 将包含 `~\.ssh\authorized_keys` 位置中的公钥，从而允许通过无凭据的 SSH 会话连接到 VM。

```azurepowershell-interactive
# Create VM config object - $vmConfig using instructions on linked page above

# Generate SSH keys in Cloud Shell
ssh-keygen -t rsa -b 2048 -f $HOME\.ssh\id_rsa 

# Ensure VM config is updated with SSH keys
$sshPublicKey = Get-Content "$HOME\.ssh\id_rsa.pub"
Add-AzureRmVMSshPublicKey -VM $vmConfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

# Create a virtual machine
New-AzureRmVM -ResourceGroupName <yourResourceGroup> -Location <vmLocation> -VM $vmConfig

# SSH to the VM
ssh azureuser@MyVM.Domain.Com
```

## <a name="list-available-commands"></a>列出可用命令

在 `Azure` 驱动器下，键入 `Get-AzureRmCommand` 可获取特定于上下文的 Azure 命令。

或者，始终可以使用 `Get-Command *azurerm* -Module AzureRM.*` 找出可用的 Azure 命令。

## <a name="install-custom-modules"></a>安装自定义模块

可以运行 `Install-Module` 安装 [PowerShell 库][gallery]中的模块。

## <a name="get-help"></a>Get-Help

键入 `Get-Help` 可获取有关 Azure Cloud Shell 中的 PowerShell 的信息。

```azurepowershell-interactive
Get-Help
```

对于特定命令，仍可以执行 `Get-Help` 后接一个 cmdlet。

```azurepowershell-interactive
Get-Help Get-AzureRmVM
```

## <a name="use-azure-files-to-store-your-data"></a>使用 Azure 文件存储数据

可以创建一个脚本（例如 `helloworld.ps1`）并将其保存到 `clouddrive`，以便在不同的 shell 会话中使用该脚本。

```azurepowershell-interactive
cd $HOME\clouddrive
code .\helloworld.ps1
# Add the content, such as 'Hello World!'
.\helloworld.ps1
Hello World!
```

下次使用 Cloud Shell 中的 PowerShell 时，`helloworld.ps1` 文件将出现在装载 Azure 文件共享的 `$HOME\clouddrive` 目录下。

## <a name="use-custom-profile"></a>使用自定义配置文件

可以创建 PowerShell 配置文件 `profile.ps1`（或 `Microsoft.PowerShell_profile.ps1`）来自定义 PowerShell 环境。
将配置文件保存在 `$profile.CurrentUserAllHosts`（或 `$profile.CurrentUserAllHosts`）下，以便可以在 Cloud Shell 会话中的每个 PowerShell 中加载该文件。

有关如何创建配置文件，请参阅[关于配置文件][profile]。

## <a name="use-git"></a>使用 Git

若要在 Cloud Shell 中克隆 Git 存储库，需要创建[个人访问令牌][githubtoken]并将其用作用户名。 创建令牌后，可按如下所示克隆存储库：

```azurepowershell-interactive
  git clone https://<your-access-token>@github.com/username/repo.git
```

## <a name="exit-the-shell"></a>退出 shell

键入 `exit` 可终止会话。

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/about/about_profiles
[azmount]: https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
