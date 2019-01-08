---
title: 在 Azure Stack 中将 API 版本配置文件与 PowerShell 配合使用 | Microsoft Docs
description: 了解如何在 Azure Stack 中将 API 版本配置文件与 PowerShell 配合使用。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: EBAEA4D2-098B-4B5A-A197-2CEA631A1882
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 890ca3abf08e280dcf0a48355ab1dea87331805b
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063448"
---
# <a name="use-api-version-profiles-for-powershell-in-azure-stack"></a>在 Azure Stack 中使用 PowerShell 的 API 版本配置文件

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

API 版本配置文件提供一种管理 Azure 与 Azure Stack 之间版本差异的方式。 API 版本配置文件是一组具有特定 API 版本 AzureRM PowerShell 模块。 每个云平台都有一组支持的 API 版本配置文件。 例如，Azure Stack 支持带有特定日期的配置文件版本（例如 **2018-03-01-hybrid**），而 Azure 则支持**最新的** API 版本配置文件。 安装配置文件时，会安装与指定的配置文件对应的 AzureRM PowerShell 模块。

## <a name="install-the-powershell-module-required-to-use-api-version-profiles"></a>安装使用 API 版本配置文件所需的 PowerShell 模块

可通过 PowerShell 库获得的 **AzureRM.Bootstrapper** 模块会提供使用 API 版本配置文件所需的 PowerShell cmdlet。 使用以下 cmdlet 安装 **AzureRM.Bootstrapper** 模块：

```PowerShell
Install-Module -Name AzureRm.BootStrapper
```

## <a name="azure-stack-version-and-profile-versions"></a>Azure Stack 版本和配置文件版本

下表列出了最近发布的 Azure Stack 所需的 API 配置文件版本和所用的 PowerShell 管理员模块名字对象。 如果你对 1808 以前版本使用本文，请使用正确的值更新版本配置文件和名字对象。

| 版本号 | API 版本配置文件 | PS 管理员模块名字对象 |
| --- | --- | --- |
| 1808 或更高版本 | 2018-03-01-hybrid | 1.5.0 |
| 1804 或更高版本 | 2017-03-09-profile | 1.4.0 |
| 1804 以前的版本 | 2017-03-09-profile | 1.2.11 |

> [!NOTE]  
> 若要从 1.2.11 版升级，请参阅[迁移指南](https://aka.ms/azpsh130migration)。

## <a name="install-a-profile"></a>安装配置文件

使用 **Install-AzureRmProfile** cmdlet 搭配 **2018-03-01-hybrid** API 版本配置文件，安装 Azure Stack 所需的 AzureRM 模块。 Azure Stack 操作员模块并不会随此 API 版本配置文件一起安装。 应该根据[安装适用于 Azure Stack 的 PowerShell](../azure-stack-powershell-install.md) 一文的“步骤 3”中所述，单独安装这些模块。

```PowerShell
Install-AzureRMProfile -Profile 2018-03-01-hybrid
```

## <a name="install-and-import-modules-in-a-profile"></a>安装并导入配置文件中的模块

使用 **Use-AzureRmProfile** cmdlet 安装并导入与 API 版本配置文件关联的模块。 在一个 PowerShell 会话中只能导入一个 API 版本配置文件。 若要导入不同的 API 版本配置文件，必须打开新的 PowerShell 会话。 **Use-azurermprofile** cmdlet 将执行以下任务：

1. 检查当前范围中是否已安装与所指定 API 版本配置文件关联的 PowerShell 模块。  
2. 下载并安装这些模块（如果尚未安装）。
3. 将模块导入到当前的 PowerShell 会话中。

```PowerShell
# Installs and imports the specified API version profile into the current PowerShell session.
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Scope CurrentUser

# Installs and imports the specified API version profile into the current PowerShell session without any prompts
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Scope CurrentUser -Force
```

若要从某个 API 版本配置文件安装并导入选定的 AzureRM 模块，请搭配 **Module** 参数运行 **Use-AzureRMProfile** cmdlet：

```PowerShell
# Installs and imports the compute, storage and network modules from the specified API version profile into your current PowerShell session.
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Module AzureRM.Compute, AzureRM.Storage, AzureRM.Network
```

## <a name="get-the-installed-profiles"></a>获取已安装的配置文件

使用 **Get-AzureRmProfile** cmdlet 获取可用的 API 版本配置文件列表：

```PowerShell
# lists all API version profiles provided by the AzureRM.BootStrapper module.
Get-AzureRmProfile -ListAvailable

# lists the API version profiles which are installed on your machine
Get-AzureRmProfile
```

## <a name="update-profiles"></a>更新配置文件

使用 **Update-AzureRmProfile** cmdlet 将 API 版本配置文件中的模块更新为 PSGallery 中可用的最新版模块。 建议始终运行**Update-azurermprofile**以避免冲突，导入模块时的新 PowerShell 会话中的 cmdlet。 **Update-AzureRmProfile** cmdlet 会运行以下任务：

1. 检查当前范围的给定 API 版本配置文件中是否已安装最新版模块。  
2. 提示安装这些模块（如果尚未安装）。  
3. 将已更新的模块安装并导入到当前的 PowerShell 会话中。  

```PowerShell
Update-AzureRmProfile -Profile 2018-03-01-hybrid
```

<!-- To remove the previously installed versions of the modules before updating to the latest available version, use the Update-AzureRmProfile cmdlet along with the **-RemovePreviousVersions** parameter:

```PowerShell 
Update-AzureRmProfile -Profile 2018-03-01-hybrid -RemovePreviousVersions
``` -->

此 cmdlet 运行以下任务：  

1. 检查当前范围的给定 API 版本配置文件中是否已安装最新版模块。  
2. 从当前的 API 版本配置文件和当前 PowerShell 会话中删除旧版模块。  
3. 将提示您安装最新版本。  
4. 将已更新的模块安装并导入到当前的 PowerShell 会话中。  

## <a name="uninstall-profiles"></a>卸载配置文件

使用 **Uninstall-AzureRmProfile** cmdlet 卸载指定的 API 版本配置文件。

```PowerShell
Uninstall-AzureRmProfile -Profile  2018-03-01-hybrid
```

## <a name="next-steps"></a>后续步骤

* [安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)
* [配置 Azure Stack 用户的 PowerShell 环境](azure-stack-powershell-configure-user.md)  
