---
title: Azure Monitor 中的准实时指标警报 | Microsoft Docs
description: 了解如何使用准实时指标警报以小到 1 分钟的频率监视 Azure 资源指标。
author: snehithm
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: snmuvva, vinagara
ms.custom: ''
ms.openlocfilehash: 88995b1f3350fe485e28efccc93779ae0a42eb97
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="near-real-time-metric-alerts-preview"></a>准实时指标警报（预览版）
Azure Monitor 支持一种称为“准实时指标警报 (预览版)”的新警报类型。 此功能目前处于公共预览状态。

准实时指标警报与常规指标警报在以下方面有所不同：

- **改进了延迟**：准实时指标警报可以以小到 1 分钟的频率监视指标值的变化。
- **更好地控制指标条件**：可以在准实时指标警报中定义更丰富的警报规则。 这些警报支持监视指标的最大值、最小值、平均值和总值。
- **日志中的指标**：从传入 [Log Analytics](../log-analytics/log-analytics-overview.md) 的常用日志数据中，可将指标提取到 Azure Monitor 中，并收到准实时警报
- **综合监视多个指标**：准实时指标警报可以使用单个规则监视多个指标（目前最多为两个指标）。 如果两个指标在指定时间段内违反其各自的阈值，则会触发警报。
- **模块化通知系统**：准实时指标警报使用[操作组](monitoring-action-groups.md)。 可以使用操作组创建模块化操作。 可以对多个警报规则重复使用操作组。

> [!NOTE]
> 准实时指标警报目前处于公共预览状态。 并且来自日志功能的指标处于“有限”公共预览状态。 功能和用户体验可能会发生变化。
>

## <a name="metrics-and-dimensions-supported"></a>指标和维度支持
准实时指标警报支持针对使用维度的指标发出警报。 可以使用维度将指标筛选到适当级别。 所有受支持的指标以及适用的维度都可以从 [Azure Monitor - 指标资源管理器（预览）](monitoring-metric-charts.md)中进行浏览和可视化。

下面是支持准实时指标警报且基于 Azure Monitor 的指标资源的完整列表：

|指标名称/详细信息  |支持的维度  |
|---------|---------|
|Microsoft.ApiManagement/service     | 是        |
|Microsoft.Automation/automationAccounts     |     不适用    |
|Microsoft.Automation/automationAccounts     |   不适用      |
|Microsoft.Cache/Redis     |    不适用     |
|Microsoft.Compute/virtualMachines     |    不适用     |
|Microsoft.Compute/virtualMachineScaleSets     |   不适用      |
|Microsoft.DataFactory/factories     |   不适用      |
|Microsoft.DBforMySQL/servers     |   不适用      |
|Microsoft.DBforPostgreSQL/servers     |    不适用     |
|Microsoft.EventHub/namespaces     |   不适用      |
|Microsoft.Logic/workflows     |     不适用    |
|Microsoft.Network/applicationGateways     |    不适用     |
|Microsoft.Network/publicipaddresses     |  不适用       |
|Microsoft.Search/searchServices     |   不适用      |
|Microsoft.ServiceBus/namespaces     |  不适用       |
|Microsoft.Storage/storageAccounts     |    是     |
|Microsoft.Storage/storageAccounts/services     |     是    |
|Microsoft.StreamAnalytics/streamingjobs     |  不适用       |
|Microsoft.CognitiveServices/accounts     |    不适用     |


来自于日志的指标当前支持以下常用 OMS 日志：
- 适用于 Windows 和 Linux 计算机的[性能计数器](../log-analytics/log-analytics-data-sources-performance-counters.md)
- 计算机的检测信号记录
- [更新管理](../operations-management-suite/oms-solution-update-management.md)记录

下面是支持准实时指标警报且基于 OMS 日志的指标资源的完整列表：

