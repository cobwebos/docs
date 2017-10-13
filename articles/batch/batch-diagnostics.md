---
title: "为 Batch 事件启用诊断日志记录 - Azure | Microsoft Docs"
description: "记录并分析 Azure Batch 帐户资源（诸如池和任务）的诊断日志事件。"
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: e14e611d-12cd-4671-91dc-bc506dc853e5
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b7bc6fd9921ab0f2374ace33ea5c1ab93a78f860
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="log-events-for-diagnostic-evaluation-and-monitoring-of-batch-solutions"></a>记录事件以用来对 Batch 解决方案进行诊断评估和监视

与许多 Azure 服务一样，Batch 服务也会在某些资源的生命周期内针对这些资源生成日志事件。 可以启用 Azure Batch 诊断日志来记录资源（诸如池和任务）的事件，并使用日志进行进行诊断评估和监视。 Batch 诊断日志中包括诸如池创建、池删除、任务启动、任务完成之类的事件和其他事件。

> [!NOTE]
> 本文讨论了 Batch 帐户资源本身的日志记录事件，没有讨论作业和任务输出数据。 有关如何存储作业和任务的输出数据的详细信息，请参阅 [Persist Azure Batch job and task output](batch-task-output.md)（保存 Azure Batch 作业和任务输出）。
> 
> 

## <a name="prerequisites"></a>先决条件
* [Azure Batch 帐户](batch-account-create-portal.md)
* [Azure 存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)
  
  要暂留 Batch 诊断日志，必须创建一个将用来存储日志的 Azure 存储帐户。 可以在为 Batch 帐户[启用诊断日志记录](#enable-diagnostic-logging)时指定此存储帐户。 启用日志收集时指定的存储帐户与[应用程序包](batch-application-packages.md)和[任务输出暂留](batch-task-output.md)文章中所提到的链接存储帐户不是同一个。
  
  > [!WARNING]
  > 对于存储在 Azure 存储帐户中的数据，会向你**收费**。 这包括本文中讨论的诊断日志。 在设计[日志保留策略](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)时请记住这一点。
  > 
  > 

## <a name="enable-diagnostic-logging"></a>启用诊断日志记录
默认情况下没有为 Batch 帐户启用诊断日志记录。 必须显式为要监视的每个 Batch 帐户启用诊断日志：

[如何启用诊断日志集合](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs)

建议阅读整篇 [Azure 诊断日志概述](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)文章以了解如何启用日志记录，以及各种 Azure 服务支持的日志类别。 例如，Azure Batch 当前仅支持一种日志类别：**服务日志**。

## <a name="service-logs"></a>服务日志
Azure Batch 服务日志包含 Azure Batch 服务在 Batch 资源（诸如池或任务）的生命周期内生成的事件。 Batch 生成的每个事件都采用 JSON 格式存储在指定的存储帐户中。 例如，下面是一个**池创建事件**样本的正文：

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "4",
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

每个事件正文都位于指定 Azure 存储帐户中的一个 .json 文件中。 如果希望直接访问日志，可能需要查看[存储帐户中诊断日志的架构](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account)。

## <a name="service-log-events"></a>服务日志事件
Batch 服务当前会生成以下服务日志事件。 此列表可能不完整，因为自本文最后更新以来可能又添加了其他事件。

| **服务日志事件** |
| --- |
| [池创建][pool_create] |
| [池删除启动][pool_delete_start] |
| [池删除完成][pool_delete_complete] |
| [池调整大小启动][pool_resize_start] |
| [池调整大小完成][pool_resize_complete] |
| [任务启动][task_start] |
| [任务完成][task_complete] |
| [任务失败][task_fail] |

## <a name="next-steps"></a>后续步骤
除了将诊断日志事件存储在 Azure 存储帐户中之外，还可以将 Batch 服务日志事件流式传输到 [Azure 事件中心](../event-hubs/event-hubs-what-is-event-hubs.md)，并将它们发送到 [Azure Log Analytics](../log-analytics/log-analytics-overview.md)。

* [将 Azure 诊断日志流式传输到事件中心](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)
  
  将 Batch 诊断事件流式传输到“事件中心”，这是一项可高度缩放的数据入口服务。 数据中心每秒可以接受数百万事件，然后可以使用任何实时分析提供程序转换并存储这些事件。
* [使用 Log Analytics 分析 Azure 诊断日志](../log-analytics/log-analytics-azure-storage.md)
  
  将诊断日志发送到 Log Analytics，可以使用该工具在 Operations Management Suite (OMS) 门户中分析这些日志，或者导出诊断日志以在 Power BI 或 Excel 中进行分析。

[pool_create]: https://msdn.microsoft.com/library/azure/mt743615.aspx
[pool_delete_start]: https://msdn.microsoft.com/library/azure/mt743610.aspx
[pool_delete_complete]: https://msdn.microsoft.com/library/azure/mt743618.aspx
[pool_resize_start]: https://msdn.microsoft.com/library/azure/mt743609.aspx
[pool_resize_complete]: https://msdn.microsoft.com/library/azure/mt743608.aspx
[task_start]: https://msdn.microsoft.com/library/azure/mt743616.aspx
[task_complete]: https://msdn.microsoft.com/library/azure/mt743612.aspx
[task_fail]: https://msdn.microsoft.com/library/azure/mt743607.aspx
