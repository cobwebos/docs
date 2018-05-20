---
title: 安装适用于 Azure Stack 的 PowerShell | Microsoft Docs
description: 了解如何安装适用于 Azure Stack 的 PowerShell。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 5/17/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: cb672c8e378db80707db1b0cf77a3196e36b1eb5
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/18/2018
---
# <a name="install-powershell-for-azure-stack"></a>安装适用于 Azure Stack 的 PowerShell

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

需要安装与 Azure Stack 兼容的 Azure PowerShell 模块才能使用 Azure Stack。 本指南逐步演示安装适用于 Azure Stack 的 PowerShell 的步骤。

本文提供有关安装适用于 Azure Stack 的 PowerShell 的详细说明。

> [!Note]
> 以下步骤需要 PowerShell 5.0。 若要检查你的版本，请运行 $PSVersionTable.PSVersion 并比较**主要**版本。

通过 PowerShell 库安装适用于 Azure Stack 的 PowerShell 命令。 你可以使用以下过程验证如果 PSGallery 注册为一个存储库中，打开提升的 PowerShell 会话并运行以下命令：

```PowerShell  
Get-PSRepository -Name "PSGallery"
```

如果未注册存储库中，打开提升的 PowerShell 会话并运行以下命令：

```PowerShell  
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```
> [!Note]  
> 此步骤需要访问 Internet。 

## <a name="uninstall-existing-versions-of-powershell"></a>卸载现有的 PowerShell 版本

在安装之前所需的版本，请确保卸载任何以前安装的 Azure 堆栈 PowerShell 模块。 可使用以下两种方法之一卸载现有版本：

 - 若要卸载现有的 PowerShell 模块，请关闭所有活动的 PowerShell 会话并运行以下命令：

  ```PowerShell
    Uninstall-Module AzureRM.AzureStackAdmin -Force
    Uninstall-Module AzureRM.AzureStackStorage -Force
    Uninstall-Module -Name AzureStack -Force
  ```

 - 从 `C:\Program Files\WindowsPowerShell\Modules` 和 `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules` 文件夹中删除以“Azure”开头的所有文件夹。 删除这些文件夹中删除任何现有的 PowerShell 模块。

以下部分介绍安装适用于 Azure Stack 的 PowerShell 所要执行的步骤。 可在已联网、部分联网或离线场景中运行的 Azure Stack 上安装 PowerShell。

## <a name="install-powershell-in-a-connected-scenario-with-internet-connectivity"></a>在连接方案中安装 PowerShell （具有 Internet 连接）

通过 API 版本配置文件安装与 Azure Stack 兼容的 AzureRM 模块。 Azure Stack 需要 **2017-03-09-profile** API 版本配置文件（可通过安装 AzureRM.Bootstrapper 模块获取）。 有关 API 版本配置文件及其提供的 cmdlet 的详细信息，请参阅[管理 API 版本配置文件](user/azure-stack-version-profiles.md)。 除了 AzureRM 模块以外，还应安装 Azure Stack 特定的 Azure PowerShell 模块。 运行以下 PowerShell 脚本，在开发工作站上安装这些模块：

  ```PowerShell  
# Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
Install-Module -Name AzureRm.BootStrapper 

# Install and import the API Version Profile required by Azure Stack into the current PowerShell session. 
Use-AzureRmProfile -Profile 2017-03-09-profile -Force 

# Install Module Version 1.3.0 if Azure Stack is running 1804 at a minimum 
Install-Module -Name AzureStack -RequiredVersion 1.3.0 

# Install Module Version 1.2.11 if Azure Stack is running a lower version then 1804 
Install-Module -Name AzureStack -RequiredVersion 1.2.11 
  ```

若要确认安装，请运行以下命令：

```PowerShell  
Get-Module -ListAvailable | where-Object {$_.Name -like "Azs*"}
```

如果安装成功，输出中会显示 AzureRM 和 AzureStack 模块。

## <a name="install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>安装 PowerShell 断开连接或部分连接的方案中 （具有有限的 Internet 连接）

中断开连接的情况下，必须首先下载到计算机具有 Internet 连接的 PowerShell 模块，然后将它们传输到安装了 Azure 堆栈开发工具包。

> [!IMPORTANT]  
> Azure 堆栈 1.3.0 PowerShell 模块的版本附带的重大更改的列表。 若要从 1.2.11 升级版本，请参阅[迁移指南](https://aka.ms/azspowershellmigration)。

1. 登录到已建立 Internet 连接的计算机，使用以下脚本将 AzureRM 和 AzureStack 包下载到本地计算机：

   ```PowerShell  
   $Path = "<Path that is used to save the packages>"

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureRM `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.11

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureStack `
     -Path $Path `
     -Force `
     -RequiredVersion 1.3.0 
   ```

  > [!Important]  
  > 如果你没有 Azure 堆栈使用运行更新 1804年或更高版本，更改**requiredversion**参数值以`1.2.11`。 

2. 将下载的包复制到 USB 设备。

3. 登录到工作站，并将包从 USB 设备复制到工作站上的位置。

4. 接下来，必须将此位置注册为默认存储库，并从此存储库安装 AzureRM 和 AzureStack 模块：

   ```PowerShell
   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository `
     -Name $RepoName `
     -SourceLocation $SourceLocation `
     -InstallationPolicy Trusted

   Install-Module AzureRM `
     -Repository $RepoName

   Install-Module AzureStack `
     -Repository $RepoName 
   ```

## <a name="configure-powershell-to-use-a-proxy-server"></a>配置 PowerShell 以使用代理服务器

在需要代理服务器访问 internet 的情况下，你必须首先配置 PowerShell 以使用现有的代理服务器。

1. 打开提升的 PowerShell 提示符。
2. 运行以下命令：

````PowerShell  
  #To use Windows credentials for proxy authentication
  [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

  #Alternatively, to prompt for separate credentials that can be used for #proxy authentication

  [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
````

## <a name="next-steps"></a>后续步骤

 - [从 GitHub 下载 Azure Stack 工具](azure-stack-powershell-download.md)
 - [配置 Azure Stack 用户的 PowerShell 环境](user/azure-stack-powershell-configure-user.md)  
 - [配置 Azure Stack 操作员的 PowerShell 环境](azure-stack-powershell-configure-admin.md) 
 - [在 Azure Stack 中管理 API 版本配置文件](user/azure-stack-version-profiles.md)  
