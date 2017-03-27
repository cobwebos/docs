---
title: "Azure PowerShell 脚本示例 - 使用 Web 服务器日志监视 Web 应用 | Microsoft 文档"
description: "Azure PowerShell 脚本示例 - 使用 Web 服务器日志监视 Web 应用"
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 5805d7cd-9e56-4eba-bd85-75b013690ff5
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/08/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: ccadd21df02d58fac8eac8e3910092c4d8374504
ms.lasthandoff: 03/10/2017

---

# <a name="monitor-a-web-app-with-web-server-logs"></a>使用 Web 服务器日志监视 Web 应用

在此方案中，将创建资源组、应用服务计划、Web 应用，并配置 Web 应用以启用 Web 服务器日志。 然后，将下载日志文件以供查看。

在运行此脚本前，请确保已使用 `Login-AzureRmAccount` cmdlet 创建与 Azure 的连接。

## <a name="sample-script"></a>示例脚本

[!code-powershell[主要](../../../powershell_scripts/app-service/monitor-with-logs/monitor-with-logs.ps1 "使用 Web 服务器日志监视 Web 应用")]

## <a name="clean-up-deployment"></a>清理部署 

运行脚本示例后，可以使用以下命令删除资源组、Web 应用以及所有相关资源。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Resources/v3.5.0/new-azurermresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzureRmAppServicePlan](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermappserviceplan) | 创建应用服务计划。 |
| [New-AzureRmWebApp](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermwebapp) | 创建 Web 应用。 |
| [Set-AzureRmWebApp](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/set-azurermwebapp) | 修改 Web 应用的配置。 |
| [Get-AzureRMWebAppMetrics](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/get-azurermwebappmetrics) | 获取 Web 应用的指标。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)。

可以在 [Azure PowerShell 示例](../app-service-powershell-samples.md)中找到 Azure 应用服务 Web 应用的其他 Azure Powershell 示例。

