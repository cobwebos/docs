---
title: "安装适用于 Azure 堆栈 PowerShell |Microsoft 文档"
description: "了解如何安装适用于 Azure 堆栈 PowerShell。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 0CDD8B9B-EC32-4453-918A-D0A606C7BC10
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/23/2018
ms.author: mabrigg
ms.openlocfilehash: 18a697813fbeb11be7a599359285f824ed804216
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2018
---
# <a name="install-powershell-for-azure-stack"></a>安装适用于 Azure 堆栈 PowerShell  

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

Azure 堆栈兼容 Azure PowerShell 模块需要使用 Azure 堆栈处理。 在本指南中，我们引导你完成安装适用于 Azure 堆栈 PowerShell 所需的步骤。 你可以使用如果你通过 VPN 连接从 Azure 堆栈开发工具包中，或从基于 Windows 的外部客户端此文章中所述的步骤。

本文提供了详细说明安装适用于 Azure 堆栈 PowerShell。 但是，如果你想要快速安装和配置 PowerShell，你可以使用中提供的脚本[使用 PowerShell 获取启动并正在运行](azure-stack-powershell-configure-quickstart.md)主题。 

> [!NOTE]
> 以下步骤需要 PowerShell 5.0。 若要检查你的版本，请运行 $PSVersionTable.PSVersion 并将"主要"版本进行比较。

通过 PowerShell 库安装 Azure 堆栈的 PowerShell 命令。 若要注册 PSGallery 存储库，请从开发工具包或基于 Windows 的外部客户端通过打开提升的 PowerShell 会话如果你通过 VPN 连接，并运行以下命令：

```powershell
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted
```

## <a name="uninstall-existing-versions-of-powershell"></a>卸载现有版本的 PowerShell

在安装之前所需的版本，请确保卸载任何现有的 Azure PowerShell 模块。 你可以卸载它们通过使用以下两种方法之一：

* 若要卸载现有的 PowerShell 模块，请登录到开发工具包中，或基于 Windows 的外部客户端如果你打算建立 VPN 连接。 关闭所有活动的 PowerShell 会话并运行以下命令： 

   ```powershell
   Get-Module -ListAvailable | where-Object {$_.Name -like “Azure*”} | Uninstall-Module
   ```

* 登录到开发工具包中，或基于 Windows 的外部客户端如果你打算建立 VPN 连接。 从删除以"Azure"开头的所有文件夹`C:\Program Files\WindowsPowerShell\Modules`和`C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules`文件夹。 删除这些文件夹中删除任何现有的 PowerShell 模块，通过"AzureStackAdmin"和"全局"用户作用域。 

以下各节描述了安装适用于 Azure 堆栈 PowerShell 所需的步骤。 连接，部分连接，在中运行的 Azure 堆栈上或在断开连接的情况下，可以安装 PowerShell。 

## <a name="install-powershell-in-a-connected-scenario-with-internet-connectivity"></a>在连接方案中安装 PowerShell （具有 internet 连接）

通过 API 版本配置文件安装了 azure 堆栈兼容 AzureRM 模块。 Azure 堆栈需要**自 2017 年 1-03-09-配置文件**API 版本配置中，数据文件可通过安装 AzureRM.Bootstrapper 模块。 若要了解有关 API 版本配置文件和它们所提供的 cmdlet，请参阅[管理 API 版本配置文件](azure-stack-version-profiles.md)。 除了 AzureRM 模块中，你还应安装的 Azure 堆栈特定 PowerShell 模块。 运行下面的 PowerShell 脚本，以在开发工作站上安装这些模块：

> [!IMPORTANT]
> AzureRM 1.2.11 PowerShell 模块的版本附带的重大更改的列表。 若要从 1.2.10 升级版本，请参阅迁移指南： [https://aka.ms/azspowershellmigration](https://aka.ms/azspowershellmigration)。

  ```powershell
  # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
  Install-Module `
    -Name AzureRm.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile `
    -Profile 2017-03-09-profile -Force

  Install-Module `
    -Name AzureStack `
    -RequiredVersion 1.2.11
  ```

若要确认安装，运行以下命令：

  ```powershell
  Get-Module `
    -ListAvailable | where-Object {$_.Name -like “Azure*”}
  ```
  如果安装不成功，将 AzureRM 和 AzureStack 模块将显示在输出中。

## <a name="install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>安装 PowerShell 断开连接或部分连接的方案中 （具有有限的 internet 连接）

中断开连接的情况下，必须首先下载到计算机具有 internet 连接的 PowerShell 模块，然后将它们传输到安装了 Azure 堆栈开发工具包。

> [!IMPORTANT]
> AzureRM 1.2.11 PowerShell 模块的版本附带的重大更改的列表。 若要从 1.2.10 升级版本，请参阅迁移指南： [https://aka.ms/azspowershellmigration](https://aka.ms/azspowershellmigration)。

1. 登录到的计算机，其中具有 internet 连接并到本地计算机上使用以下脚本下载 AzureRM，和 AzureStack 包：

   ```powershell
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
     -RequiredVersion 1.2.11 
   ```

2. 将下载的包复制到 USB 设备。

3. 登录到开发工具包，然后将包从 USB 设备复制到上开发工具包的位置。 

4. 现在你必须注册此位置用作默认存储库并从该存储库安装 AzureRM 和 AzureStack 模块：

   ```powershell
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

## <a name="next-steps"></a>后续步骤

* [从 GitHub 下载 Azure 堆栈工具](azure-stack-powershell-download.md)
* [配置 Azure 堆栈用户 PowerShell 环境](user/azure-stack-powershell-configure-user.md)  
* [配置 Azure 堆栈运算符 PowerShell 环境](azure-stack-powershell-configure-admin.md) 
* [管理 Azure 堆栈中的 API 版本配置文件](azure-stack-version-profiles.md)  
