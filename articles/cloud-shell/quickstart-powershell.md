---
title: "Azure Cloud Shell（预览版）中的 PowerShell 快速入门 | Microsoft Docs"
description: "Cloud Shell 中的 PowerShell 快速入门"
services: Azure
documentationcenter: 
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: damaerte
ms.openlocfilehash: fd1d340bc0408eaeb0b7b18235df109224eae5f5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell"></a>Azure Cloud Shell 中的 PowerShell 快速入门

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

```Powershell
PS Azure:\> Get-Date
Monday, September 25, 2017 08:55:09 AM

PS Azure:\> Get-AzureRmVM -Status

ResourceGroupName       Name       Location                VmSize   OsType     ProvisioningState  PowerState
-----------------       ----       --------                ------   ------     -----------------  ----------
MyResourceGroup2        Demo        westus         Standard_DS1_v2  Windows    Succeeded           running
MyResourceGroup         MyVM1       eastus            Standard_DS1  Windows    Succeeded           running
MyResourceGroup         MyVM2       eastus   Standard_DS2_v2_Promo  Windows    Succeeded           deallocated
```

## <a name="navigate-azure-resources"></a>浏览 Azure 资源

 1. 列出订阅

    ``` Powershell
    PS Azure:\> dir
    ```

 2. 运行 `cd` 可切换到首选的订阅

    ``` Powershell
    PS Azure:\> cd MySubscriptionName
    PS Azure:\MySubscriptionName>
    ```

 3. 查看当前订阅下的所有 Azure 资源
 
    键入 `dir` 可列出 Azure 资源的多个视图。
 
    ``` PowerShell
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
    
    PS Azure:\MySubscriptionName> dir AllResources

### <a name="explore-resource-groups"></a>浏览资源组

 可以转到 `ResourceGroups` 目录，并在特定的资源组中查找虚拟机。

``` PowerShell
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
    
进入 `StorageAccounts` 文件夹可以轻松浏览存储资源
    
``` PowerShell 
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> dir

    Directory: Azure:\MySubscriptionNameStorageAccounts\MyStorageAccountName\Files


Name          ConnectionString
----          ----------------
MyFileShare1  \\MyStorageAccountName.file.core.windows.net\MyFileShare1;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare2  \\MyStorageAccountName.file.core.windows.net\MyFileShare2;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare3  \\MyStorageAccountName.file.core.windows.net\MyFileShare3;AccountName=MyStorageAccountName AccountKey=<key>


```

借助连接字符串，可以使用以下命令装载 Azure 文件共享。
        
``` PowerShell
net use <DesiredDriveLetter>: \\<MyStorageAccountName>.file.core.windows.net\<MyFileShareName> <AccountKey> /user:Azure\<MyStorageAccountName>


```

有关详细信息，请参阅[在 Windows 中装载 Azure 文件共享并对其进行访问][azmount]。

还可以按如下所示浏览 Azure 文件共享下的目录：

            
``` PowerShell
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> cd .\MyFileShare1\
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files\MyFileShare1> dir

Mode  Name
----  ----
+     TestFolder
.     hello.ps1

    
```

### <a name="interact-with-virtual-machines"></a>与虚拟机交互

可以通过 `VirtualMachines` 目录查找当前订阅下的所有虚拟机。
    
``` PowerShell
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
 > 请参阅 [Azure VM 的远程管理故障排除](troubleshooting.md#powershell-resolutions)。

  假设有一个名为 MyVM1 的 VM，现在让我们使用 `Invoke-AzureRmVMCommand` 在远程计算机上调用 PowerShell 脚本块。

  ``` Powershell
  Invoke-AzureRmVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -EnableRemoting
  ```
  还可以先导航到 virtualMachines 目录，然后按如下所示运行 `Invoke-AzureRmVMCommand`。

  ``` Powershell
  PS Azure:\> cd MySubscriptionName\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzureRmVMCommand -Scriptblock{Get-ComputerInfo}
  ```
  将显示类似于下面的输出：

  ``` Powershell
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

  ``` Powershell
  Enter-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup -EnableRemoting
  ```

还可以先导航到 `virtualMachines` 目录，然后按如下所示运行 `Enter-AzureRmVM`

  ``` Powershell
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzureRmVM
 ```

