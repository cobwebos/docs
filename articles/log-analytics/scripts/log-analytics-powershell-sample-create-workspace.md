---
title: "Azure PowerShell 脚本示例 - 创建 Log Analytics 工作区 | Microsoft Docs"
description: "Azure PowerShell 脚本示例 - 创建 Log Analytics 工作区"
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
tags: 
ms.assetid: 
ms.service: log-analytics
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: magoedte
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 30d036ae56acc3a798d2776f292243f65cbea43d
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="create-a-log-analytics-workspace-with-powershell"></a>使用 PowerShell 创建 Log Analytics 工作区

利用此脚本，可使用 Azure Log Analytics 工作区快速启动和运行；必须使用此工作区才能开始收集、分析和在数据上执行操作。  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令在订阅中创建新的 Log Analytics 工作区。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [Get-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightsworkspace) | 获取现有工作区的相关信息。 |
| [New-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/new-azurermoperationalinsightsworkspace) | 在指定的资源组和位置中创建一个工作区。 |


## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。


