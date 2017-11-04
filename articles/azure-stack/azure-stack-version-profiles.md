---
title: "在 Azure 堆栈中使用 API 版本配置文件 |Microsoft 文档"
description: "了解有关 Azure 堆栈中的 API 版本配置文件。"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: sngun
ms.openlocfilehash: b9a010409dc7f49333ab188b89280f3ad54816c1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>管理 Azure 堆栈中的 API 版本配置文件

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

API 版本配置文件提供一种方法来管理 Azure 和 Azure 堆栈之间的版本差异。 API 版本配置文件是一套用于特定的 API 版本 AzureRM PowerShell 模块。 每个云平台都有一组受支持的 API 版本配置文件。 例如，Azure 堆栈支持特定过期的配置文件版本如**自 2017 年 1-03-09-配置文件**，Azure 支持和**最新**API 版本配置文件。 安装配置文件，则也将同时安装对应于指定的配置文件的 AzureRM PowerShell 模块。

## <a name="install-the-powershell-module-required-to-use-api-version-profiles"></a>安装使用 API 版本配置文件所需的 PowerShell 模块

**AzureRM.Bootstrapper**可通过 PowerShell 库的模块提供 PowerShell cmdlet 所需使用 API 版本配置文件。 使用以下 cmdlet 来安装 AzureRM.Bootstrapper 模块：

```PowerShell
Install-Module -Name AzureRm.BootStrapper
```
AzureRM.Bootstrapper 模块是在预览，则为详细信息和功能可能有所更改。 若要下载并安装此模块的最新版本从 PowerShell 库，运行以下 cmdlet:

```PowerShell
Update-Module -Name "AzureRm.BootStrapper"
```

## <a name="install-a-profile"></a>安装配置文件

使用**安装 AzureRmProfile** cmdlet 与**自 2017 年 1-03-09-配置文件**API 版本配置文件以安装 Azure 堆栈所需的 AzureRM 模块。 请注意，Azure 堆栈运算符模块不会安装与此 API 版本配置文件，它们应安装单独的步骤 3 中指定[安装适用于 Azure 堆栈的 PowerShell](azure-stack-powershell-install.md)文章。

```PowerShell 
Install-AzureRMProfile -Profile 2017-03-09-profile
```
## <a name="install-and-import-modules-in-a-profile"></a>安装和导入的配置文件中的模块

使用**使用 AzureRmProfile** cmdlet 来安装和导入 API 版本配置文件关联的模块。 你可以在 PowerShell 会话中导入只有一个 API 版本配置。 若要导入不同的 API 版本配置文件，必须打开新的 PowerShell 会话。 使用 AzureRMProfile cmdlet 将运行以下任务：  
1. 检查是否将与指定的 API 版本配置文件关联的 PowerShell 模块安装在当前范围内。  
2. 下载并安装模块，如果尚未安装。   
3. 将模块导入当前 PowerShell 会话。 

```PowerShell
# Installs and imports the specified API version profile into the current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser

# Installs and imports the specified API version profile into the current PowerShell session without any prompts
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser -Force
```

若要安装并从 API 版本配置文件导入选定的 AzureRM 模块，运行使用 AzureRMProfile cmdlet 与**模块**参数：

```PowerShell
# Installs and imports the compute, Storage and Network modules from the specified API version profile into your current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Module AzureRM.Compute, AzureRM.Storage, AzureRM.Network
```

## <a name="get-the-installed-profiles"></a>获取已安装的配置文件

使用**Get AzureRmProfile** cmdlet 来获取可用的 API 版本配置文件列表： 

```PowerShell
# lists all API version profiles provided by the AzureRM.BootStrapper module.
Get-AzureRmProfile -ListAvailable 

# lists the API version profiles which are installed on your machine
Get-AzureRmProfile
```
## <a name="update-profiles"></a>更新配置文件

使用**更新 AzureRmProfile** cmdlet 可更新的 API 版本配置文件中的模块的模块 PSGallery 中可用的最新版本。 建议你先始终运行**更新 AzureRmProfile**中新的 PowerShell 会话，以避免冲突，导入模块时的 cmdlet。 更新 AzureRmProfile cmdlet 将运行以下任务：

1. 检查是否当前作用域的给定 API 版本配置文件中安装模块的最新版本。  
2. 将提示您安装如果尚未安装。  
3. 安装和更新的模块导入到当前 PowerShell 会话。  

```PowerShell
Update-AzureRmProfile -Profile 2017-03-09-profile
```

若要在更新到最新的可用版本之前删除以前安装的版本的模块，请使用与更新 AzureRmProfile cmdlet **-RemovePreviousVersions**参数：

```PowerShell 
Update-AzureRmProfile -Profile 2017-03-09-profile -RemovePreviousVersions
```

此 cmdlet 将运行以下任务：  

1. 检查是否当前作用域的给定 API 版本配置文件中安装模块的最新版本。  
2. 从当前 API 版本配置文件和当前 PowerShell 会话中删除模块的较旧版本。  
4. 会提示你安装最新版本。  
5. 安装和更新的模块导入到当前 PowerShell 会话。  
 
## <a name="uninstall-profiles"></a>卸载配置文件

使用**卸载 AzureRmProfile** cmdlet 以卸载指定的 API 版本配置文件。

```PowerShell 
Uninstall-AzureRmProfile -Profile 2017-03-09-profile
```

## <a name="next-steps"></a>后续步骤
* [安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)
* [配置 Azure 堆栈用户 PowerShell 环境](user/azure-stack-powershell-configure-user.md)  
