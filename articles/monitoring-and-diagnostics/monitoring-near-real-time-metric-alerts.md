---
title: Azure Monitor 支持的资源中的新型指标警报 | Microsoft Docs
description: 新型 Azure 准实时指标警报的支持指标和日志参考。
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
ms.date: 04/27/2018
ms.author: snmuvva, vinagara
ms.custom: ''
ms.openlocfilehash: c4a4a82eedc41b7690af005faecc1505257183ab
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33778107"
---
# <a name="newer-metric-alerts-for-azure-services-in-the-azure-portal"></a>Azure 门户中 Azure 服务的新型指标警报
Azure Monitor 现在支持一种新型指标警报类型。 新型警报与[经典指标警报](insights-alerts-portal.md)在以下方面有所不同：

- **延迟降低**：新型指标警报的运行频率可达每分钟一次。 旧式指标警报每 5 分钟方可运行 1 次。 由于引入日志需要时间，日志警报的延迟仍然超过 1 分钟。 
- **支持多维指标**：支持对维度指标发出警报，从而可仅监视关注的指标段。 
- **更好地控制指标条件**：可以定义更丰富的警报规则。 新型警报支持监视指标的最大值、最小值、平均值和总值。 
- **综合监视多个指标**：可以使用单个规则监视多个指标（目前最多为两个指标）。 如果两个指标在指定时间段内违反其各自的阈值，则会触发警报。 
- **更好的通知系统**：所有新型警报均使用[操作组](monitoring-action-groups.md)，这些组是命名的通知和操作组，可以在多个警报中重复使用。 经典指标警报和旧版 Log Analytics 警报不使用操作组。 
- **日志中的指标**（受限公共预览版）：进入 Log Analytics 的日志数据现在可以提取并转换为 Azure Monitor 指标，然后就像其他指标一样，基于其发出警报。 

若要了解如何在 Azure 门户中创建新型指标警报，请参阅[在 Azure 门户中创建警报规则](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal)。 创建后，可以按照[在 Azure 门户中管理警报](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal)中所述的步骤管理警报。


## <a name="portal-powershell-cli-rest-support"></a>门户、PowerShell、CLI、REST 支持
目前，仅可在 Azure 门户、[REST API](https://docs.microsoft.com/en-us/rest/api/monitor/metricalerts/createorupdate) 或[资源管理器模板](monitoring-create-metric-alerts-with-templates.md)中创建新型指标警报。 即将推出使用 PowerShell 和 Azure 命令行接口 (Azure CLI 2.0) 配置新型警报的支持功能。

## <a name="metrics-and-dimensions-supported"></a>指标和维度支持
新型指标警报支持针对使用维度的指标发出警报。 可以使用维度将指标筛选到适当级别。 所有受支持的指标以及适用的维度都可以从 [Azure Monitor - 指标资源管理器（预览）](monitoring-metric-charts.md)中进行浏览和可视化。

下面是新型警报支持的 Azure Monitor 指标源的完整列表：

|资源类型  |支持的维度  | 可用指标|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | 是        | [API 管理](monitoring-supported-metrics.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     是   | [自动化帐户](monitoring-supported-metrics.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | 不适用| [Batch 帐户](monitoring-supported-metrics.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    不适用     |[Redis 缓存](monitoring-supported-metrics.md#microsoftcacheredis)|
|Microsoft.Compute/virtualMachines     |    不适用     | [虚拟机](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   不适用      |[虚拟机规模集](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | 是| [容器组](monitoring-supported-metrics.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.DataFactory/datafactories| 是| [数据工厂 V1](monitoring-supported-metrics.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories     |   是     |[数据工厂 V2](monitoring-supported-metrics.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   不适用      |[适用于 MySQL 的 DB](monitoring-supported-metrics.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    不适用     | [适用于 PostgreSQL 的 DB](monitoring-supported-metrics.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  是      |[事件中心](monitoring-supported-metrics.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| 否 | [保管库](monitoring-supported-metrics.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows     |     不适用    |[逻辑应用](monitoring-supported-metrics.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    不适用     | [应用程序网关](monitoring-supported-metrics.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/dnsZones | 不适用| [DNS 区域](monitoring-supported-metrics.md#microsoftnetworkdnszones) |
|Microsoft.Network/loadBalancers（仅限标准 SKU）| 是| [负载均衡器](monitoring-supported-metrics.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/publicipaddresses     |  不适用       |[公共 IP 地址](monitoring-supported-metrics.md#microsoftnetworkpublicipaddresses)|
|Microsoft.PowerBIDedicated/capacities | 不适用 | [容量](monitoring-supported-metrics.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices     |   不适用      |[搜索服务](monitoring-supported-metrics.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  是       |[服务总线](monitoring-supported-metrics.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    是     | [存储帐户](monitoring-supported-metrics.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     是    | [Blob 服务](monitoring-supported-metrics.md#microsoftstoragestorageaccountsblobservices)、[文件服务](monitoring-supported-metrics.md#microsoftstoragestorageaccountsfileservices)、[队列服务](monitoring-supported-metrics.md#microsoftstoragestorageaccountsqueueservices)和[表服务](monitoring-supported-metrics.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  不适用       | [流分析](monitoring-supported-metrics.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft.CognitiveServices/accounts     |    不适用     | [认知服务](monitoring-supported-metrics.md#microsoftcognitiveservicesaccounts)|
|Microsoft.OperationalInsights/workspaces（预览版） | 是|[Log Analytics 工作区](#log-analytics-logs-as-metrics-for-alerting)|


## <a name="log-analytics-logs-as-metrics-for-alerting"></a>Log Analytics 日志作为警报的指标 

还可在常用 Log Analytics 日志中使用新型指标警报，Log Analytics 日志提取为指标，是日志预览中指标的一部分。  
- 适用于 Windows 和 Linux 计算机的[性能计数器](../log-analytics/log-analytics-data-sources-performance-counters.md)
- [代理运行状况的检测信号记录](../operations-management-suite/oms-solution-agenthealth.md)
- [更新管理](../operations-management-suite/oms-solution-update-management.md)记录
 
> [!NOTE]
> 只有在选定期间内存在其数据时，特定的指标和/或维度才会显示。 在美国东部、美国中西部和西欧具有工作区且已选择加入预览版的客户可使用这些指标。 如果希望参与此预览版，请前往[调查](https://aka.ms/MetricLogPreview)页面进行注册。

支持下面列出的基于 Log Analytics 日志的指标源：

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



## <a name="payload-schema"></a>负载架构

使用适当配置的[操作组](monitoring-action-groups.md)时，POST 操作对于所有准新型指标警报包含以下 JSON 有效负载和架构：

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

* 详细了解新式[警报体验](monitoring-overview-unified-alerts.md)。
* 了解 [Azure 中的日志警报](monitor-alerts-unified-log.md)。
* 了解 [Azure 中的警报](monitoring-overview-alerts.md)。