指标名称/详细信息  |支持的维度  | 日志类型  |
|---------|---------|---------|
|Average_Avg. 磁盘秒数/读取     |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Windows 性能计算器      |
| Average_Avg. 磁盘秒数/写入     |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Windows 性能计算器      |
| Average_Current Disk Queue Length   |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Windows 性能计算器      |
| Average_Disk Reads/sec    |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Windows 性能计算器      |
| Average_Disk Transfers/sec    |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Windows 性能计算器      |
|   Average_% Free Space    |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Windows 性能计算器      |
| Average_Available MBytes     |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Windows 性能计算器      |
| Average_% Committed Bytes In Use    |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Windows 性能计算器      |
| Average_Bytes Received/sec    |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Windows 性能计算器      |
|  Average_Bytes Sent/sec    |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Windows 性能计算器      |
|  Average_Bytes Total/sec    |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Windows 性能计算器      |
|  Average_% Processor Time    |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Windows 性能计算器      |
|   Average_Processor Queue Length    |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Windows 性能计算器      |
|   Average_% Free Inodes   |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_% Free Space   |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_% Used Inodes  |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_% Used Space   |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Disk Read Bytes/sec    |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Disk Reads/sec |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Disk Transfers/sec |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Disk Write Bytes/sec   |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Disk Writes/sec    |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Free Megabytes |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Logical Disk Bytes/sec |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_% Available Memory |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_% Available Swap Space |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_% Used Memory  |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_% Used Swap Space  |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Available MBytes Memory    |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Available MBytes Swap  |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Page Reads/sec |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Page Writes/sec    |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Pages/sec  |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Used MBytes Swap Space |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Used Memory MBytes |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Total Bytes Transmitted    |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Total Bytes Received   |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Total Bytes    |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Total Packets Transmitted  |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Total Packets Received |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Total Rx Errors    |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Total Tx Errors    |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Total Collisions   |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Avg. 磁盘秒数/读取 |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Avg. 磁盘秒数/传输 |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Avg. 磁盘秒数/写入    |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Physical Disk Bytes/sec    |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Pct Privileged Time    |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Pct User Time  |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Used Memory kBytes |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Virtual Shared Memory  |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_% DPC Time |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_% Idle Time    |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_% Interrupt Time   |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_% IO Wait Time |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_% Nice Time    |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_% Privileged Time  |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_% Processor Time   |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_% User Time    |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Free Physical Memory   |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Free Space in Paging Files |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Free Virtual Memory    |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Processes  |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Size Stored In Paging Files    |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Uptime |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    Average_Users  |     是 - Computer, ObjectName, InstanceName, CounterPath & SourceSystem    |   Linux 性能计算器      |
|    检测信号  |     是 - Computer, OSType, Version & SourceComputerId    |   检测信号记录 |
|    更新 |     是 - Computer, Product, Classification, UpdateState, Optional & Approved    |   更新管理 |

> [!NOTE]
> 只有在选定期间内存在其数据时，特定的指标和/或维度才会显示

## <a name="create-a-near-real-time-metric-alert"></a>创建准实时指标警报
目前，只能在 Azure 门户中创建准实时指标警报。 即将推出通过使用 PowerShell、Azure 命令行接口 (Azure CLI) 和 Azure Monitor REST API 配置准实时指标警报的支持功能。

创建准实时指标警报的体验已移动到新的“警报(预览)”页。 即使当前“警报”页显示了“添加准实时指标警报”，也会将你重定向到“警报(预览)”页。

若要了解如何创建准实时指标警报，请参阅[在 Azure 门户中创建警报规则](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal)。

## <a name="manage-near-real-time-metric-alerts"></a>管理准实时指标警报
创建准实时指标警报后，可以按照[在 Azure 门户中管理警报](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal)中所述的步骤管理警报。

## <a name="payload-schema"></a>负载架构

使用适当配置的[操作组](monitoring-action-groups.md)时，POST 操作对于所有准实时指标警报包含以下 JSON 有效负载和架构：

```json
{"schemaId":"AzureMonitorMetricAlert","data":
    {
    "version": "2.0",
    "status": "Activated",
    "context": {
    "timestamp": "2018-02-28T10:44:10.1714014Z",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
    "name": "StorageCheck",
    "description": "",
    "conditionType": "SingleResourceMultipleMetricCriteria",
    "condition": {
      "windowSize": "PT5M",
      "allOf": [
        {
          "metricName": "Transactions",
          "dimensions": [
            {
              "name": "AccountResourceId",
              "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
            },
            {
              "name": "GeoType",
              "value": "Primary"
            }
          ],
          "operator": "GreaterThan",
          "threshold": "0",
          "timeAggregation": "PT5M",
          "metricValue": 1.0
        },
      ]
    },
    "subscriptionId": "00000000-0000-0000-0000-000000000000",
    "resourceGroupName": "Contoso",
    "resourceName": "diag500",
    "resourceType": "Microsoft.Storage/storageAccounts",
    "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
    "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
  },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
    }
}
```

## <a name="next-steps"></a>后续步骤

* 了解有关新的[“警报(预览)”体验](monitoring-overview-unified-alerts.md)的详细信息。
* 了解 [Azure 警报（预览）中的日志警报](monitor-alerts-unified-log.md)。
* 了解 [Azure 中的警报](monitoring-overview-alerts.md)。
