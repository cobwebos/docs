---
title: "Azure PowerShell 脚本示例 - 缩放具有高可用性体系结构的全球 Web 应用 | Microsoft 文档"
description: "Azure PowerShell 脚本示例 - 缩放具有高可用性体系结构的全球 Web 应用"
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 470f0129-1efe-462c-a029-5c66e04158a8
ms.service: app-service
ms.devlang: multiple
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 9acd1cf4d1a5705811c4dedc545505ec0ac55fc7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="scale-a-web-app-worldwide-with-a-high-availability-architecture"></a>缩放具有高可用性体系结构的全球 Web 应用

在此方案中，将创建一个资源组、两个应用服务计划、两个 Web 应用、一个流量管理器配置文件和两个流量管理器终结点。 完成本练习后，会获得一个高可用性体系结构，它基于最低网络延迟提供 Web 应用的全局可用性。

必要时，请使用 [Azure PowerShell 指南](/powershell/azure/overview)中的说明安装 Azure PowerShell，并运行 `Login-AzureRmAccount` 创建与 Azure 的连接。

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/app-service/scale-geographic/scale-geographic.ps1 "Scale a web app worldwide with a high-availability architecture")]

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
| [New-AzureRMTrafficManagerProfile](/powershell/module/azurerm.trafficmanager/new-azurermtrafficmanagerprofile) | 创建流量管理器配置文件。 |
| [New-AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | 创建应用服务计划。 |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | 创建 Web 应用。 |
| [New-AzureRMTrafficManagerEndpoint](/powershell/module/azurerm.trafficmanager/new-azurermtrafficmanagerendpoint) | 在流量管理器配置文件中创建终结点。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure PowerShell 示例](../app-service-powershell-samples.md)中找到 Azure 应用服务 Web 应用的其他 Azure Powershell 示例。
