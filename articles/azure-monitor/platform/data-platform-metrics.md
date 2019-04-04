---
title: Azure Monitor 中的指标 |Microsoft Docs
description: 介绍 Azure Monitor 中的指标是轻量的监视数据，能够支持附近实时方案。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 1027398a1a7f790adedf6c7eebed44a8db501b8a
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905029"
---
# <a name="metrics-in-azure-monitor"></a>Azure Monitor 中的指标

> [!NOTE]
> Azure Monitor 数据平台基于两种基本数据类型：指标和日志。 本文介绍了指标。 请参阅[Azure Monitor 中的日志](data-platform-logs.md)有关详细说明的日志和[Azure Monitor 数据 platforn](data-platform.md)有关的两个比较。


Azure Monitor 中的指标是轻量和能够附近实时方案，使其特别适用于支持警报和快速检测到的问题。 本文介绍如何构造指标，可以使用它们，执行的操作，并标识数据存储在度量值的不同数据源。

## <a name="what-are-metrics"></a>什么是指标？
指标是数字值，用于描述系统某些方面在特定时间的情况。 系统收集指标按固定间隔，可用于触发的警报，因为它们通常情况下，可以进行采样，并且使用相对较简单的逻辑可以快速触发警报。

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>使用 Azure Monitor 指标可以做什么？
下表列出了可以在 Azure Monitor 中使用指标数据的不同方法。

|  |  |
|:---|:---|
| 分析 | 使用[指标资源管理器](metrics-charts.md)来分析收集的指标图表上比较来自不同的资源的指标。 |
| 可视化 | 将从指标资源管理器图表固定[Azure 仪表板](../learn/tutorial-app-dashboards.md)。<br>创建[工作簿](../app/usage-workbooks.md)来组合和多个的交互式报表中的数据集。导出到查询的结果[Grafana](grafana-plugin.md)利用其仪表板建设并结合其他数据源。 |
| 警报 | 配置[指标预警规则](alerts-metric.md)的发送通知或执行[自动化操作](action-groups.md)指标值超过阈值时。 |
| 自动化 |  使用[自动缩放](autoscale-overview.md)来增加或减少资源基于超出阈值的指标值。 |
| 导出 | [将指标路由到日志](diagnostic-logs-stream-log-store.md)分析中 Azure Monitor 指标以及在 Azure Monitor 日志中的数据的数据并将其存储的时间超过 93 天的指标值。<br>Stream 的指标[事件中心](stream-monitoring-data-event-hubs.md)以将它们路由到外部系统。 |
| 检索 | 从命令行使用访问指标值[PowerShell cmdlet](https://docs.microsoft.com/powershell/module/az.applicationinsights)<br>从自定义应用程序使用访问指标值[REST API](rest-api-walkthrough.md)。<br>从命令行使用访问指标值[CLI](/azure/monitor/metrics)。 |
| 存档 | 出于符合性、审核或脱机报告目的，对资源的性能或运行状况历史记录进行 [存档](..//learn/tutorial-archive-data.md)。 |


## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>如何为结构化的 Azure Monitor 指标中的数据？
通过 Azure Monitor 指标收集的数据存储在时间序列数据库针对分析加盖时间戳数据进行了优化。 跃点值的每个集是包含以下属性的时间序列：

* 收集值的时间
* 与之关联的资源值
* 命名空间的作用类似于指标类别
* 指标名称
* 值本身
* 某些指标可能具有多个维度中所述[多维度指标](#multi-dimensional-metrics)。 自定义指标最多可以包含 10 个维度。

Azure 中的指标存储 93 天。 你可以[将平台的 Azure Monitor 资源指标发送到 Log Analytics 工作区](diagnostic-logs-stream-log-store.md)了解长期趋势。

## <a name="multi-dimensional-metrics"></a>多维指标
为指标数据挑战之一是，它通常具有限制要收集的值为提供的上下文信息。 Azure 监视器解决了这一挑战与多维度指标。 指标维度是携带附加数据来描述指标值的名称/值对。 例如，某个指标_可用磁盘空间_可能具有名为的维度_驱动器_值_c:_， _d:_，它允许查看或者可用磁盘空间，在所有驱动器，或者为每个驱动器单独。

以下示例演示了名为“网络吞吐量”的假设指标的两个数据集。 第一个数据集不包含维度。 第二个数据集使用两个维度（_IP 地址_和_方向_）显示值：

### <a name="network-throughput"></a>网络吞吐量

| Timestamp     | 指标值 |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1,331.8 Kbps |
| 8/9/2017 8:15 | 1,141.4 Kbps |
| 8/9/2017 8:16 | 1,110.2 Kbps |

上述不包含维度的指标只能够回答类似“在某个给定时间我的网络吞吐量是多少？”的基本问题

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>网络吞吐量 + 两个维度（“IP 地址”和“方向”）

| Timestamp     | “IP”维度   | “方向”维度 | 指标值|
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

## <a name="interacting-with-azure-monitor-metrics"></a>与 Azure Monitor 指标进行交互
使用[指标资源管理器](metrics-charts.md)以交互方式分析指标数据库中的数据，并随着时间的推移图表的多个指标的值。 您可以将图表固定到仪表板查看与其他可视化效果。 还可以使用 [Azure 监视 REST API](rest-api-walkthrough.md) 检索指标。

![指标资源管理器](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Azure Monitor 指标源
Azure Monitor 从三个基本源收集指标。 一旦在 Azure Monitor 指标数据库中收集这些指标，它们可以一起计算而不考虑其源。

**平台指标**由 Azure 资源创建，可用于洞察这些资源的运行状况和性能。 每种资源创建[一组非重复性指标](metrics-supported.md)，无需进行任何配置。 除非另有指定指标的定义中从 Azure 资源在一分钟的频率收集平台指标。 

**来宾 OS 指标**收集来自虚拟机的来宾操作系统。 启用 Windows 的虚拟机的来宾 OS 指标[Windows 诊断扩展 (WAD)](../platform/diagnostics-extension-overview.md)以及与 Linux 虚拟机[InfluxData Telegraf 代理](https://www.influxdata.com/time-series-platform/telegraf/)。

**应用程序指标**由 Application Insights 为受监视的应用程序创建，可帮助检测性能问题，以及跟踪应用程序的用法趋势。 此类指标包括“服务器响应时间”和“浏览器异常”等值。

**自定义指标**是除了会自动提供的标准度量值定义的度量值。 你可以[应用程序中定义自定义指标](../app/api-custom-events-metrics.md)的 Application Insights 进行监视，或创建用于 Azure 服务使用自定义指标[自定义指标 API](metrics-store-custom-rest-api.md)。


## <a name="next-steps"></a>后续步骤

- 详细了解如何[Azure Monitor 数据平台](data-platform.md)。
- 了解如何[Azure Monitor 中的日志数据](data-platform-logs.md)。
- 了解适用于 Azure 中不同资源的[监视数据](data-sources.md)。
