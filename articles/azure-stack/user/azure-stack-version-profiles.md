---
title: "管理 Azure 堆栈中的 API 版本配置文件 |Microsoft 文档"
description: "了解 Azure 堆栈中的 API 版本配置文件"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 6B749785-DCF5-4AD8-B808-982E7C6BBA0E
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: mabrigg
ms.openlocfilehash: d86a54ea9e165269131eb961df7f74703f0ec6ff
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>管理 Azure 堆栈中的 API 版本配置文件

Azure App Service 版本配置文件的 API 功能提供了如何管理 Azure 和 Azure 堆栈之间的版本差异。 API 版本配置文件是一套用于特定的 API 版本 AzureRM PowerShell 模块。 每个云平台都有一组受支持的 API 版本配置文件。 例如，Azure 堆栈支持的特定，发布日期为配置文件版本，如**自 2017 年 1-03-09-配置文件**，Azure 支持和*最新*API 版本配置文件。 安装配置文件，则也将同时安装对应于指定的配置文件的 AzureRM PowerShell 模块。

## <a name="install-the-powershell-module-required-to-use-api-version-profiles"></a>安装使用 API 版本配置文件所需的 PowerShell 模块

**AzureRM.Bootstrapper**可通过 PowerShell 库的模块提供 PowerShell cmdlet 所需使用 API 版本配置文件。 使用以下 cmdlet 来安装**AzureRM.Bootstrapper**模块：

```PowerShell
Install-Module -Name AzureRm.BootStrapper
```
**AzureRM.Bootstrapper**模块是预览版，因此详细信息和功能可能有所更改。 若要下载并安装此模块的最新版本从 PowerShell 库，运行以下 cmdlet:

```PowerShell
Update-Module -Name "AzureRm.BootStrapper"
```

## <a name="install-a-profile"></a>安装配置文件

使用**安装 AzureRmProfile** cmdlet 与**自 2017 年 1-03-09-配置文件**API 版本配置文件以安装 Azure 堆栈所需的 AzureRM 模块。 

>[!NOTE]
>Azure 堆栈云管理员模块未安装与此 API 版本配置文件中。 应安装管理员模块中的第 3 步单独指定[安装适用于 Azure 堆栈的 PowerShell](azure-stack-powershell-install.md)文章。

```PowerShell 
Install-AzureRMProfile -Profile 2017-03-09-profile
```
## <a name="install-and-import-modules-in-a-profile"></a>安装和导入的配置文件中的模块

使用**使用 AzureRmProfile** cmdlet 来安装和导入 API 版本配置文件关联的模块。 你可以在 PowerShell 会话中导入只有一个 API 版本配置。 若要导入不同的 API 版本配置文件，必须打开新的 PowerShell 会话。 **使用 AzureRMProfile** cmdlet 运行以下任务：  
1. 检查以查看当前作用域中是否安装了与指定的 API 版本配置文件关联的 PowerShell 模块。  
2. 下载并安装模块，如果尚未安装。   
3. 将模块导入当前 PowerShell 会话。 

```PowerShell
# Installs and imports the specified API version profile into the current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser

# Installs and imports the specified API version profile into the current PowerShell session without any prompts.
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser -Force
```

若要安装，并从 API 版本配置文件导入选定的 AzureRM 模块，运行**使用 AzureRMProfile** cmdlet 与*模块*参数：

```PowerShell
# Installs and imports the Compute, Storage, and Network modules from the specified API version profile into your current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Module AzureRM.Compute, AzureRM.Storage, AzureRM.Network
```

## <a name="get-the-installed-profiles"></a>获取已安装的配置文件

使用**Get AzureRmProfile** cmdlet 来获取可用的 API 版本配置文件列表： 

```PowerShell
# Lists all API version profiles provided by the AzureRM.BootStrapper module.
Get-AzureRmProfile -ListAvailable 

# Lists the API version profiles that are installed on your machine.
Get-AzureRmProfile
```
## <a name="update-profiles"></a>更新配置文件

使用**更新 AzureRmProfile** cmdlet 更新到 PowerShell 库中可用的模块的最新版本的 API 版本配置文件中的模块。 我们建议你运行**更新 AzureRmProfile**中新的 PowerShell 会话，以避免冲突，当你导入模块的 cmdlet。 **更新 AzureRmProfile** cmdlet 运行以下任务：

1. 检查以查看当前作用域的给定 API 版本配置文件中是否安装的模块的最新版本。  
2. 将提示您安装的模块，如果尚未安装。  
3. 安装和更新的模块导入到当前 PowerShell 会话。  

```PowerShell
Update-AzureRmProfile -Profile 2017-03-09-profile
```

若要删除的模块以前安装的版本之后更新为最新的可用版本,，使用**更新 AzureRmProfile** cmdlet 和*-RemovePreviousVersions*参数：

```PowerShell 
Update-AzureRmProfile -Profile 2017-03-09-profile -RemovePreviousVersions
```

此 cmdlet 将运行以下任务：  

1. 检查以查看当前作用域的给定 API 版本配置文件中是否安装的模块的最新版本。  
2. 从当前 API 版本配置文件和当前 PowerShell 会话中删除的模块的较旧版本。  
3. 会提示你安装最新版本的模块。  
4. 安装和更新的模块导入到当前 PowerShell 会话。  
 
## <a name="uninstall-profiles"></a>卸载配置文件

使用**卸载 AzureRmProfile** cmdlet 以卸载指定的 API 版本配置文件：

```PowerShell 
Uninstall-AzureRmProfile -Profile 2017-03-09-profile
```

## <a name="next-steps"></a>后续步骤
* [安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)
* [配置 Azure 堆栈用户 PowerShell 环境](azure-stack-powershell-configure-user.md)  
