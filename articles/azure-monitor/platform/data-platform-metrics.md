---
title: Azure Monitor 中的指标 |Microsoft Docs
description: 介绍 Azure Monitor 中的指标，这些指标是能够支持准实时方案的轻型监视数据。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 80bbf83da17d833c4f8bb1abac9610d70e9a23cb
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274822"
---
# <a name="metrics-in-azure-monitor"></a>Azure Monitor 中的指标

> [!NOTE]
> Azure Monitor 数据平台基于两种基本数据类型：度量值和日志。 本文介绍指标。 有关日志的详细说明，请参阅[Azure Monitor 中的日志](data-platform-logs.md)，并[Azure Monitor 数据平台](data-platform.md)对两者进行比较。

Azure Monitor 中的指标是轻型的并且能够支持近乎实时的方案，这使得它们特别适用于警报和快速检测问题。 本文介绍如何构造度量值，以及如何处理度量值的不同数据源。

## <a name="what-are-metrics"></a>什么是指标？
指标是数字值，用于描述系统某些方面在特定时间的情况。 度量值是定期收集的，并且可用于警报，因为它们可以频繁采样，并且可以使用相对简单的逻辑快速触发警报。

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>您可以对 Azure Monitor 指标执行哪些操作？
下表列出了可以在 Azure Monitor 中使用指标数据的不同方式。

