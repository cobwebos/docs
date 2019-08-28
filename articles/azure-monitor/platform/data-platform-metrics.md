---
title: Azure Monitor 中的指标 |Microsoft Docs
description: 介绍 Azure Monitor 中的指标：能够为近实时方案提供支持的轻型监视数据。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: ea95b91d57255db8f638e600d57a98db314cd80f
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70113520"
---
# <a name="metrics-in-azure-monitor"></a>Azure Monitor 中的指标

> [!NOTE]
> Azure Monitor 数据平台基于两种基本数据类型：指标和日志。 本文介绍指标。 有关日志的详细介绍，请参阅 [Azure Monitor 中的日志](data-platform-logs.md)；有关日志与指标的比较，请参阅 [Azure Monitor 数据平台](data-platform.md)。

Azure Monitor 中的指标是能够为近实时方案提供支持的轻型数据，因此，它们特别适合用于警报和快速检测问题。 本文将介绍指标的构建方式、可对指标执行哪些操作，以及如何识别需要在指标中存储数据的不同数据源。

## <a name="what-are-metrics"></a>什么是指标？
指标是数字值，用于描述系统某些方面在特定时间的情况。 指标是按固定间隔收集的，可用于警报，因为它们可以频繁采样，而警报则可以使用相对简单的逻辑快速触发。

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>可对 Azure Monitor 指标执行哪些操作？
下表列出了 Azure Monitor 中的指标数据的不同使用方式。

|  |  |
|:---|:---|
| 分析 | 使用[指标资源管理器](metrics-charts.md)可以在图表中分析收集的指标，并比较来自不同资源的指标。 |
| 可视化 | 将指标资源管理器中的图表固定到 [Azure 仪表板](../learn/tutorial-app-dashboards.md)。<br>创建一个[工作簿](../app/usage-workbooks.md)用于在交互式报表中合并多个数据集。将查询结果导出到 [Grafana](grafana-plugin.md)，以利用其仪表板功能以及合并其他数据源。 |
| 警报 | 配置指标[警报规则](alerts-metric.md)，以便在指标值超过阈值时发送通知或执行[自动化操作](action-groups.md)。 |
| 自动化 |  根据超过阈值的指标值，使用[自动缩放](autoscale-overview.md)来增加或减少资源。 |
| 导出 | [将指标路由到日志](diagnostic-logs-stream-log-store.md)，以连同 Azure Monitor 日志中的数据一起分析 Azure Monitor 指标中的数据，并将指标值存储 93 天以上。<br>将指标流式传输到[事件中心](stream-monitoring-data-event-hubs.md)，以将其路由到外部系统。 |
| 检索 | 使用 [PowerShell cmdlet](https://docs.microsoft.com/powershell/module/az.applicationinsights) 从命令行访问指标值<br>使用 [REST API](rest-api-walkthrough.md) 从自定义应用程序访问指标值。<br>使用 [CLI](/cli/azure/monitor/metrics) 从命令行访问指标值。 |
| 存档 | 出于符合性、审核或脱机报告目的，对资源的性能或运行状况历史记录进行 [存档](..//learn/tutorial-archive-data.md)。 |

## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>Azure Monitor 指标中的数据是如何构建的？
Azure Monitor 指标收集的数据存储在时序数据库中，该数据库经过优化，可用于分析带时间戳的数据。 每组指标值是包含以下属性的时序：

* 值的收集时间
* 与关联的资源
* 类似于指标类别的命名空间
* 指标名称
* 值本身
* 如[多维指标](#multi-dimensional-metrics)中所述，某指标可能包含多个维度。 自定义指标最多可以包含 10 个维度。

## <a name="multi-dimensional-metrics"></a>多维指标
指标数据的一大难题在于，它通常包含有限的信息，以致无法提供所收集值的上下文。 Azure Monitor 通过多维指标解决了这一难题。 指标维度是携带附加数据来描述指标值的名称/值对。 例如，指标“可用磁盘空间”可能包含名为“驱动器”并具有值“C:”和“D:”的维度，通过此维度可以查看所有驱动器的可用磁盘空间，或者可以分别查看每个驱动器的可用磁盘空间。

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
使用[指标资源管理器](metrics-charts.md)可以交互方式分析指标数据库中的数据，并在图表中绘制多个指标在不同时间段的值。 可将图表固定到仪表板，以结合其他可视化效果一起查看这些图表。 还可以使用 [Azure 监视 REST API](rest-api-walkthrough.md) 检索指标。

![指标资源管理器](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Azure Monitor 指标的源
Azure Monitor 从三个基本源收集指标。 在 Azure Monitor 指标数据库中收集这些指标后，不管指标的源是什么，都可以统一评估这些指标。

**平台指标**由 Azure 资源创建，可用于洞察这些资源的运行状况和性能。 每种资源创建[一组非重复性指标](metrics-supported.md)，无需进行任何配置。 除非指标定义中另有指定，否则，将以一分钟频率从 Azure 资源收集平台指标。 

**来宾 OS 指标**是从虚拟机的来宾操作系统收集的。 对于 Windows 虚拟机和 Linux 虚拟机，可以分别使用 [Windows 诊断扩展 (WAD)](../platform/diagnostics-extension-overview.md) 和 [InfluxData Telegraf 代理](https://www.influxdata.com/time-series-platform/telegraf/)启用来宾 OS 指标。

**应用程序指标**由 Application Insights 为受监视的应用程序创建，可帮助检测性能问题，以及跟踪应用程序的用法趋势。 此类指标包括“服务器响应时间”和“浏览器异常”等值。

**自定义指标**是在自动提供的标准指标之外定义的指标。 可以在受 Application Insights 监视的[应用程序中定义自定义指标](../app/api-custom-events-metrics.md)，或者使用[自定义指标 API](metrics-store-custom-rest-api.md) 为 Azure 服务创建自定义指标。

## <a name="retention-of-metrics"></a>指标的保留期
对于 Azure 中的大多数资源，指标存储 93 天。 有一些例外情况：

**来宾 OS 指标**
-   **经典来宾操作系统指标**。 这些是由[Windows 诊断扩展 (WAD)](../platform/diagnostics-extension-overview.md)或[Linux 诊断扩展 (LAD)](../../virtual-machines/extensions/diagnostics-linux.md)收集并路由到 Azure 存储帐户的性能计数器。 这些指标的保留期为14天。
-   **发送给 Azure Monitor 指标的来宾 OS 指标**。 这些是由 Windows 诊断扩展 (WAD) 收集的性能计数器, 并通过 Linux 计算机上的[InfluxData Telegraf 代理](https://www.influxdata.com/time-series-platform/telegraf/)发送到[Azure Monitor 接收器](diagnostics-extension-overview.md#data-storage)。 这些指标的保留期为93天。
-   **Log Analytics 代理收集的来宾 OS 指标**。 这些是由 Log Analytics 代理收集并发送到 Log Analytics 工作区的性能计数器。 这些指标的保留期为31天, 最长可延长到2年。

**Application Insights 基于日志的指标**。 
- 在后台，[基于日志的指标](../app/pre-aggregated-metrics-log-metrics.md)将转换为日志查询。 其保留期与基础日志中事件的保留期相匹配。 对于 Application Insights 资源，日志存储 90 天。


> [!NOTE]
> 可[将 Azure Monitor 资源的平台指标发送到 Log Analytics 工作区](diagnostic-logs-stream-log-store.md)以分析长期趋势。





## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Monitor 数据平台](data-platform.md)。
- 了解 [Azure Monitor 中的日志数据](data-platform-logs.md)。
- 了解适用于 Azure 中不同资源的[监视数据](data-sources.md)。
