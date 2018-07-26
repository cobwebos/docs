---
title: Azure Batch 的指标、警报和诊断日志 | Microsoft Docs
description: 记录并分析 Azure Batch 帐户资源（诸如池和任务）的诊断日志事件。
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 04/05/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 54034b9a851fc6f06f97be9cfd5f261465bad455
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248256"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>用于诊断评估和监视的 Batch 指标、警报和日志

本文介绍如何使用 [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) 的功能监视 Batch 帐户。 Azure Monitor 收集 Batch 帐户中资源的[指标](../monitoring-and-diagnostics/monitoring-overview-metrics.md)和[诊断日志](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)。 以各种方法收集和使用此数据可以监视 Batch 帐户及诊断问题。 还可以配置[指标警报](../monitoring-and-diagnostics/monitoring-overview-alerts.md#alerts-on-azure-monitor-data)，以便在某项指标达到指定值时收到通知。 

## <a name="batch-metrics"></a>Batch 指标

指标是 Azure Monitor 服务使用的 Azure 资源发出的 Azure 遥测数据（也称为性能计数器）。 Batch 帐户中的指标示例包括：“池创建事件”、“低优先级节点计数”和“任务完成事件”。 

请参阅[支持的 Batch 指标列表](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftbatchbatchaccounts)。

指标：

* 无需经过额外的配置，便已在每个 Batch 帐户中默认启用
* 每分钟生成一次
* 不会自动保留，但有 30 天的历史记录滚动更新周期。 可将活动指标保留为[诊断日志记录](#work-with-diagnostic-logs)的一部分。

### <a name="view-metrics"></a>查看指标

在 Azure 门户中查看 Batch 帐户的指标。 帐户的“概述”页默认显示关键的节点、核心和任务指标。 

查看所有 Batch 帐户指标： 

1. 在门户中，单击“所有服务” > “Batch 帐户”，然后单击 Batch 帐户的名称。
2. 在“监视”下，单击“指标”。
3. 选择一个或多个指标。 如果需要，请使用“订阅”、“资源组”、“资源类型”和“资源”下拉菜单选择其他资源指标。

    ![Batch 指标](media/batch-diagnostics/metrics-portal.png)

若要以编程方式检索指标，请使用 Azure Monitor API。 有关示例，请参阅[使用 .NET 检索 Azure Monitor 指标](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/)。

## <a name="batch-metric-alerts"></a>Batch 指标警报

（可选）配置准实时指标警报。当指定指标的值超过分配的阈值时，会触发这些警报。 当警报状态为“已激活”（超过阈值并满足警报条件）以及“已解决”（再次超过阈值，并且不再满足条件）时，警报将生成所选的[通知](../monitoring-and-diagnostics/insights-alerts-portal.md)。 

例如，你可能想要配置一个当低优先级核心计数降到特定级别时触发的指标警报，以便能够调整池的组成部分。

在门户中配置指标警报：

1. 单击“所有服务” > “Batch 帐户”，然后单击 Batch 帐户的名称。
2. 在“监视”下，单击“警报规则” > “添加指标警报”。
3. 选择一个指标、一个警报条件（例如，在某个时间段内当某个指标超过特定的值时）和一个或多个通知。

还可以使用 [REST API]() 配置准实时警报。 有关详细信息，请参阅[在 Azure 门户中对 Azure 服务使用新型指标警报](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md)
## <a name="batch-diagnostics"></a>Batch 诊断

诊断日志包含 Azure 资源发出的、描述每个资源的操作的信息。 对于 Batch，可以收集以下日志：

* Azure Batch 服务在单个 Batch 资源（例如池或任务）的生存期内发出的**服务日志**事件。 

* 帐户级别的**指标**日志。 

用于启用诊断日志收集的设置默认未启用。 请针对想要监视的每个 Batch 帐户显式启用诊断日志。

### <a name="log-destinations"></a>日志目标

一种常见场景是选择 Azure 存储帐户作为日志目标。 若要在 Azure 存储中存储日志，请在启用日志收集之前创建帐户。 如果已将某个存储帐户关联到了 Batch 帐户，可以选择该帐户作为日志目标。 

诊断日志的其他可选目标：

* 将 Batch 诊断日志事件流式传输到 [Azure 事件中心](../event-hubs/event-hubs-what-is-event-hubs.md)。 数据中心每秒可以接受数百万事件，然后可以使用任何实时分析提供程序转换并存储这些事件。 

* 将诊断日志发送到 [Azure Log Analytics](../log-analytics/log-analytics-overview.md)，然后可以使用 Log Analytics 在 Operations Management Suite (OMS) 门户中分析这些日志，或者导出诊断日志以在 Power BI 或 Excel 中进行分析。

> [!NOTE]
> 使用 Azure 服务存储或处理诊断日志数据可能会产生额外的费用。 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>启用 Batch 诊断日志的收集

1. 在门户中，单击“所有服务” > “Batch 帐户”，然后单击 Batch 帐户的名称。
2. 在“监视”下，单击“诊断日志” > “启用诊断”。
3. 在“诊断设置”中，输入设置的名称，并选择日志目标（现有存储帐户、事件中心或 Log Analytics）。 选择“ServiceLog”和/或“AllMetrics”。

    选择存储帐户时，请选择性地设置保留策略。 如果未指定保留天数，则数据在存储帐户的生存期内会一直保留。

4. 单击“ **保存**”。

    ![Batch 诊断](media/batch-diagnostics/diagnostics-portal.png)

用于启用日志收集的其他选项包括：在门户中使用 Azure Monitor 配置诊断设置、使用[资源管理器模板](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md)，或者使用 Azure PowerShell 或 Azure CLI。 请参阅[从 Azure 资源收集和使用日志数据](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs)。


### <a name="access-diagnostics-logs-in-storage"></a>访问存储中的诊断日志

如果在存储帐户中存档 Batch 诊断日志，则在发生相关的事件后，会立即在存储帐户中创建一个存储容器。 根据以下命名模式创建 Blob：

```
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
示例：

```
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```
每个 PT1H.json Blob 文件包含 JSON 格式的事件，这些事件是在 Blob URL 中指定的小时（例如 h=12）内发生的。 在当前的小时内发生的事件将附加到 PT1H.json 文件。 分钟值始终为 00 (m=00)，因为诊断日志事件按小时细分成单个 blob。 （所有时间均是 UTC 时间。）


有关存储帐户中诊断日志的架构的详细信息，请参阅[存档 Azure 诊断日志](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account)。

若要以编程方式访问存储帐户中的日志，请使用存储 API。 

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
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Batch 服务当前会生成以下服务日志事件。 此列表可能不完整，因为自本文最后更新以来可能又添加了其他事件。

| **服务日志事件** |
| --- |
| [池创建](batch-pool-create-event.md) |
| [池删除启动](batch-pool-delete-start-event.md) |
| [池删除完成](batch-pool-delete-complete-event.md) |
| [池调整大小启动](batch-pool-resize-start-event.md) |
| [池调整大小完成](batch-pool-resize-complete-event.md) |
| [任务启动](batch-task-start-event.md) |
| [任务完成](batch-task-complete-event.md) |
| [任务失败](batch-task-fail-event.md) |



## <a name="next-steps"></a>后续步骤

* 了解适用于生成批处理解决方案的[批处理 API 和工具](batch-apis-tools.md)。
* 详细了解如何[监视 Batch 解决方案](monitoring-overview.md)。
