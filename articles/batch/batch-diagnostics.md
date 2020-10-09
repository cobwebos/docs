---
title: 指标、警报和诊断日志
description: 记录并分析 Azure Batch 帐户资源（诸如池和任务）的诊断日志事件。
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: seodec18
ms.openlocfilehash: 265149e8d3cd775974ec690ebffbce92a1b82b2e
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2020
ms.locfileid: "91848681"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>用于诊断评估和监视的 Batch 指标、警报和日志

本文介绍如何使用 [Azure Monitor](../azure-monitor/overview.md) 的功能监视 Batch 帐户。 Azure Monitor 收集 Batch 帐户中资源的[指标](../azure-monitor/platform/data-platform-metrics.md)和[诊断日志](../azure-monitor/platform/platform-logs-overview.md)。 以各种方法收集和使用此数据可以监视 Batch 帐户及诊断问题。 还可以配置[指标警报](../azure-monitor/platform/alerts-overview.md)，以便在某项指标达到指定值时收到通知。

## <a name="batch-metrics"></a>Batch 指标

指标是 Azure 资源发出的并由 Azure Monitor 服务使用的 Azure 遥测数据（也称为性能计数器）。 Batch 帐户中的指标示例包括“池创建事件”、“低优先级节点计数”和“任务完成事件”。

请参阅[支持的 Batch 指标列表](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)。

指标：

- 无需经过额外的配置，便已在每个 Batch 帐户中默认启用
- 每分钟生成一次
- 不会自动保留，但有 30 天的历史记录滚动更新周期。 可将活动指标保留为诊断日志记录的一部分。

## <a name="view-batch-metrics"></a>查看 Batch 指标

在 Azure 门户中，帐户的“概览”页会默认显示关键的节点、核心和任务指标。

若要在 Azure 门户中查看所有 Batch 帐户指标，请执行以下操作：

1. 在 Azure 门户中，选择“所有服务” > “Batch 帐户”，然后选择你的 Batch 帐户的名称。
2. 在“监视”下，选择“指标”。 
3. 选择“添加指标”，然后从下拉列表中选择一个指标。
4. 为指标选择“聚合”选项。 对于基于计数的指标（如“专用核心计数”或“低优先级节点计数”），请使用“平均”聚合。 对于基于事件的指标（如“池重设大小完成事件数”），请使用“计数”聚合。

   > [!WARNING]
   > 请勿使用“求和”聚合，该聚合会将图表生存期间接收到的所有数据点的值相加。

5. 若要添加其他指标，请重复步骤 3 和 4。

还可以使用 Azure Monitor API 以编程方式检索指标。 有关示例，请参阅[使用 .NET 检索 Azure Monitor 指标](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/)。

### <a name="batch-metric-reliability"></a>Batch 指标可靠性

指标可帮助识别趋势，并可用于数据分析。 请务必注意，指标不保证送达，并且可能会出现乱序送达、数据丢失和/或数据重复的情况。 因此，建议不要使用单一事件来发出警报或触发函数。 若要更详细地了解如何为警报设置阈值，请参阅下一部分。

过去 3 分钟内发出的指标可能仍然正在聚合，因此在此时间范围内，指标值可能会因记录不全而少报。

## <a name="batch-metric-alerts"></a>Batch 指标警报

你可以配置准实时指标警报。当指定指标的值超过分配的阈值时，会触发这些警报。 当警报“激活”（当阈值越过并满足警报条件时）以及“已解决”（当阈值再次超过并且不再满足条件）时，警报将生成通知。

建议不要使用基于单一数据点触发的警报，因为指标可能会出现乱序送达、数据丢失和/或数据重复的情况。 创建警报时，可以使用阈值来应对这些不一致。

例如，你可能想要配置一个当低优先级核心计数降到特定级别时触发的指标警报，以便能够调整池的组成部分。 为获得最佳结果，请设置一个 10 分钟或 10 分钟以上的周期，如果平均低优先级核心计数在整个周期内都低于阈值，则触发警报。 这样就可以有更多的时间来聚合指标，以便获得更准确的结果。

若要在 Azure 门户中配置指标警报，请执行以下操作：

1. 选择“所有服务” > “Batch 帐户”，然后选择 Batch 帐户的名称。
2. 在“监视”下，选择“警报”，然后选择“新建警报规则”。  
3. 单击“选择条件”，然后选择一个指标。 确认“图表期间”、“阈值类型”、“运算符”和“聚合类型”的值，然后输入一个**阈值**。 然后选择“完成”。
4. 通过选择现有操作组或创建新的操作组，将一个操作组添加到警报中。
5. 在“警报规则详细信息”部分中，输入**警报规则名称**和**说明**并选择**严重性**
6. 选择“创建警报规则”。

有关创建指标警报的详细信息，请参阅[了解指标警报在 Azure Monitor 中的工作方式](../azure-monitor/platform/alerts-metric-overview.md)和[使用 Azure Monitor 创建、查看和管理指标警报](../azure-monitor/platform/alerts-metric.md)。

