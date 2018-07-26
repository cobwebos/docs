---
title: 监视 Azure Batch | Microsoft Docs
description: 了解 Azure 监视服务、指标、诊断日志以及 Azure Batch 的其他监视功能。
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: danlep
ms.openlocfilehash: fd4226254e076957ac8651f9172f1f3f82427a60
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001824"
---
# <a name="monitor-batch-solutions"></a>监视 Batch 解决方案

Azure 和 Batch 服务提供了一系列服务、工具和 API 来监视 Batch 解决方案。 本概述文章可帮助你选择适合需求的监视方法。

有关可用来监视 Azure 资源的 Azure 组件和服务的概述，请参阅[监视 Azure 应用程序和资源](../monitoring-and-diagnostics/monitoring-overview.md)。

## <a name="subscription-level-monitoring"></a>订阅级监视

在订阅级别（包括 Batch 帐户），[Azure 活动日志](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)将操作事件数据收集到[几个类别](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md#categories-in-the-activity-log)中。

对于 Batch 帐户，具体而言，活动日志收集与帐户创建和删除以及密钥管理相关的事件。

从活动日志中检索事件的一种方法是使用 Azure 门户。 单击“所有服务” > “活动日志”。 或者，使用 Azure CLI、PowerShell cmdlet 或 Azure Monitor REST API 来查询事件。 还可以导出活动日志，或者配置[活动日志警报](../monitoring-and-diagnostics/monitoring-activity-log-alerts-new-experience.md)。

## <a name="batch-account-level-monitoring"></a>Batch 帐户级监视

使用 [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) 的各项功能监视每个 Batch 帐户。 Azure Monitor 针对 Batch 帐户级别范围内的资源（例如池、作业和任务）收集[指标](../monitoring-and-diagnostics/monitoring-overview-metrics.md)以及可选的[诊断日志](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)。 可以手动或以编程方式收集并使用此数据来监视 Batch 帐户中的活动以及对问题进行诊断。 有关详细信息，请参阅[用于诊断评估和监视的 Batch 指标、警报和日志](batch-diagnostics.md)。
 
> [!NOTE]
> 指标默认情况下在 Batch 帐户中可用，不需要进行额外配置，它们具有为期 30 天的滚动历史记录。 必须为 Batch 帐户启用诊断日志记录，并且，若要存储或处理诊断日志数据，可能会产生其他成本。 

## <a name="batch-resource-monitoring"></a>Batch 资源监视

在 Batch 应用程序中，可以使用 Batch API 来监视或查询资源（包括作业、任务、节点和池）的状态。 例如：

* [按状态对任务和计算节点计数](batch-get-resource-counts.md)
* [创建可高效列出 Batch 资源的查询](batch-efficient-list-queries.md)
* [创建任务依赖项](batch-task-dependencies.md)
* 使用[作业管理器任务](/rest/api/batchservice/job/add#jobmanagertask)
* 监视[任务状态](/rest/api/batchservice/task/list#taskstate)
* 监视[节点状态](/rest/api/batchservice/computenode/list#computenodestate)
* 监视[池状态](/rest/api/batchservice/pool/get#poolstate)
* 监视[帐户中的池使用情况](/rest/api/batchservice/pool/listusagemetrics)
* [按状态对池节点进行计数](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="vm-performance-counters-and-application-monitoring"></a>VM 性能计数器和应用程序监视

* [Application Insights](../application-insights/app-insights-overview.md) 是一项 Azure 服务，可以用来以编程方式监视 Batch 作业和任务的可用性、性能和使用情况。 轻松从计算节点 (VM) 获取性能计数器，并从 VM 获取任务的自定义信息。 

  有关示例，请参阅[使用 Application Insights 监视和调试 Batch .NET 应用程序](monitor-application-insights.md)以及附随的[代码示例](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights)。

  > [!NOTE]
  > 使用 Application Insights 可能会产生额外的成本。 请参阅[定价选项](https://azure.microsoft.com/pricing/details/application-insights/)。 
  >

* [BatchLabs](https://github.com/Azure/BatchLabs) 是一个功能丰富的免费独立客户端工具，可帮助创建、调试和监视 Azure Batch 应用程序。 下载适用于 Mac、Linux 或 Windows 的[安装包](https://azure.github.io/BatchLabs/)。 （可选）对 Batch 解决方案进行配置以[显示 Application Insights 数据](https://github.com/Azure/batch-insights)，例如 BatchLabs 中的 VM 性能计数器。


## <a name="next-steps"></a>后续步骤

* 了解适用于生成批处理解决方案的[批处理 API 和工具](batch-apis-tools.md)。
* 详细了解使用 Batch [实现诊断日志记录](batch-diagnostics.md)。