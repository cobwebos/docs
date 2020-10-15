---
title: 用于 Azure Monitor 的资源管理器模板示例
description: 使用资源管理器模板部署和配置 Azure Monitor 功能
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: sample
ms.date: 05/18/2020
ms.subservice: ''
ms.openlocfilehash: ab869fc8577d4d1934be96404ded5a2051237bbf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87922722"
---
# <a name="resource-manager-template-samples-for-azure-monitor"></a>用于 Azure Monitor 的资源管理器模板示例

可以使用 [Azure 资源管理器模板](../../azure-resource-manager/templates/template-syntax.md)大规模部署和配置 Azure Monitor。 以下文章提供了适用于不同 Azure Monitor 功能的示例模板。 这些示例可以根据特定要求进行修改，并且可以使用任何标准的资源管理器模板部署方法进行部署。 

## <a name="deploying-the-sample-templates"></a>部署示例模板
使用示例的基本步骤如下：

1. 复制模板并另存为 JSON 文件。
2. 根据环境修改参数并另存为 JSON 文件。
4. 使用[任意资源管理器模板部署方法](../../azure-resource-manager/templates/deploy-powershell.md)部署模板。 

例如，使用以下命令通过 PowerShell 或 Azure CLI 将模板和参数文件部署到资源组中。


```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name AzureMonitorDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

```azurecli
az login
az deployment group create \
    --name AzureMonitorDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file azure-monitor-deploy.json \
    --parameters azure-monitor-deploy.parameters.json
```

## <a name="list-of-sample-templates"></a>示例模板列表

- [代理](resource-manager-agent.md) - 部署和配置 Log Analytics 代理和诊断扩展。
- 警报
  - [日志警报规则](resource-manager-alerts-log.md) - 来自日志查询和 Azure 活动日志的警报。
  - [指标警报规则](resource-manager-alerts-metric.md) - 来自使用不同类型逻辑的指标的警报。
- [Application Insights](resource-manager-app-resource.md)
- [诊断设置](resource-manager-diagnostic-settings.md) - 创建诊断设置以转发来自不同资源类型的日志和指标。
- [日志查询](resource-manager-log-queries.md) - 在 Log Analytics 工作区中创建保存的日志查询。
- [Log Analytics 工作区](resource-manager-workspace.md) - 创建 Log Analytics 工作区，并配置来自 Log Analytics 代理的不同数据源的集合。
- [工作簿](resource-manager-workbooks.md) - 创建工作簿。
- [用于容器的 Azure Monitor](resource-manager-container-insights.md) - 将群集加入用于容器的 Azure Monitor。
- [用于 VM 的 Azure Monitor](resource-manager-vminsights.md) - 将虚拟机加入用于 VM 的 Azure Monitor。



## <a name="next-steps"></a>后续步骤

- 详细了解[资源管理器模板](../../azure-resource-manager/templates/overview.md)