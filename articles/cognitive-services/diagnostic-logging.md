---
title: 诊断日志记录
titleSuffix: Azure Cognitive Services
description: 本指南提供分步说明启用 Azure 认知服务的诊断日志记录。 这些日志提供丰富和频繁操作相关的数据的资源使用问题识别和调试。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: e1a6a44d7ff9d5786388fc47245ef5c79cb9be82
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155727"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>启用 Azure 认知服务的诊断日志记录

本指南提供分步说明启用 Azure 认知服务的诊断日志记录。 这些日志提供丰富和频繁操作相关的数据的资源使用问题识别和调试。 在继续之前，您必须具有如具有至少一个认知服务的订阅的 Azure 帐户[必应 Web 搜索](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/overview)，[语音服务](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview)，或[LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis)。

## <a name="prerequisites"></a>必备组件

若要启用诊断日志记录，将需要某个位置来存储日志数据。 本教程使用 Azure 存储和 Log Analytics。

* [Azure 存储](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs)的保留策略审核、 静态分析或备份的诊断日志。 只要配置设置的用户同时拥有两个订阅的相应 RBAC 访问权限，存储帐户就不必位于发出日志的资源所在的订阅中。
* [Log Analytics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics) -可以分析的生成的一种 Azure 资源的原始日志数据的灵活的日志搜索和分析工具。

> [!NOTE]
> 提供了其他配置选项。 若要了解详细信息，请参阅[收集和使用日志数据从 Azure 资源](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)。

## <a name="enable-diagnostic-log-collection"></a>启用诊断日志收集  

让我们先启用诊断日志记录使用 Azure 门户。

> [!NOTE]
> 若要启用此功能使用 PowerShell 或 Azure CLI，请使用中提供的说明[收集和使用日志数据从 Azure 资源](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs)。

1. 导航到 Azure 门户。 然后找到并选择认知服务资源。 例如，你订阅了必应 Web 搜索。   
2. 接下来，从左侧导航菜单中，找到**监视**，然后选择**诊断设置**。 该屏幕包含此资源的所有以前创建的诊断设置。
3. 如果你想要使用以前创建的资源，您可以立即选择它。 否则，请选择 **+ 添加诊断设置**。
4. 输入设置的名称。 然后选择**存档到存储帐户**并**发送 log Analytics**。
5. 当提示你配置，选择存储帐户和 OMS 工作区，你想要用于存储诊断日志。 **注意**：如果没有存储帐户或 OMS 工作区中，按照提示创建一个。
6. 选择**审核**， **RequestResponse**，并**AllMetrics**。 然后设置诊断日志数据的保留期。 如果保留期策略设置为零，将无限期存储日志类别的事件。
7. 单击“ **保存**”。

它可以需要长达两个小时，可用于查询和分析日志记录数据之前。 因此不必担心，如果未立即看到任何内容。

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>查看和导出从 Azure 存储诊断数据

Azure 存储是一种功能强大的对象存储解决方案，适用于存储大量非结构化数据。 在本部分中，将了解对 30 天时间范围内进行查询你的存储帐户的总事务和导出到 excel 的数据。

1. 从 Azure 门户中，找到在上一部分中创建的 Azure 存储资源。
2. 从左侧导航菜单中，找到**监视**，然后选择**指标**。
3. 使用可用的下拉列表来配置你的查询。 对于此示例中，让我们来设置时间范围为**过去 30 天**和到指标**事务**。
4. 查询完成后，您将看到过去 30 天内的事务的可视化效果。 若要导出此数据，请使用**导出到 Excel**按钮位于页的顶部。

了解有关如何使用中的诊断数据的详细信息[Azure 存储](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)。

## <a name="view-logs-in-log-analytics"></a>在 Log Analytics 中查看日志

按照这些说明来浏览 log analytics 数据资源。

1. 从 Azure 门户中，找到并选择**Log Analytics**在左侧导航菜单。
2. 找到并选择创建时启用诊断的资源。
3. 下**常规**，找到并选择**日志**。 在此页，可以针对日志运行查询。

### <a name="sample-queries"></a>示例查询

以下是几个基本 Kusto 查询可用于浏览日志数据。

指定的时间段内的 Azure 认知服务从运行此查询的所有诊断日志：

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

运行此查询以查看最近的 10 个日志：

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

运行此查询将按操作分组**资源**:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
运行此查询以查找的平均时间所需执行的操作：

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

运行此查询来查看一段时间 OperationName 按计数装箱为每隔 10 秒拆分操作的卷。

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>后续步骤

* 若要了解如何启用日志记录，以及各种 Azure 服务支持的指标和日志类别，请阅读文章[的指标概述](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)Microsoft Azure 中和[Azure 诊断日志概述](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)文章。
* 阅读以下文章，了解事件中心：
  * [什么是 Azure 事件中心？](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)
  * [事件中心入门](https://docs.microsoft.com/azure/event-hubs/event-hubs-csharp-ephcs-getstarted)
* 参阅[从 Azure 存储下载指标和诊断日志](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#download-blobs)。
* 读取[了解 Azure Monitor 日志中的日志搜索](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-new)。
