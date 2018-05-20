---
title: 安装适用于 Azure Stack 的 PowerShell | Microsoft Docs
description: 了解如何安装适用于 Azure Stack 的 PowerShell。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: F8D99A91-15B5-4073-BE07-A43514A6D2CF
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: mabrigg
ms.openlocfilehash: 9d09fb60722865a75ea0825f5ca54f792642980a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
---
# <a name="install-powershell-for-azure-stack"></a>安装适用于 Azure Stack 的 PowerShell

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

需要安装与 Azure Stack 兼容的 Azure PowerShell 模块才能使用 Azure Stack。 本文将指导您完成安装适用于 Azure 堆栈 PowerShell 所需的步骤。 可以通过 Azure Stack 开发工具包或者基于 Windows 的外部客户端（如果已通过 VPN 建立连接）执行本文中所述的步骤。

本文提供了有关安装 PowerShell 的详细的说明。 但是，如果你想要快速安装和配置 PowerShell，你可以使用"使用 PowerShell 获取启动并正在运行"文章中提供的脚本。

> [!NOTE]
> 以下步骤需要 PowerShell 5.0。 若要检查版本，请运行 $PSVersionTable.PSVersion 并比较“Major”（主要）版本。

通过 PowerShell 库安装适用于 Azure Stack 的 PowerShell 命令。 若要注册 PSGallery 存储库，请通过开发工具包或者基于 Windows 的外部客户端（如果已通过 VPN 建立连接）打开权限提升的 PowerShell 会话，然后运行以下命令：

```powershell
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted
```

## <a name="uninstall-existing-versions-of-powershell"></a>卸载现有的 PowerShell 版本

在安装所需版本之前，请务必卸载任何现有 Azure PowerShell 模块。 可使用以下两种方法之一卸载现有版本：

* 若要卸载现有的 PowerShell 模块，请登录到开发工具包或基于 Windows 的外部客户端（如果打算建立 VPN 连接）。 关闭所有活动的 PowerShell 会话，然后运行以下命令：

   ```powershell
   Get-Module -ListAvailable | where-Object {$_.Name -like “Azure*”} | Uninstall-Module
   ```

* 登录到开发工具包或基于 Windows 的外部客户端（如果打算建立 VPN 连接）。 从 `C:\Program Files (x86)\WindowsPowerShell\Modules` 和 `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules` 文件夹中删除以“Azure”开头的所有文件夹。 删除这些文件夹会从“AzureStackAdmin”和“global”用户范围中删除所有现有的 PowerShell 模块。

以下部分介绍安装适用于 Azure Stack 的 PowerShell 所要执行的步骤。 可在已联网、部分联网或离线场景中运行的 Azure Stack 上安装 PowerShell。

## <a name="install-powershell-in-a-connected-scenario-with-internet-connectivity"></a>在连接方案中安装 PowerShell （具有 Internet 连接）

通过 API 版本配置文件安装与 Azure Stack 兼容的 AzureRM 模块。 Azure Stack 需要 **2017-03-09-profile** API 版本配置文件（可通过安装 AzureRM.Bootstrapper 模块获取）。 有关 API 版本配置文件及其提供的 cmdlet 的详细信息，请参阅[管理 API 版本配置文件](azure-stack-version-profiles-powershell.md)。 除了 AzureRM 模块以外，还应安装 Azure Stack 特定的 Azure PowerShell 模块。 运行以下 PowerShell 脚本，在开发工作站上安装这些模块：

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

若要确认安装，请运行以下命令：

  ```powershell
  Get-Module `
    -ListAvailable | where-Object {$_.Name -like “Azure*”}
  ```

  如果安装成功，输出中会显示 AzureRM 和 Azure Stack 模块。

## <a name="install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>在离线或部分联网的场景中（Internet 连接受到限制）安装 PowerShell

在断开连接或部分连接的情况下，必须先将 PowerShell 模块下载到已建立 Internet 连接的计算机，然后将其传送到 Azure Stack 开发工具包进行安装。

1. 登录到已建立 Internet 连接的计算机，使用以下脚本将 AzureRM 和 AzureStack 包下载到本地计算机：

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

3. 登录到开发工具包，将包从 USB 设备复制到开发工具包中的某个位置。

4. 接下来，必须将此位置注册为默认存储库，并从此存储库安装 AzureRM 和 AzureStack 模块：

   ```powershell
   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository `
     -Name $RepoName `
     -SourceLocation $SourceLocation `
     -InstallationPolicy Trusted

   ```powershell
   Install-Module AzureRM `
     -Repository $RepoName

   Install-Module AzureStack `
     -Repository $RepoName
   ```

## <a name="next-steps"></a>后续步骤

* [从 GitHub 下载 Azure Stack 工具](azure-stack-powershell-download.md)
* [配置 Azure Stack 用户的 PowerShell 环境](azure-stack-powershell-configure-user.md)
* [在 Azure Stack 中管理 API 版本配置文件](azure-stack-version-profiles-powershell.md)