还可以使用 Azure Monitor [REST API](/rest/api/monitor/) 配置准实时警报。 有关详细信息，请参阅 [Microsoft Azure 中的警报概述](../azure-monitor/platform/alerts-overview.md)。 若要在警报中包含特定于作业、任务或池的信息，请参阅[借助 Azure Monitor 警报对事件做出响应](../azure-monitor/learn/tutorial-response.md)中有关搜索查询的信息。

## <a name="batch-diagnostics"></a>Batch 诊断

诊断日志包含 Azure 资源发出的、描述每个资源的操作的信息。 对于 Batch，可以收集以下日志：

- Azure Batch 服务在单个 Batch 资源（例如池或任务）的生存期内发出的**服务日志**事件。
- 帐户级别的**指标**日志。

用于启用诊断日志收集的设置默认未启用。 请针对想要监视的每个 Batch 帐户显式启用诊断日志。

### <a name="log-destinations"></a>日志目标

一种常见场景是选择 Azure 存储帐户作为日志目标。 若要在 Azure 存储中存储日志，请在启用日志收集之前创建帐户。 如果已将某个存储帐户关联到了 Batch 帐户，可以选择该帐户作为日志目标。

此外，还可以：

- 将 Batch 诊断日志事件流式传输到 [Azure 事件中心](../event-hubs/event-hubs-about.md)。 数据中心每秒可以接受数百万事件，用户可以使用任何实时分析提供程序转换并存储这些事件。
- 将诊断日志发送到 [Azure Monitor 日志](../azure-monitor/log-query/log-query-overview.md)（可用于分析这些日志），或者导出诊断日志以在 Power BI 或 Excel 中进行分析。

> [!NOTE]
> 使用 Azure 服务存储或处理诊断日志数据可能会产生额外的费用。

### <a name="enable-collection-of-batch-diagnostic-logs"></a>启用 Batch 诊断日志的收集

若要在 Azure 门户中创建新的诊断设置，请执行以下步骤。

1. 在 Azure 门户中，选择“所有服务” > “Batch 帐户”，然后选择你的 Batch 帐户的名称。
2. 在“监视”下，选择“诊断设置” 。
3. 在“诊断设置”中，选择“添加诊断设置” 。
4. 输入设置名称。
5. 选择目标：“发送到 Log Analytics”、“存档到存储帐户”或“流式传输到事件中心”。 如果选择了存储帐户，则还可设置保留策略。 如果未指定保留天数，则数据在存储帐户的生存期内会一直保留。
6. 选择“ServiceLog”和/或“AllMetrics”。
7. 选择“保存”以创建诊断设置。

还可以使用[资源管理器模板](../azure-monitor/platform/diagnostic-settings-template.md)、Azure PowerShell 或 Azure CLI [在 Azure 门户中通过 Azure Monitor 来启用收集功能](../azure-monitor/platform/diagnostic-settings.md)，以便配置诊断设置。 有关详细信息，请参阅 [Azure 平台日志概述](../azure-monitor/platform/platform-logs-overview.md)。

### <a name="access-diagnostics-logs-in-storage"></a>访问存储中的诊断日志

如果在存储帐户中存档 Batch 诊断日志，则在发生相关的事件后，会立即在存储帐户中创建一个存储容器。 根据以下命名模式创建 Blob：

```json
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{Batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

例如：

```json
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```

每个 `PT1H.json` Blob 文件包含 JSON 格式的事件，这些事件是在 Blob URL 中指定的小时（例如 `h=12`）内发生的。 在当前的小时内发生的事件将追加​​到 `PT1H.json` 文件。 分钟值 (`m=00`) 始终为 `00`，因为诊断日志事件按小时细分成单个 blob。 （所有时间均是 UTC 时间。）

以下是 `PT1H.json` 日志文件中 `PoolResizeCompleteEvent` 条目的示例。 它包括有关专用和低优先级节点的当前和目标数量以及操作的开始和结束时间的信息：

```json
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

若要详细了解存储帐户中诊断日志的架构，请参阅[将 Azure 资源日志存档到存储帐户](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)。 若要以编程方式访问存储帐户中的日志，请使用存储 API。

### <a name="service-log-events"></a>服务日志事件

Azure Batch 服务日志（如果已收集）包含 Azure Batch 服务在单个 Batch 资源（例如池或任务）的生存期内发出的事件。 Batch 发出的每个事件以 JSON 格式记录。 例如，下面是一个**池创建事件**样本的正文：

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "5",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "taskSlotsPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Batch 服务发出的服务日志事件包括以下各项：

- [池创建](batch-pool-create-event.md)
- [池删除启动](batch-pool-delete-start-event.md)
- [池删除完成](batch-pool-delete-complete-event.md)
- [池调整大小启动](batch-pool-resize-start-event.md)
- [池调整大小完成](batch-pool-resize-complete-event.md)
- [池自动缩放](batch-pool-autoscale-event.md)
- [任务启动](batch-task-start-event.md)
- [任务完成](batch-task-complete-event.md)
- [任务失败](batch-task-fail-event.md)
- [任务计划失败](batch-task-schedule-fail-event.md)

## <a name="next-steps"></a>后续步骤

- 了解适用于生成批处理解决方案的[批处理 API 和工具](batch-apis-tools.md)。
- 详细了解如何[监视 Batch 解决方案](monitoring-overview.md)。
