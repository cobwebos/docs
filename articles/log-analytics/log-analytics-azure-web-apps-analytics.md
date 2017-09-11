---
title: "查看 Azure Web 应用分析数据 | Microsoft Docs"
description: "可以使用 Azure Web 应用分析解决方案来收集所有 Azure Web 应用资源的各种指标，以便深入了解 Azure Web 应用。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 20ff337f-b1a3-4696-9b5a-d39727a94220
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: banders
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 90d0b7e3f49daa33ab8c617d07ba9098cdda751f
ms.contentlocale: zh-cn
ms.lasthandoff: 05/12/2017

---

# <a name="view-analytic-data-for-metrics-across-all-your-azure-web-app-resources"></a>查看所有 Azure Web 应用资源的指标的分析数据

![Web 应用符号](./media/log-analytics-azure-web-apps-analytics/azure-web-apps-analytics-symbol.png)  
Azure Web 应用分析（预览版）解决方案通过收集所有 Azure Web 应用资源的各种指标，深入剖析 [Azure Web 应用](../app-service-web/app-service-web-overview.md)。 借助该解决方案，可以分析和搜索 Web 应用资源指标数据。

使用该解决方案可以查看：

- 响应时间最高的前几个 Web 应用
- 各 Web 应用中的请求数，包括成功和失败的请求
- 传入和传出流量最高的前几个 Web 应用
- CPU 和内存使用率较高的前几个服务计划
- Azure Web 应用活动日志操作

## <a name="connected-sources"></a>连接的源

与大多数其他的 Log Analytics 解决方案不同，代理不会为 Azure Web 应用收集数据。 该解决方案使用的全部数据都直接来自于 Azure。

| 连接的源 | 支持 | 说明 |
| --- | --- | --- |
| [Windows 代理](log-analytics-windows-agents.md) | 否 | 解决方案不会从 Windows 代理收集信息。 |
| [Linux 代理](log-analytics-linux-agents.md) | 否 | 解决方案不会从 Linux 代理收集信息。 |
| [SCOM 管理组](log-analytics-om-agents.md) | 否 | 解决方案不会从连接的 SCOM 管理组中的代理收集信息。 |
| [Azure 存储帐户](log-analytics-azure-storage.md) | 否 | 该解决方案不会从 Azure 存储收集信息。 |

## <a name="prerequisites"></a>先决条件

- 若要访问 Azure Web 应用资源指标信息，必须拥有 Azure 订阅。

## <a name="configuration"></a>配置

执行以下步骤，为工作区配置 Azure Web 应用分析解决方案。

1. 从 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureWebAppsAnalyticsOMS?tab=Overview) 或者使用[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)中所述的过程，启用 Azure Web 应用分析解决方案。
2. [Enable Azure resource metrics logging to OMS using PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell)（使用 PowerShell 启用 OMS 的 Azure 资源指标日志记录）。

Azure Web 应用分析解决方案从 Azure 中收集两组指标：

- Azure Web 应用指标
  - 平均内存工作集
  - 平均响应时间
  - 接收/发送的字节数
  - CPU 时间
  - 请求
  - 内存工作集
  - Httpxxx
- 应用服务计划指标
  - 接收/发送的字节数
  - CPU 百分比
  - 磁盘队列长度
  - Http 队列长度
  - 内存百分比

仅当使用专用服务计划时，才收集应用服务计划指标。 这一点不适用于免费或共享的应用服务计划。

如果使用 OMS 门户添加解决方案，将看到以下磁贴。 需要 [enable Azure resource metrics logging to OMS using PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell)（使用 PowerShell 启用 OMS 的 Azure 资源指标日志记录）。

![执行评估通知](./media/log-analytics-azure-web-apps-analytics/performing-assessment.png)

配置解决方案后，数据应在 15 分钟内开始流向工作区。

## <a name="using-the-solution"></a>使用解决方案

将 Azure Web 应用分析解决方案添加到工作区时，“Azure Web 应用分析”磁贴将添加到“概述”仪表板。 此磁贴显示该解决方案在 Azure 订阅中可访问的 Azure Web 应用计数。

![“Azure Web 应用分析”磁贴](./media/log-analytics-azure-web-apps-analytics/azure-web-apps-analytics-tile.png)

### <a name="view-azure-web-apps-analytics-information"></a>查看 Azure Web 应用分析信息

单击“Azure Web 应用分析”磁贴以打开“Azure Web 应用分析”仪表板。 该仪表板包含下表中的边栏选项卡。 每个边栏选项卡按照指定范围和时间范围列出匹配该边栏选项卡条件的最多 10 个项。 可通过单击边栏选项卡底部的“查看全部”或单击边栏选项卡标题，运行返回所有记录的日志搜索。

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

