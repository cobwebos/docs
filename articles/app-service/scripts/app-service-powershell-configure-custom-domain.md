---
title: "Azure PowerShell 脚本示例 - 将自定义域分配到 Web 应用 | Microsoft 文档"
description: "Azure PowerShell 脚本示例 - 将自定义域分配到 Web 应用"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 356f5af9-f62e-411c-8b24-deba05214103
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: a8a0a8ee9ae03afe27898efd1eb0bd15587f34a3
ms.contentlocale: zh-cn
ms.lasthandoff: 04/27/2017

---

# <a name="assign-a-custom-domain-to-a-web-app"></a>将自定义域分配到 Web 应用

此示例脚本使用其相关资源，在应用服务中创建 Web 应用，然后将 `www.<yourdomain>` 映射到它。 

必要时，请使用 [Azure PowerShell 指南](/powershell/azure/overview)中的说明安装 Azure PowerShell，然后运行 `Login-AzureRmAccount` 创建与 Azure 的连接。 此外，还需有权访问域注册机构的 DNS 配置页。

## <a name="sample-script"></a>示例脚本

[!code-powershell[主要](../../../powershell_scripts/app-service/map-custom-domain/map-custom-domain.ps1?highlight=1 "将自定义域分配到 Web 应用")]

## <a name="clean-up-deployment"></a>清理部署 

运行脚本示例后，可以使用以下命令删除资源组、Web 应用以及所有相关资源。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | 创建应用服务计划。 |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | 创建 Web 应用。 |
| [Set-AzureRmAppServicePlan](/powershell/module/azurerm.websites/set-azurermappserviceplan) | 修改应用服务计划以更改其定价层。 |
| [Set-AzureRmWebApp](/powershell/module/azurerm.websites/set-azurermwebapp) | 修改 Web 应用的配置。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure PowerShell 示例](../app-service-powershell-samples.md)中找到 Azure 应用服务 Web 应用的其他 Azure Powershell 示例。

