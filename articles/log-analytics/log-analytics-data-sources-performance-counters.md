---
title: "Log Analytics 中的 Windows 和 Linux 性能计数器 | Microsoft Docs"
description: "性能计数器由 Log Analytics 收集，用于分析 Windows 和 Linux 代理的性能。  本文介绍了如何为 Windows 和 Linux 代理配置性能计数器的集合、它们在 OMS 存储库中的存储详情和如何在 OMS 门户中对他们对其分析。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 20e145e4-2ace-4cd9-b252-71fb4f94099e
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: b4d326064059b42cf2bf059184066c9acb4dcfd0


---
# <a name="windows-and-linux-performance-data-sources-in-log-analytics"></a>Log Analytics 中的 Windows 和 Linux 性能数据源
Windows 和 Linux 中的性能计数器提供对硬件组件、操作系统和应用程序性能的见解。  除聚合性能数据以用于长期分析和报告外，Log Analytics 还可以定期收集性能计数器以进行近实时 (NRT) 分析。

![性能计数器](media/log-analytics-data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>配置性能计数器
可以从 [Log Analytics 设置中的数据菜单](log-analytics-data-sources.md#configuring-data-sources)配置性能计数器。

首次为新的 OMS 工作区配置 Windows 或 Linux 性能计数器时，可以选择快速创建几个通用的计数器。  将这些计数器在一个复选框中依次列出。  请确保已选中所有想要首先创建的计数器，然后单击“**添加选定的性能计数器**。

![配置 Windows 性能计数器](media/log-analytics-data-sources-performance-counters/configure-windows.png)

遵循以下步骤添加要收集的新 Windows 性能计数器。

1. 按照 *object(instance)\counter* 格式在文本框中键入计数器的名称。  开始键入时，将会显示通用计数器的匹配列表。  可以选择列表中的计数器或者键入自己的计数器。  还可以通过指定 *object\counter* 返回特定计数器的所有实例。 
2. 单击 **+** 或按 **Enter** 将计数器添加到列表中。
3. 添加计数器后，计数器将把 10 秒作为“**采样间隔**”的默认时间。  如果想要降低收集的性能数据的存储要求，可以将此值更改为更高值，最高可达 1800 秒（30 分钟）。
4. 添加完计数器后，单击屏幕顶部的“**保存**”按钮保存配置。

![配置 Linux 性能计数器](media/log-analytics-data-sources-performance-counters/configure-linux.png)

遵循以下步骤添加要收集的新 Linux 性能计数器。

1. 默认情况下，所有配置更改均会自动推送到所有代理。  对于 Linux 代理，配置文件发送到 Fluentd 数据收集器。  如果想在每个 Linux 代理上手动修改此文件，则取消选中“*将下面的配置应用到我的 Linux 计算机*”框即可。
2. 按照 *object(instance)\counter* 格式在文本框中键入计数器的名称。  开始键入时，将会显示通用计数器的匹配列表。  可以选择列表中的计数器或者键入自己的计数器。  
3. 单击 **+** 或按 **Enter** 将计数器添加到此对象的其他计数器列表中。
4. 一个对象的所有计数器使用相同的“**采样间隔**”。  默认为 10 秒。  如果想要降低收集的性能数据的存储要求，可以将此值更改为更高值，最高可达 1800 秒（30 分钟）。
5. 添加完计数器后，单击屏幕顶部的“**保存**”按钮保存配置。

## <a name="data-collection"></a>数据收集
Log Analytics 以指定的采样间隔在已安装相应计数器的所有代理上收集所有指定的性能计数器。  该数据不会聚合，在 OMS 订阅指定的时间期限内可在所有的日志搜索视图中获取其原始数据。

## <a name="performance-record-properties"></a>性能记录属性
性能记录具有 **Perf** 类型，并且具有下表中的属性。

| 属性 | 说明 |
|:--- |:--- |
| 计算机 |从中收集事件的计算机。 |
| CounterName |性能计数器的名称 |
| CounterPath |性能计数器的完整路径，以 \\\\\<Computer>\\object(instance)\\counter 格式显示。 |
| CounterValue |计数器的数值。 |
| InstanceName |事件实例的名称。  无实例时为空。 |
| ObjectName |性能对象的名称 |
| SourceSystem |从中收集数据的代理类型。 <br> OpsManager – Windows 代理，直接连接或通过 SCOM 连接 <br> Linux - 所有 Linux 代理  <br> AzureStorage – Azure 诊断 |
| TimeGenerated |对数据进行采样的日期和时间。 |

## <a name="sizing-estimates"></a>大小估计值
 以 10 秒间隔收集特定计数器的粗略估计值约为每个实例每天 1 MB。  可以使用以下公式估计特定计数器的存储要求。

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-searches-with-performance-records"></a>含有性能记录的日志搜索
下表提供了检索性能记录的不同日志搜索的示例。

| 查询 | 说明 |
|:--- |:--- |
| Type=Perf |所有性能数据 |
| Type=Perf Computer="MyComputer" |特定计算机中的所有性能数据 |
| Type=Perf CounterName="Current Disk Queue Length" |特定计数器的所有性能数据 |
| Type=Perf (ObjectName=Processor) CounterName="% Processor Time" InstanceName=_Total &#124; measure Avg(Average) as AVGCPU  by Computer |所有计算机的平均 CPU 使用率 |
| Type=Perf (CounterName="% Processor Time") &#124;  measure max(Max) by Computer |所有计算机的最大 CPU 使用率 |
| Type=Perf ObjectName=LogicalDisk CounterName="Current Disk Queue Length" Computer="MyComputerName" &#124; measure Avg(Average) by InstanceName |指定计算机的所有实例上的当前磁盘队列平均长度 |
| Type=Perf CounterName="DiskTransfers/sec" &#124; measure percentile95(Average) by Computer |每秒所有计算机上磁盘传输的第 95 百分位数 |
| Type=Perf CounterName="% Processor Time" InstanceName="_Total"  &#124; measure avg(CounterValue) by Computer Interval 1HOUR |每小时所有计算机 CPU 使用率的平均值 |
| Type=Perf Computer="MyComputer" CounterName=%* InstanceName=_Total &#124; measure percentile70(CounterValue) by CounterName Interval 1HOUR |每小时特定计算机的每个 % 百分比计数器的第 70 百分位数 |
| Type=Perf CounterName="% Processor Time" InstanceName="_Total"  (Computer="MyComputer") &#124; measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR |每小时特定计算机的 CPU 使用率的平均值、最小值、最大值和第 75 百分位数 |

## <a name="viewing-performance-data"></a>查看性能数据
运行性能数据的日志查询时，将默认显示“**日志**”视图。  若要以图像形式查看数据，单击“**指标**”。  若要详细查看图形，单击计数器旁的 **+**。  

![指标视图折叠](media/log-analytics-data-sources-performance-counters/metricscollapsed.png)

如果你选择的时间范围为 6 小时或更短，则图像会每隔几秒更新一次。  实时数据以浅蓝色显示在图形右侧。

![指标视图展开后显示实时数据](media/log-analytics-data-sources-performance-counters/metricsexpanded.png)

若要聚合日志搜索中的性能数据，请参阅 [OMS 中的按需指标聚合和可视化](http://blogs.technet.microsoft.com/msoms/2016/02/26/on-demand-metric-aggregation-and-visualization-in-oms/)。

## <a name="next-steps"></a>后续步骤
* 了解[日志搜索](log-analytics-log-searches.md)以便分析从数据源和解决方案中收集的数据。  
* 将收集的数据导出到 [Power BI](log-analytics-powerbi.md) 以进行其他可视化操作和分析。




<!--HONumber=Nov16_HO3-->