### <a name="discover-webapps"></a>发现 WebApps

进入 `WebApps` 文件夹可以轻松浏览存储资源

``` PowerShell
PS Azure:\MySubscriptionName> dir .\WebApps\

    Directory: Azure:\MySubscriptionName\WebApps


Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Stopped  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US



# You can use Azure cmdlets to Start/Stop your web apps for example,
PS Azure:\MySubscriptionName\WebApps> Start-AzureRmWebApp -Name mywebapp1 -ResourceGroupName MyResourceGroup1

Name           State    ResourceGroup        EnabledHostNames                   Location
----           -----    -------------        ----------------                   --------
mywebapp1      Running  MyResourceGroup1     {mywebapp1.azurewebsites.net ...   West US

# Refresh the current state with -force
PS Azure:\MySubscriptionName\WebApps> dir -force

    Directory: Azure:\MySubscriptionName\WebApps


Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Running  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US

```

## <a name="list-available-commands"></a>列出可用命令

在 `Azure` 驱动器下，键入 `Get-AzureRmCommand` 可获取区分上下文的 Azure 命令。

或者，始终可以使用 `Get-Command *azurerm* -Module AzureRM.*` 找出可用的 Azure 命令。

## <a name="install-custom-modules"></a>安装自定义模块

可以运行 `Install-Module` 安装 [PowerShell 库][gallery]中的模块。

## <a name="get-help"></a>Get-Help

键入 `Get-Help` 可获取有关 Azure Cloud Shell 中的 PowerShell 的信息。

``` Powershell
PS Azure:\> Get-Help
```

对于特定的命令，仍可以执行 Get-Help 再后接一个 cmdlet，例如：

``` Powershell
PS Azure:\> Get-Help Get-AzureRmVM
```

## <a name="use-azure-file-storage-to-store-your-data"></a>使用 Azure 文件存储来存储数据

可以创建一个脚本（例如 `helloworld.ps1`）并将其保存到云驱动器，以便在不同的 shell 会话中使用该脚本。

``` Powershell
cd C:\users\ContainerAdministrator\CloudDrive
PS C:\users\ContainerAdministrator\CloudDrive> vim .\helloworld.ps1
# Add the content, such as 'Hello World!'
PS C:\users\ContainerAdministrator\CloudDrive> .\helloworld.ps1
Hello World!
```

下次使用 Cloud Shell 中的 PowerShell 时，`helloworld.ps1` 文件将出现在装载 Azure 文件共享的 `CloudDrive` 文件夹下。

## <a name="use-custom-profile"></a>使用自定义配置文件

可以创建 PowerShell 配置文件 `profile.ps1` 或 `Microsoft.PowerShell_profile.ps1` 来自定义 PowerShell 环境。 将配置文件保存在 `CloudDrive` 下，以便启动 Cloud Shell 时可将其载入每个 PowerShell 会话。

有关如何创建配置文件，请参阅[关于配置文件][profile]。

## <a name="use-git"></a>使用 Git

若要在 CloudShell 中克隆 git 存储库，需要创建[个人访问令牌][githubtoken]并将其用作用户名。 创建令牌后，可如下所示克隆存储库：

 ``` PowerShell
  git clone https://<your-access-token>@github.com/username/repo.git

```
由于注销会话或会话超时时， CloudShell 中的会话不会保留，因此下次登录时 Git 配置文件不会存在。 若要持久保存 Git 配置，必须将 .gitconfig 保存到 `CloudDrive` 并将其复制，或者在启动 `CloudShell` 时创建符号链接。 在 profile.ps1 中使用以下代码片段可创建 `CloudDrive` 的符号链接。

 ``` PowerShell
 
# .gitconfig path relative to this script
$script:gitconfigPath = Join-Path $PSScriptRoot .gitconfig

# Create a symlink to .gitconfig in user's $home
if(Test-Path $script:gitconfigPath){

    if(-not (Test-Path (Join-Path $Home .gitconfig ))){
         New-Item -ItemType SymbolicLink -Path $home -Name .gitconfig -Value $script:gitconfigPath
    }
}

```
## <a name="exit-the-shell"></a>退出 shell

键入 `exit` 可终止会话。

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/about/about_profiles
[azmount]: https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/