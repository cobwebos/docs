---
title: 诊断日志记录
titleSuffix: Azure Cognitive Services
description: 本指南逐步说明如何为 Azure 认知服务启用诊断日志记录。 这些日志提供频繁生成的有关资源操作的丰富数据用于识别问题和调试。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: cd380b4e2a7c05f0beedc2ab102b268aa4068f66
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516373"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>为 Azure 认知服务启用诊断日志记录

本指南逐步说明如何为 Azure 认知服务启用诊断日志记录。 这些日志提供频繁生成的有关资源操作的丰富数据用于识别问题和调试。 继续之前, 必须有一个 Azure 帐户, 其中至少有一个认知服务 (如[必应 Web 搜索](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/overview)、[语音服务](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview)或[LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis)) 的订阅。

## <a name="prerequisites"></a>先决条件

若要启用诊断日志记录，需要指定某个位置用于存储日志数据。 本教程使用 Azure 存储和 Log Analytics。

* [Azure 存储](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) - 保留策略审核、静态分析或备份的诊断日志。 只要配置设置的用户同时拥有两个订阅的相应 RBAC 访问权限，存储帐户就不必位于发出日志的资源所在的订阅中。
* [Log Analytics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics) -一种灵活的日志搜索和分析工具, 可用于分析由 Azure 资源生成的原始日志。

> [!NOTE]
> 还有其他配置选项可供使用。 有关详细信息，请参阅[从 Azure 资源收集和使用日志数据](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)。

## <a name="enable-diagnostic-log-collection"></a>启用诊断日志收集  

首先，让我们使用 Azure 门户启用诊断日志记录。

> [!NOTE]
> 若要使用 PowerShell 或 Azure CLI 启用此功能，请参考[从 Azure 资源收集和使用日志数据](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview#diagnostic-settings)中提供的说明。

1. 导航到 Azure 门户。 然后找到并选择某个认知服务资源。 例如, 你要必应 Web 搜索的订阅。   
2. 接下来，在左侧导航菜单中找到“监视”，然后选择“诊断设置”。 此屏幕包含以前为此资源创建的所有诊断设置。
3. 如果你想要使用以前创建的某个资源，现在可以选择它。 否则，请选择“+ 添加诊断设置”。
4. 输入设置名称。 依次选择“存档到存储帐户”、“发送到 Log Analytics”。
5. 出现配置提示时，请选择你要用来存储诊断日志的存储帐户和 OMS 工作区。 **注意**：如果你没有存储帐户或 OMS 工作区，请按提示创建一个。
6. 选择 "**审核**"、" **RequestResponse**" 和 " **AllMetrics**"。 然后设置诊断日志数据的保留期。 如果将保留策略设置为零，则会无限期存储该日志类别的事件。
7. 单击“保存” 。

最长可能需要在两个小时之后，日志数据才可供查询和分析。 因此，如果当前未显示任何内容，请不要担心。

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>从 Azure 存储查看和导出诊断数据

Azure 存储是一个可靠的对象存储解决方案，它已经过优化，适合存储大量的非结构化数据。 本部分介绍如何在存储帐户中查询 30 天时间范围内的事务总数，并将数据导出到 Excel。

1. 在 Azure 门户中，找到在上一部分创建的 Azure 存储资源。
2. 在左侧导航菜单中找到“监视”，然后选择“指标”。
3. 使用提供的下拉列表配置查询。 对于本示例，我们将时间范围设置为“过去 30 天”，并将指标设置为“事务”。
4. 查询完成后，你将看到过去 30 天的事务的可视化效果。 若要导出此数据，请使用页面顶部的“导出到 Excel”按钮。

详细了解可对 [Azure 存储](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)中的诊断数据执行哪些操作。

## <a name="view-logs-in-log-analytics"></a>在 Log Analytics 中查看日志

请遵照以下说明浏览资源的 Log Analytics 数据。

1. 在 Azure 门户上的左侧导航菜单中，找到并选择“Log Analytics”。
2. 找到并选择启用诊断时创建的资源。
3. 在“常规”下，找到并选择“日志”。 在此页中，可以针对日志运行查询。

### <a name="sample-queries"></a>示例查询

下面是可用于浏览日志数据的几个基本 Kusto 查询。

针对指定时间段内来自 Azure 认知服务的所有诊断日志运行此查询：

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

运行此查询可查看最近的 10 条日志：

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

运行此查询可按**资源**将操作分组：

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
运行此查询可以确定执行某个操作平均花费的时间：

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

运行此查询可以查看不同时间按 OperationName 划分的操作量，以及每隔 10 秒装箱的操作计数。

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>后续步骤

* 若要了解如何启用日志记录, 以及各种 Azure 服务支持的指标和日志类别, 请参阅 Azure 诊断日志 Microsoft Azure 的[指标概述](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)和[azure 诊断日志文章概述](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)。
* 阅读以下文章，了解事件中心：
  * [什么是 Azure 事件中心？](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)
  * [事件中心入门](https://docs.microsoft.com/azure/event-hubs/event-hubs-csharp-ephcs-getstarted)
* 参阅[从 Azure 存储下载指标和诊断日志](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#download-blobs)。
* 阅读[了解 Azure Monitor 日志中的日志搜索](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-new)。
