---
title: 收集和分析资源日志
description: 记录和分析 Azure 容器注册表的资源日志事件，如身份验证、映像推送和图像提取。
ms.topic: article
ms.date: 01/03/2020
ms.openlocfilehash: 00f9468721126bd166051df47cec1596356e9b54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409637"
---
# <a name="azure-container-registry-logs-for-diagnostic-evaluation-and-auditing"></a>用于诊断评估和审核的 Azure 容器注册表日志

本文介绍如何使用[Azure 监视器](../azure-monitor/overview.md)的功能为 Azure 容器注册表收集日志数据。 Azure 监视器收集注册表中用户驱动事件[的资源日志](../azure-monitor/platform/platform-logs-overview.md)（以前称为*诊断日志*）。 收集和使用此数据以满足需求，例如：

* 审核注册表身份验证事件，以确保安全性和合规性 

* 在注册表项目（如拉取和拉取事件）上提供完整的活动跟踪，以便您可以诊断注册表的操作问题 

使用 Azure 监视器收集资源日志数据可能会产生额外费用。 请参阅[Azure 监视器定价](https://azure.microsoft.com/pricing/details/monitor/)。 

## <a name="repository-events"></a>存储库事件

当前记录以下映像和其他工件的存储库级事件：

* **推送事件**
* **拉取事件**
* **取消标记事件**
* **删除事件**（包括存储库删除事件）

当前未记录的存储库级事件：清除事件。

## <a name="registry-resource-logs"></a>注册表资源日志

资源日志包含 Azure 资源发出的描述其内部操作的信息。 对于 Azure 容器注册表，日志包含存储在下表中的身份验证和存储库级事件。 

* **容器注册登录事件**- 注册表身份验证事件和状态，包括传入的标识和 IP 地址
* **容器注册表存储库事件**- 操作，如推送和拉取注册表存储库中的图像和其他工件
* **AzureMetrics** - [容器注册表指标](../azure-monitor/platform/metrics-supported.md#microsoftcontainerregistryregistries)，如聚合推送和拉取计数。

对于操作，日志数据包括：
  * 成功或失败状态
  * 开始和结束时间戳

除了资源日志之外，Azure 还提供[活动日志](../azure-monitor/platform/platform-logs-overview.md)，Azure 管理事件的单个订阅级记录，如创建或删除容器注册表。

## <a name="enable-collection-of-resource-logs"></a>启用资源日志集合

默认情况下，不会启用容器注册表的资源日志集合。 显式启用要监视的每个注册表的诊断设置。 有关启用诊断设置的选项，请参阅[创建诊断设置以在 Azure 中收集平台日志和指标](../azure-monitor/platform/diagnostic-settings.md)。

例如，要在 Azure 监视器中近乎实时地查看容器注册表的日志和指标，请收集日志分析工作区中的资源日志。 要使用 Azure 门户启用此诊断设置，请使用以下因素：

1. 如果还没有工作区，请使用[Azure 门户](../azure-monitor/learn/quick-create-workspace.md)创建工作区。 要最大程度地减少数据收集中的延迟，请确保工作区与容器注册表**位于同一区域**。
1. 在门户中，选择注册表，然后选择 **"监视>诊断设置>添加诊断设置**。
1. 输入设置的名称，然后选择 **"发送到日志分析**"。
1. 选择注册表诊断日志的工作区。
1. 选择要收集的日志数据，然后单击"**保存**"。

下图显示了使用门户为注册表创建的诊断设置。

![启用诊断设置](media/container-registry-diagnostics-audit-logs/diagnostic-settings.png)

> [!TIP]
> 仅收集所需的数据，平衡成本和监控需求。 例如，如果您只需要审核身份验证事件，请仅选择**容器注册登录事件**日志。 

## <a name="view-data-in-azure-monitor"></a>在 Azure 监视器中查看数据

在日志分析中启用诊断日志集合后，数据可能需要几分钟才能显示在 Azure 监视器中。 要查看门户中的数据，请选择注册表，然后选择 **"监视>日志**。 选择包含注册表数据的表之一。 

运行查询以查看数据。 提供了几个示例查询，或运行您自己的查询。 例如，以下查询从**容器注册表存储库事件**表中检索最近 24 小时的数据：

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

下图显示了示例输出：

![查询日志数据](media/container-registry-diagnostics-audit-logs/azure-monitor-query.png)

有关在 Azure 门户中使用日志分析的教程，请参阅[使用 Azure 监视器日志分析入门](../azure-monitor/log-query/get-started-portal.md)，或尝试日志分析[演示环境](https://portal.loganalytics.io/demo)。 

有关日志查询的详细信息，请参阅 Azure[监视器 中的日志查询概述](../azure-monitor/log-query/log-query-overview.md)。

### <a name="additional-query-examples"></a>其他查询示例

#### <a name="100-most-recent-registry-events"></a>100 个最近的注册表事件

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer , OperationName , Identity , Repository , DurationMs , Region , ResultType
```

## <a name="additional-log-destinations"></a>其他日志目标

除了将日志发送到日志分析或作为替代方法之外，常见方案是选择 Azure 存储帐户作为日志目标。 要在 Azure 存储中存档日志，请先创建存储帐户，然后再通过诊断设置启用存档。

还可以将诊断日志事件流式传输到[Azure 事件中心](../event-hubs/event-hubs-what-is-event-hubs.md)。 数据中心每秒可以接受数百万事件，然后可以使用任何实时分析提供程序转换并存储这些事件。 

## <a name="next-steps"></a>后续步骤

* 了解有关使用[日志分析和](../azure-monitor/log-query/get-started-portal.md)创建[日志查询的更多信息](../azure-monitor/log-query/get-started-queries.md)。
* 请参阅[Azure 平台日志概述](../azure-monitor/platform/platform-logs-overview.md)，以了解 Azure 不同层可用的平台日志。