|  |  |
|:---|:---|
| 分析 | 使用[指标资源管理器](metrics-charts.md)分析图表上的收集的指标，并比较不同资源的指标。 |
| 可视化 | 将指标资源管理器中的图表固定到[Azure 仪表板](../learn/tutorial-app-dashboards.md)。<br>创建要与交互式报表中的多个数据集组合在一起的[工作簿](../app/usage-workbooks.md)。将查询结果导出到[Grafana](grafana-plugin.md) ，以利用其仪表板建设并将其与其他数据源组合。 |
| 警报 | 配置[指标警报规则](alerts-metric.md)，以便在指标值超过阈值时，发送通知或执行[自动操作](action-groups.md)。 |
| 自动化 |  使用 "[自动缩放](autoscale-overview.md)" 根据阈值来增加或减少资源。 |
| Export | 将[度量值路由到日志](resource-logs-collect-storage.md)，以便分析 Azure Monitor 度量值中的数据以及 Azure Monitor 日志中的数据，以及存储超过93天的指标值。<br>将指标流式传输到[事件中心](stream-monitoring-data-event-hubs.md)，以将其路由到外部系统。 |
| 检索 | 使用[PowerShell cmdlet](https://docs.microsoft.com/powershell/module/az.applicationinsights)从命令行访问指标值<br>使用[REST API](rest-api-walkthrough.md)从自定义应用程序访问指标值。<br>使用[CLI](/cli/azure/monitor/metrics)从命令行访问指标值。 |
| 存档 | 出于符合性、审核或脱机报告目的，对资源的性能或运行状况历史记录进行 [存档](..//learn/tutorial-archive-data.md)。 |

## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>Azure Monitor 度量值如何结构化？
Azure Monitor 度量值收集的数据存储在一个时间系列数据库中，该数据库针对分析时间戳的数据进行了优化。 每组指标值都是具有以下属性的时间序列：

* 收集值的时间
* 与值关联的资源
* 作用类似于指标类别的命名空间
* 指标名称
* 值本身
* 有些指标可以有多个维度，如[多维指标](#multi-dimensional-metrics)中所述。 自定义指标最多可以包含 10 个维度。

## <a name="multi-dimensional-metrics"></a>多维指标
指标数据面临的难题之一是，它经常提供信息以提供收集值的上下文。 Azure Monitor 利用多维指标解决了这一难题。 指标维度是携带附加数据来描述指标值的名称/值对。 例如，指标_可用磁盘空间_可能有一个名为_Drive_的维度，其值为_C：_ ， _D：_ ，这允许在所有驱动器上查看可用磁盘空间，或分别为每个驱动器查看可用磁盘空间。

以下示例演示了名为“网络吞吐量”的假设指标的两个数据集。 第一个数据集不包含维度。 第二个数据集使用两个维度（_IP 地址_和_方向_）显示值：

### <a name="network-throughput"></a>网络吞吐量

| 时间戳     | 指标值 |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1,331.8 Kbps |
| 8/9/2017 8:15 | 1,141.4 Kbps |
| 8/9/2017 8:16 | 1,110.2 Kbps |

上述不包含维度的指标只能够回答类似“在某个给定时间我的网络吞吐量是多少？”的基本问题

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>网络吞吐量 + 两个维度（“IP 地址”和“方向”）

| 时间戳     | “IP”维度   | “方向”维度 | 指标值|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP="192.168.5.2" | Direction="Send"    | 646.5 Kbps |
| 8/9/2017 8:14 | IP="192.168.5.2" | Direction="Receive" | 420.1 Kbps |
| 8/9/2017 8:14 | IP="10.24.2.15"  | Direction="Send"    | 150.0 Kbps |
| 8/9/2017 8:14 | IP="10.24.2.15"  | Direction="Receive" | 115.2 Kbps |
| 8/9/2017 8:15 | IP="192.168.5.2" | Direction="Send"    | 515.2 Kbps |
| 8/9/2017 8:15 | IP="192.168.5.2" | Direction="Receive" | 371.1 Kbps |
| 8/9/2017 8:15 | IP="10.24.2.15"  | Direction="Send"    | 155.0 Kbps |
| 8/9/2017 8:15 | IP="10.24.2.15"  | Direction="Receive" | 100.1 Kbps |

此指标可以回答类似“每个 IP 地址的网络吞吐量是多少？”，以及“相对于收到的数据，发送的数据有多少？”的问题 与不包含维度的指标相比，多维指标具有更多分析值和诊断值。

## <a name="interacting-with-azure-monitor-metrics"></a>与 Azure Monitor 指标交互
使用[指标资源管理器](metrics-charts.md)以交互方式分析度量数据库中的数据，并在一段时间内绘制多个度量值的图表。 您可以将这些图表固定到仪表板，以使用其他可视化效果查看它们。 还可以使用 [Azure 监视 REST API](rest-api-walkthrough.md) 检索指标。

![指标资源管理器](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Azure Monitor 度量值的源
Azure Monitor 从三个基本源收集指标。 在 Azure Monitor 指标数据库中收集这些指标后，无论其源如何，都可以一起计算。

**平台指标**由 Azure 资源创建，可用于洞察这些资源的运行状况和性能。 每种资源创建[一组非重复性指标](metrics-supported.md)，无需进行任何配置。 除非在指标的定义中另行指定，否则平台指标将以一分钟的频率从 Azure 资源中收集。 

**来宾 OS 指标**是从虚拟机的来宾操作系统中收集的。 使用[Windows 诊断扩展（WAD）](../platform/diagnostics-extension-overview.md)为 windows 虚拟机启用来宾 OS 度量，并为具有[InfluxData Telegraf 代理](https://www.influxdata.com/time-series-platform/telegraf/)的 Linux 虚拟机启用来宾 OS 指标。

**应用程序指标**由 Application Insights 为受监视的应用程序创建，可帮助检测性能问题，以及跟踪应用程序的用法趋势。 此类指标包括“服务器响应时间”和“浏览器异常”等值。

**自定义指标**是指除了自动可用的标准指标之外，还可以定义的指标。 可以通过 Application Insights 或使用[自定义指标 API](metrics-store-custom-rest-api.md)为 Azure 服务创建自定义指标来[定义应用程序中的自定义指标](../app/api-custom-events-metrics.md)。

## <a name="retention-of-metrics"></a>指标保留
对于 Azure 中的大多数资源，指标存储为93天。 有一些例外情况：

**来宾 OS 指标**
-   **经典来宾操作系统指标**。 这些是由[Windows 诊断扩展（WAD）](../platform/diagnostics-extension-overview.md)或[Linux 诊断扩展（LAD）](../../virtual-machines/extensions/diagnostics-linux.md)收集并路由到 Azure 存储帐户的性能计数器。 这些指标的保留期为14天。
-   **发送给 Azure Monitor 指标的来宾 OS 指标**。 这些是由[Windows 诊断扩展（WAD）](diagnostics-extension-overview.md)收集并发送到[Azure Monitor 数据接收器](diagnostics-extension-overview.md#data-destinations)或通过 Linux 计算机上的[InfluxData Telegraf 代理](https://www.influxdata.com/time-series-platform/telegraf/)收集的性能计数器。 这些指标的保留期为93天。
-   **Log Analytics 代理收集的来宾 OS 指标**。 这些是由 Log Analytics 代理收集并发送到 Log Analytics 工作区的性能计数器。 这些指标的保留期为31天，最长可延长到2年。

**Application Insights 基于日志的指标**。 
- 在场景后，将[基于日志的指标](../app/pre-aggregated-metrics-log-metrics.md)转换为日志查询。 它们的保留期与基础日志中事件的保留期匹配。 对于 Application Insights 资源，日志存储了90天。


> [!NOTE]
> 可以[将 Azure Monitor 资源的平台指标发送到 Log Analytics 工作区](resource-logs-collect-storage.md)，以便进行长期趋势分析。





## <a name="next-steps"></a>后续步骤

- 详细了解[Azure Monitor 数据平台](data-platform.md)。
- 了解[Azure Monitor 中的日志数据](data-platform-logs.md)。
- 了解适用于 Azure 中不同资源的[监视数据](data-sources.md)。