| 列 | 说明 |
| --- | --- |
| Azure Web 应用 |   |
| Web 应用请求趋势 | 显示所选日期范围内 Web 应用请求趋势的折线图，并列出前 10 个 Web 请求。 单击该折线图，为 <code>Type=AzureMetrics ResourceId=*"/MICROSOFT.WEB/SITES/"* (MetricName=Requests OR MetricName=Http*) &#124; measure avg(Average) by MetricName interval 1HOUR</code> 运行日志搜索 <br>单击某个 Web 请求项，为请求的 Web 请求指标趋势运行日志搜索。 |
| Web 应用响应时间 | 显示所选日期范围内 Web 应用响应时间的折线图。 此外还列出响应时间最快的前 10 个 Web 应用。 单击该图表，为 <code>Type:AzureMetrics ResourceId=*"/MICROSOFT.WEB/SITES/"* MetricName="AverageResponseTime" &#124; measure avg(Average) by Resource interval 1HOUR</code> 运行日志搜索<br> 单击某个 Web 应用，为该 Web 应用运行返回响应时间的日志搜索。 |
| Web 应用流量 | 显示 Web 应用流量的折线图（以 MB 为单位），并列出流量最大的前几个 Web 应用。 单击该图表，为 <code>Type:AzureMetrics ResourceId=*"/MICROSOFT.WEB/SITES/"*  MetricName=BytesSent OR BytesReceived &#124; measure sum(Average) by Resource interval 1HOUR</code> 运行日志搜索<br> 它会显示最近一分钟产生了流量的所有 Web 应用。 单击某个 Web 应用，为该 Web 应用运行显示接收和发送的字节数的日志搜索。 |
| Azure App Service 计划 |   |
| CPU 使用率 &gt; 80% 的应用服务计划 | 显示 CPU 使用率大于 80% 的应用服务计划的总数，并按 CPU 使用率列出前 10 大应用服务计划。 单击全部区域，为 <code>Type=AzureMetrics ResourceId=*"/MICROSOFT.WEB/SERVERFARMS/"* MetricName=CpuPercentage &#124; measure Avg(Average) by Resource</code> 运行日志搜索<br> 它会列出应用服务计划及其平均 CPU 使用率。 单击某个应用服务计划，以运行显示其平均 CPU 使用率的日志搜索。 |
| 内存使用率 &gt; 80% 的应用服务计划 | 显示内存使用率大于 80% 的应用服务计划的总数，并按内存使用率列出前 10 大应用服务计划。 单击全部区域，为 <code>Type=AzureMetrics ResourceId=*"/MICROSOFT.WEB/SERVERFARMS/"* MetricName=MemoryPercentage &#124; measure Avg(Average) by Resource</code> 运行日志搜索<br> 它会列出应用服务计划及其平均内存使用率。 单击某个应用服务计划，以运行显示其平均内存使用率的日志搜索。 |
| Azure Web 应用活动日志 |   |
| Azure Web 应用活动审核 | 显示具有[活动日志](log-analytics-activity.md)的 Web 应用的总数并列出前 10 个活动日志操作。 单击全部区域，为 <code>Type=AzureActivity ResourceProvider= "Azure Web Sites" &#124; measure count() by OperationName</code> 运行日志搜索<br> 它会列出活动日志操作。 单击某个活动日志操作，为该操作运行列出记录的日志搜索。 |



### <a name="azure-web-apps"></a>Azure Web 应用

可以在仪表板中向下钻取，以便更深入地了解 Web 应用指标。 第一组边栏选项卡显示随时间推移的 Web 应用请求趋势、错误（例如 HTTP404）数量、流量和平均响应时间。 它还显示这些指标针对不同 Web 应用的细分。

![Azure Web 应用边栏选项卡](./media/log-analytics-azure-web-apps-analytics/web-apps-dash01.png)

显示该数据主要是为了确定响应时间较高的 Web 应用并查清根本原因。 此外，还可应用阈值限制来帮助用户更轻松地确定有问题的 Web 应用。

- 红色显示的 Web 应用的响应时间高于 1 秒。
- 橙色显示的 Web 应用的响应时间高于 0.7 秒但低于 1 秒。
- 绿色显示的 Web 应用的响应时间低于 0.7 秒。

在以下日志搜索示例图中，可以看到 *anugup3* Web 应用比其他 Web 应用的响应时间高很多。

![日志搜索示例](./media/log-analytics-azure-web-apps-analytics/web-app-search-example.png)

### <a name="app-service-plans"></a>应用服务计划

如果使用专用服务计划，还可以收集应用服务计划指标。 此视图显示 CPU 或内存使用率较高 (&gt; 80%) 的应用服务计划。 它还显示内存或 CPU 使用率较高的前几个应用服务。 同样，可以应用阈值限制来帮助用户更轻松地确定有问题的应用服务。

- 红色显示的应用服务计划的 CPU /内存使用率高于 80%。
- 橙色显示的应用服务计划的 CPU /内存使用率高于 60% 但低于 80%。
- 绿色显示的应用服务计划的 CPU /内存使用率低于 60%。

![Azure App Service 计划边栏选项卡](./media/log-analytics-azure-web-apps-analytics/web-apps-dash02.png)

## <a name="azure-web-apps-log-searches"></a>Azure Web 应用日志搜索

**常见 Azure Web 应用搜索查询列表**显示 Web 应用的所有相关活动日志，用于深入剖析对 Web 应用资源执行的操作。 它还列出所有相关的操作以及它们出现的次数。

通过将任意日志搜索查询作为起始点，可以轻松创建警报。 例如，当某个指标的平均响应时间大于每隔一秒一次时，可能需要创建警报。

## <a name="next-steps"></a>后续步骤

- 为特定指标创建[警报](log-analytics-alerts-creating.md)。
- 使用[日志搜索](log-analytics-log-searches.md)查看活动日志中的详细信息。

