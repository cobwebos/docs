---
title: 收集 & 分析资源日志
description: 记录和分析 Azure 容器注册表的资源日志事件，例如身份验证、图像推送和图像拉取。
ms.topic: article
ms.date: 01/03/2020
ms.openlocfilehash: 72d03149cd24636ba2086dfaaff0dbba16d30f1e
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748005"
---
# <a name="azure-container-registry-logs-for-diagnostic-evaluation-and-auditing"></a>用于诊断评估和审核的 Azure 容器注册表日志

本文介绍如何使用[Azure Monitor](../azure-monitor/overview.md)的功能收集 Azure 容器注册表的日志数据。 Azure Monitor 为注册表中的用户驱动的事件收集[资源日志](../azure-monitor/platform/platform-logs-overview.md)（以前称为*诊断日志*）。 收集并使用此数据以满足以下需求：

* 审核注册表身份验证事件以确保安全性和合规性 

* 为注册表项目提供完整的活动跟踪，如请求和拉取事件，以便可以使用注册表诊断操作问题 

使用 Azure Monitor 收集资源日志数据可能会产生额外成本。 请参阅[Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)。 


> [!IMPORTANT]
> 此功能目前处于预览阶段，但有一些[限制](#preview-limitations)。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

## <a name="preview-limitations"></a>预览版限制

当前记录了图像和其他项目的以下存储库级别事件：

* **推送事件**
* **请求事件**
* **取消标记事件**
* **删除事件**（包括存储库删除事件）

当前未记录的存储库级事件：清除事件。

## <a name="registry-resource-logs"></a>注册表资源日志

资源日志包含 Azure 资源发出的信息，这些资源描述了其内部操作。 对于 Azure 容器注册表，日志包含下表中存储的身份验证和存储库级事件。 

* **ContainerRegistryLoginEvents** -注册表身份验证事件和状态，包括传入的标识和 IP 地址
* **ContainerRegistryRepositoryEvents** -在注册表存储库中的映像和其他项目的推送和拉取操作
* **AzureMetrics** - [容器注册表指标](../azure-monitor/platform/metrics-supported.md#microsoftcontainerregistryregistries)，例如聚合推送和拉取计数。

对于操作，日志数据包括：
  * 成功或失败状态
  * 开始和结束时间戳

除资源日志以外，Azure 还提供[活动日志](../azure-monitor/platform/platform-logs-overview.md)（azure 管理事件的一个订阅级别记录，例如创建或删除容器注册表）。

## <a name="enable-collection-of-resource-logs"></a>启用资源日志收集

默认情况下，不启用容器注册表的资源日志收集。 为要监视的每个注册表显式启用诊断设置。 有关启用诊断设置的选项，请参阅[创建诊断设置以在 Azure 中收集平台日志和指标](../azure-monitor/platform/diagnostic-settings.md)。

例如，若要在 Azure Monitor 中以近乎实时的速度查看容器注册表的日志和指标，请在 Log Analytics 工作区中收集资源日志。 使用 Azure 门户启用此诊断设置：

1. 如果还没有工作区，请使用[Azure 门户](../azure-monitor/learn/quick-create-workspace.md)创建工作区。 若要最大程度地减少数据收集延迟，请确保工作区与容器注册表位于**同一区域**。
1. 在门户中，选择注册表，然后选择 "**监视 > 诊断设置" > 添加诊断设置**"。
1. 输入设置的名称，并选择 "**发送到 Log Analytics**"。
1. 选择注册表诊断日志的工作区。
1. 选择要收集的日志数据，并单击 "**保存**"。

下图显示了如何使用门户创建注册表的诊断设置。

![启用诊断设置](media/container-registry-diagnostics-audit-logs/diagnostic-settings.png)

> [!TIP]
> 只收集所需的数据，平衡成本和监视需求。 例如，如果只需审核身份验证事件，请仅选择 " **ContainerRegistryLoginEvents** " 日志。 

## <a name="view-data-in-azure-monitor"></a>查看 Azure Monitor 中的数据

在 Log Analytics 中启用诊断日志收集后，数据可能需要几分钟时间才会出现在 Azure Monitor 中。 若要在门户中查看数据，请选择注册表，然后选择 "**监视 > 日志**"。 选择包含注册表数据的一个表。 

运行查询以查看数据。 提供了几个示例查询，或运行自己的查询。 例如，以下查询从**ContainerRegistryRepositoryEvents**表中检索最近24小时的数据：

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

下图显示了示例输出：

![查询日志数据](media/container-registry-diagnostics-audit-logs/azure-monitor-query.png)

有关在 Azure 门户中使用 Log Analytics 的教程，请参阅[Azure Monitor Log Analytics 入门](../azure-monitor/log-query/get-started-portal.md)，或者试用 Log Analytics[演示环境](https://portal.loganalytics.io/demo)。 

有关日志查询的详细信息，请参阅[Azure Monitor 中的日志查询概述](../azure-monitor/log-query/log-query-overview.md)。

### <a name="additional-query-examples"></a>其他查询示例

#### <a name="100-most-recent-registry-events"></a>100最近的注册表事件

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer , OperationName , Identity , Repository , DurationMs , Region , ResultType
```

## <a name="additional-log-destinations"></a>其他日志目标

除了将日志发送到 Log Analytics，或者作为替代方法，一种常见的情况是选择 Azure 存储帐户作为日志目标。 若要将日志存档在 Azure 存储中，请先创建一个存储帐户，然后通过诊断设置启用存档。

还可以将诊断日志事件流式传输到[Azure 事件中心](../event-hubs/event-hubs-what-is-event-hubs.md)。 数据中心每秒可以接受数百万事件，然后可以使用任何实时分析提供程序转换并存储这些事件。 

## <a name="next-steps"></a>后续步骤

* 详细了解如何使用[Log Analytics](../azure-monitor/log-query/get-started-portal.md)和创建[日志查询](../azure-monitor/log-query/get-started-queries.md)。
* 请参阅[azure 平台日志概述](../azure-monitor/platform/platform-logs-overview.md)，了解 azure 不同层提供的平台日志。

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
