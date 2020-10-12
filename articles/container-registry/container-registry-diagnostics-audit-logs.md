---
title: 收集和分析资源日志
description: 记录和分析 Azure 容器注册表的资源日志事件，例如身份验证、映像推送和映像拉取。
ms.topic: article
ms.date: 06/01/2020
ms.openlocfilehash: 63ccb944b9c3de9941acf55ca5ea85fda70a7008
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87553370"
---
# <a name="azure-container-registry-logs-for-diagnostic-evaluation-and-auditing"></a>用于诊断评估和审核的 Azure 容器注册表日志

本文介绍如何使用 [Azure Monitor](../azure-monitor/overview.md) 的功能收集 Azure 容器注册表的日志数据。 Azure Monitor 针对注册表中的用户驱动事件收集[资源日志](../azure-monitor/platform/platform-logs-overview.md)（前称为诊断日志）。 收集并使用这些数据可以解决如下所述的需求：

* 审核注册表身份验证事件，以确保安全与合规 

* 提供有关注册表项目的完整活动线索（例如拉取和拉取事件），以便可以诊断注册表的操作问题 

使用 Azure Monitor 收集资源日志数据可能会产生额外的费用。 请参阅 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)。 

## <a name="repository-events"></a>存储库事件

当前会记录映像和其他项目的以下存储库级事件：

* **推送**
* **拉取**
* **取消标记**
* **删除**（包括存储库删除事件）
* **清除标记**和**清除清单**

> [!NOTE]
> 仅当配置了注册表[保留策略](container-registry-retention-policy.md)时，才记录清除事件。

## <a name="registry-resource-logs"></a>注册表资源日志

资源日志包含 Azure 资源发出的描述其内部操作的信息。 对于 Azure 容器注册表，日志包含下表中存储的身份验证和存储库级事件。 

* **ContainerRegistryLoginEvents** - 注册表身份验证事件和状态，包括传入标识和 IP 地址
* **ContainerRegistryRepositoryEvents** - 注册表存储库中的映像及其他项目的推送和拉取等操作
* **AzureMetrics** - 推送和拉取聚合计数等[容器注册表指标](../azure-monitor/platform/metrics-supported.md#microsoftcontainerregistryregistries)。

对于操作，日志数据包括：
  * 成功或失败状态
  * 开始和结束时间戳

除资源日志以外，Azure 还提供[活动日志](../azure-monitor/platform/platform-logs-overview.md)，它是 Azure 管理事件（例如创建或删除容器注册表）的单个订阅级记录。

## <a name="enable-collection-of-resource-logs"></a>启用资源日志的收集

默认未启用容器注册表的资源日志收集。 针对每个需要监视的注册表显式启用诊断设置。 关于那些可启用诊断设置的选项，请参阅[创建诊断设置以收集 Azure 中的平台日志和指标](../azure-monitor/platform/diagnostic-settings.md)。

例如，若要在 Azure Monitor 中近实时查看容器注册表的日志和指标，请在 Log Analytics 工作区中收集资源日志。 若要使用 Azure 门户启用此诊断设置，请执行以下操作：

1. 如果没有工作区，请使用 [Azure 门户](../azure-monitor/learn/quick-create-workspace.md)创建一个工作区。 为了尽量减少数据收集时的延迟，请确保工作区与容器注册表位于**同一区域**。
1. 在门户中选择注册表，然后选择“监视”>“诊断设置”>“添加诊断设置”。
1. 输入设置名称，然后选择“发送到 Log Analytics”。
1. 选择注册表诊断日志对应的工作区。
1. 选择要收集的日志数据，然后单击“保存”。

下图显示了如何使用门户创建注册表的诊断设置。

![启用诊断设置](media/container-registry-diagnostics-audit-logs/diagnostic-settings.png)

> [!TIP]
> 仅收集所需的数据，以便在成本与监视需求之间进行适当的平衡。 例如，如果只需审核身份验证事件，请仅选择“ContainerRegistryLoginEvents”日志。 

## <a name="view-data-in-azure-monitor"></a>在 Azure Monitor 中查看数据

在 Log Analytics 中启用诊断日志收集后，数据可能需要在几分钟时间后才出现在 Azure Monitor 中。 若要在门户中查看数据，请选择注册表，然后选择“监视”>“日志”。 选择一个包含注册表数据的表。 

运行查询以查看数据。 系统中提供了多个示例查询，你也可以运行自己的查询。 例如，以下查询从 **ContainerRegistryRepositoryEvents** 表中检索最近 24 小时的数据：

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

下图显示了示例输出：

![查询日志数据](media/container-registry-diagnostics-audit-logs/azure-monitor-query.png)

有关在 Azure 门户中使用 Log Analytics 的教程，请参阅 [Azure Monitor Log Analytics 入门](../azure-monitor/log-query/get-started-portal.md)，或试用 Log Analytics [演示环境](https://portal.loganalytics.io/demo)。 

有关日志查询的详细信息，请参阅 [Azure Monitor 中的日志查询概述](../azure-monitor/log-query/log-query-overview.md)。

## <a name="query-examples"></a>查询示例

### <a name="error-events-from-the-last-hour"></a>最近一小时内的错误事件

```Kusto
union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
| where TimeGenerated > ago(1h)
| where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
```

### <a name="100-most-recent-registry-events"></a>100 个最近的注册表事件

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer, OperationName, Identity, Repository, DurationMs, Region , ResultType
```

### <a name="identity-of-user-or-object-that-deleted-repository"></a>删除了存储库的用户或对象的标识

```Kusto
ContainerRegistryRepositoryEvents
| where OperationName contains "Delete"
| project LoginServer, OperationName, Repository, Identity, CallerIpAddress
```

### <a name="identity-of-user-or-object-that-deleted-tag"></a>删除了标记的用户或对象的标识

```Kusto
ContainerRegistryRepositoryEvents
| where OperationName contains "Untag"
| project LoginServer, OperationName, Repository, Tag, Identity, CallerIpAddress
```

### <a name="repository-level-operation-failures"></a>存储库级操作失败

```kusto
ContainerRegistryRepositoryEvents 
| where ResultDescription contains "40"
| project TimeGenerated, OperationName, Repository, Tag, ResultDescription
```

### <a name="registry-authentication-failures"></a>注册表身份验证失败

```kusto
ContainerRegistryLoginEvents 
| where ResultDescription != "200"
| project TimeGenerated, Identity, CallerIpAddress, ResultDescription
```


## <a name="additional-log-destinations"></a>其他日志目标

除了将日志发送到 Log Analytics 以外，还有一种常见的替代方案，那就是选择 Azure 存储帐户作为日志目标。 若要将日志存档在 Azure 存储中，请先创建一个存储帐户，然后通过诊断设置启用存档。

还可以将诊断日志事件流式传输到 [Azure 事件中心](../event-hubs/event-hubs-about.md)。 数据中心每秒可以接受数百万事件，用户可以使用任何实时分析提供程序转换并存储这些事件。 

## <a name="next-steps"></a>后续步骤

* 详细了解如何使用 [Log Analytics](../azure-monitor/log-query/get-started-portal.md) 和创建[日志查询](../azure-monitor/log-query/get-started-queries.md)。
* 参阅 [Azure 平台日志概述](../azure-monitor/platform/platform-logs-overview.md)，了解不同的 Azure 层提供的平台日志。
