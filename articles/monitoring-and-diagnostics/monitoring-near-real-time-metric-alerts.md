---
title: "Azure Monitor 中的准实时指标警报 | Microsoft Docs"
description: "了解如何使用准实时指标警报以小到 1 分钟的频率监视 Azure 资源指标。"
author: snehithm
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: snmuvva
ms.custom: 
ms.openlocfilehash: 6370f4596e6b20962c6de0dbcbd5f86c3b7777cc
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2018
---
# <a name="near-real-time-metric-alerts-preview"></a>准实时指标警报（预览版）
Azure Monitor 支持一种称为“准实时指标警报 (预览版)”的新警报类型。 此功能目前处于公共预览状态。

准实时指标警报与常规指标警报在以下方面有所不同：

- **改进了延迟**：准实时指标警报可以以小到 1 分钟的频率监视指标值的变化。
- **更好地控制指标条件**：可以在准实时指标警报中定义更丰富的警报规则。 这些警报支持监视指标的最大值、最小值、平均值和总值。
- **综合监视多个指标**：准实时指标警报可以使用单个规则监视多个指标（目前最多为两个指标）。 如果两个指标在指定时间段内违反其各自的阈值，则会触发警报。
- **模块化通知系统**：准实时指标警报使用[操作组](monitoring-action-groups.md)。 可以使用操作组创建模块化操作。 可以对多个警报规则重复使用操作组。

> [!NOTE]
> 准实时指标警报功能目前处于公共预览状态。 功能和用户体验可能会发生变化。
>

## <a name="resources-you-can-use-with-near-real-time-metric-alerts"></a>可用于准实时指标警报的资源
下面是准实时指标警报支持的资源类型的完整列表：

* Microsoft.ApiManagement/service
* Microsoft.Automation/automationAccounts
* Microsoft.Batch/batchAccounts
* Microsoft.Cache/Redis
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets
* Microsoft.DataFactory/factories
* Microsoft.DBforMySQL/servers
* Microsoft.DBforPostgreSQL/servers
* Microsoft.EventHub/namespaces
* Microsoft.Logic/workflows
* Microsoft.Network/applicationGateways
* Microsoft.Network/publicipaddresses
* Microsoft.Search/searchServices
* Microsoft.ServiceBus/namespaces
* Microsoft.Storage/storageAccounts
* Microsoft.Storage/storageAccounts/services
* Microsoft.StreamAnalytics/streamingjobs
* Microsoft.CognitiveServices/accounts

## <a name="near-real-time-metric-alerts-for-metrics-that-use-dimensions"></a>使用维度的指标的准实时指标警报
准实时指标警报支持针对使用维度的指标发出警报。 可以使用维度将指标筛选到适当级别。 以下资源类型支持针对使用维度的指标的准实时指标警报：

* Microsoft.ApiManagement/service
* Microsoft.Storage/storageAccounts（仅美国区域中的存储帐户支持）
* Microsoft.Storage/storageAccounts/services（仅美国区域中的存储帐户支持）

## <a name="create-a-near-real-time-metric-alert"></a>创建准实时指标警报
目前，只能在 Azure 门户中创建准实时指标警报。 即将推出通过使用 PowerShell、Azure 命令行接口 (Azure CLI) 和 Azure Monitor REST API 配置准实时指标警报的支持功能。

创建准实时指标警报的体验已移动到新的“警报(预览)”页。 即使当前“警报”页显示了“添加准实时指标警报”，也会将你重定向到“警报(预览)”页。

若要了解如何创建准实时指标警报，请参阅[在 Azure 门户中创建警报规则](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal)。

## <a name="manage-near-real-time-metric-alerts"></a>管理准实时指标警报
创建准实时指标警报后，可以按照[在 Azure 门户中管理警报](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal)中所述的步骤管理警报。

## <a name="payload-schema"></a>负载架构

POST 操作对于所有准实时指标警报包含以下 JSON 有效负载和架构：

```json
{
    "WebhookName": "Alert1510875839452",
    "RequestBody": {
        "status": "Activated",
        "context": {
            "condition": {
                "metricName": "Percentage CPU",
                "metricUnit": "Percent",
                "metricValue": "17.7654545454545",
                "threshold": "1",
                "windowSize": "10",
                "timeAggregation": "Average",
                "operator": "GreaterThan"
            },
            "resourceName": "ContosoVM1",
            "resourceType": "microsoft.compute/virtualmachines",
            "resourceRegion": "westus",
            "portalLink": "https://portal.azure.com/#resource/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/automationtest/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
            "timestamp": "2017-11-16T23:54:03.9517451Z",
            "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/microsoft.insights/alertrules/VMMetricAlert1",
            "name": "VMMetricAlert1",
            "description": "A metric alert for the VM Win2012R2",
            "conditionType": "Metric",
            "subscriptionId": "00000000-0000-0000-0000-000000000000",
            "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
            "resourceGroupName": "ContosoVM"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
    },
    "RequestHeader": {
        "Connection": "Keep-Alive",
        "Host": "s1events.azure-automation.net",
        "User-Agent": "azure-insights/0.9",
        "x-ms-request-id": "00000000-0000-0000-0000-000000000000"
    }
}
```

## <a name="next-steps"></a>后续步骤

* 了解有关新的[“警报(预览)”体验](monitoring-overview-unified-alerts.md)的详细信息。
* 了解 [Azure 警报（预览）中的日志警报](monitor-alerts-unified-log.md)。
* 了解 [Azure 中的警报](monitoring-overview-alerts.md)。