---
title: 如何使用用于 VM 的 Azure Monitor（预览版）绘制性能图表 | Microsoft Docs
description: “性能”是用于 VM 的 Azure Monitor 的一项功能，可以自动发现 Windows 和 Linux 系统上的应用程序组件并映射服务之间的通信。 本文详细介绍如何在各种场景中使用该功能。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: 0d679675758b736455c66066f3df4cb9ea43fdea
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75399284"
---
# <a name="how-to-chart-performance-with-azure-monitor-for-vms-preview"></a>如何使用用于 VM 的 Azure Monitor（预览版）绘制性能图表

用于 VM 的 Azure Monitor 包含一组针对几项关键性能指标 (KPI) 的性能图表，帮助你确定虚拟机的性能状况。 这些图表显示一段时间内的资源利用率，可让你识别瓶颈和异常，或切换到列出每个计算机的透视图，以根据所选指标查看资源利用率。 尽管处理性能时需要考虑很多元素，但用于 VM 的 Azure Monitor 会监视与处理器、内存、网络适配器和磁盘利用率相关的关键操作系统性能指标。 “性能”对运行状况监视功能做了补充，可帮助揭示可能指示系统组件发生了故障的问题，支持调整和优化以提高效率，或支持容量规划。  

## <a name="multi-vm-perspective-from-azure-monitor"></a>Azure Monitor 中的多 VM 透视图

从 Azure Monitor 中，性能功能提供了在你的订阅中或你的环境中跨工作组部署的所有受监视 Vm 的视图。 若要从 Azure Monitor 访问此功能，请执行以下步骤。 

1. 在 Azure 门户中选择“监视”。 
2. 在“见解”部分选择“虚拟机(预览)”。
3. 选择“性能”选项卡。

![VM Insights - 性能 -“前 N 项列表”视图](./media/vminsights-performance/vminsights-performance-aggview-01.png)

在“前 N 项图表”选项卡中，如果有多个 Log Analytics 工作区，请在页面顶部的“工作区”选择器中选择使用解决方案启用的工作区。 “组”选择器将返回与所选工作区相关的订阅、资源组、[计算机组](../platform/computer-groups.md)和计算机的虚拟机规模集，可使用这些返回的内容对此页和其他页的图表中显示的结果进行进一步的筛选。 你的选择仅应用于性能功能，不会延伸到运行状况或映射。  

默认情况下，图表会显示过去 24 小时的信息。 使用“TimeRange”选择器可以查询最长 30 天的历史时间范围，以便显示过去观测到的性能状况。

页面上显示的五个容量利用率图表为：

* CPU 利用率百分比 - 显示平均处理器利用率最高的 5 台计算机 
* 可用内存 - 显示平均可用内存量最低的 5 台计算机 
* 已用逻辑磁盘空间百分比 - 显示所有磁盘卷中平均已用磁盘空间百分比最高的 5 台计算机 
* 字节发送速率 - 显示平均发送字节数最高的 5 台计算机 
* 字节接收速率-显示最高五台计算机，接收的字节数最高。 

单击其中任何一个图表右上角的 "固定" 图标，即可将所选图表固定到上次查看的 Azure 仪表板。  在仪表板中，可以调整图表的大小和重新定位图表。 从仪表板中选择图表将重定向到用于 VM 的 Azure Monitor 并加载正确的作用域和视图。  

单击其中五个图表上的 "固定" 图标左侧的图标会打开**前 N 个列表**视图。  在此处，可以查看列表视图中单个 VM 的性能指标对应的资源使用率，以及哪台计算机有攀升到最高值的趋势。  

![所选性能指标的“前 N 项列表”视图](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

单击虚拟机时，"**属性**" 窗格将在右侧展开以显示选定项的属性，如操作系统报告的系统信息、Azure VM 的属性等。单击 "**快速链接**" 部分下的某个选项会直接从所选 VM 将你重定向到该功能。  

![虚拟机属性窗格](./media/vminsights-performance/vminsights-properties-pane-01.png)

切换到“聚合图表”选项卡可以查看按平均或百分位测量值筛选的性能指标。  

![VM Insights - 性能 - 聚合视图](./media/vminsights-performance/vminsights-performance-aggview-02.png)

提供以下容量利用率图表：

* CPU 利用率百分比 - 默认显示平均值和极限第 95 百分位数 
* 可用内存-显示平均值、前5个和第10个百分点的默认值 
* 已用逻辑磁盘空间百分比 - 默认显示平均值和第 95 百分位数 
* 字节发送速率 - 默认显示平均发送字节数 
* 字节接收速率 - 默认显示平均接收字节数

还可以在百分位选择器中选择“平均值”、“最小值”、“最大值”、“第 50 百分位”、“第 90 百分位”和“第 95 百分位”，来更改时间范围内图表的粒度级。

若要在列表视图中查看单个 VM 的资源利用率，并查看哪台计算机的趋势和使用率最高，请选择**前 N 个列表**选项卡。 "**前 N 个列表**" 页显示了前20台计算机，其最大*CPU 使用率百分比*为95%。  可以选择“加载更多”查看更多计算机，此时结果将会展开，以显示前 500 台计算机。 

>[!NOTE]
>该列表不能一次性显示 500 台以上的计算机。  
>

![“前 N 项列表”页示例：](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

若要根据列表中的特定虚拟机筛选结果，请在“按名称搜索”文本框中输入其计算机名称。  

若要查看不同性能指标中的利用率，请在“指标”下拉列表中，选择“可用内存”、“已用逻辑磁盘空间百分比”、“网络接收字节数/秒”或“网络发送字节数/秒”，此时列表将会更新，以显示限定为该指标的利用率。  

在列表中选择某个虚拟机时，页面右侧会打开“属性”面板，可从中选择“性能详细信息”。  随后会打开限定为该 VM 的“虚拟机详细信息”页，与直接从 Azure VM 访问“VM Insights 性能”时的体验类似。  

## <a name="view-performance-directly-from-an-azure-vm"></a>直接从 Azure VM 查看性能

若要直接从虚拟机访问，请执行以下步骤。

1. 在 Azure 门户中，选择“虚拟机”。 
2. 在列表中，选择 VM 并“监视”分区中选择“见解(预览)”。  
3. 选择“性能”选项卡。 

此页面不仅包含性能利用率图表，而且还包含一个表格，其中显示了发现的每个逻辑磁盘、其容量、利用率，以及按每个度量列出的总平均值。  

提供以下容量利用率图表：

* CPU 利用率百分比 - 默认显示平均值和极限第 95 百分位数 
* 可用内存-显示平均值、前5个和第10个百分点的默认值 
* 已用逻辑磁盘空间百分比 - 默认显示平均值和第 95 百分位数 
* 逻辑磁盘 IOPS - 默认显示平均值和第 95 百分位数
* 逻辑磁盘 MB/秒 - 默认显示平均值和第 95 百分位数
* 最大逻辑磁盘已用百分比 - 默认显示平均值和第 95 百分位数
* 字节发送速率 - 默认显示平均发送字节数 
* 字节接收速率 - 默认显示平均接收字节数

单击任意一个图表右上角的固定图标可将所选图表固定到最近查看的 Azure 仪表板。 在仪表板中，可以调整图表的大小和重新定位图表。 从仪表板中选择图表将重定向到用于 VM 的 Azure Monitor 并加载 VM 的 "性能详细信息" 视图。  

![直接从 VM 查看 VM insights 性能](./media/vminsights-performance/vminsights-performance-directvm-01.png)

## <a name="view-performance-directly-from-an-azure-virtual-machine-scale-set"></a>直接从 Azure 虚拟机规模集查看性能

若要直接从 Azure 虚拟机规模集访问，请执行以下步骤。

1. 在 Azure 门户中，选择 "**虚拟机规模集**"。
2. 从列表中选择一个 VM，并在 "**监视**" 部分选择 "**见解（预览版）** " 查看 "**性能**" 选项卡。

此页将加载 Azure Monitor 性能视图，范围为所选规模集。 这使您可以在每组被监视指标内查看规模集中的前 N 个实例，查看规模集内的聚合性能，并查看规模集的单个实例中所选指标的趋势。 通过从列表视图中选择一个实例，可以将其映射或导航到该实例的详细性能视图中。

单击任意一个图表右上角的固定图标可将所选图表固定到最近查看的 Azure 仪表板。 在仪表板中，可以调整图表的大小和重新定位图表。 从仪表板中选择图表将重定向到用于 VM 的 Azure Monitor 并加载 VM 的 "性能详细信息" 视图。  

![直接从虚拟机规模集视图中获得 VM insights 性能](./media/vminsights-performance/vminsights-performance-directvmss-01.png)

>[!NOTE]
>你还可以从规模集的实例视图中访问特定实例的详细性能视图。 导航到 "**设置**" 部分下的 "**实例**"，然后选择 "**见解（预览版）** "。

## <a name="alerts"></a>警报  

VM 的 Azure Monitor 中启用的性能指标不包含预配置的预警规则。 存在与在 Azure VM 上检测到的性能问题对应的[运行状况警报](vminsights-health.md#alerts)，如 CPU 使用率高、可用内存不足、磁盘空间不足等。 但是，这些运行状况警报仅适用于启用了用于 VM 的 Azure Monitor 的所有 Vm。 

但是，我们只能在 Log Analytics 工作区中收集和存储所需的一部分性能指标。 如果监视策略需要包含其他各种性能指标的分析或警报才能有效评估虚拟机的容量或运行状况，或者你需要灵活地指定自己的警报条件或逻辑，可以在 Log Analytics 中配置[这些性能计数器的收集](../platform/data-sources-performance-counters.md)，并定义[日志警报](../platform/alerts-log.md)。 尽管 Log Analytics 允许对其他数据类型执行复杂分析，并提供较长的保留期来支持趋势分析，但另一方面，指标是轻量的，它们能够支持近实时的方案。 它们由 [Azure 诊断代理](../../virtual-machines/windows/monitor.md)收集并存储在 Azure Monitor 指标存储中，使你能够以较低的延迟和成本创建警报。

在配置这些附加指标和警报规则的收集之前，请查看[使用 Azure Monitor 收集指标和日志](../platform/data-platform.md)的概述，以了解基本差异和其他注意事项。  

## <a name="next-steps"></a>后续步骤

- 了解如何使用用于 VM 的 Azure Monitor 附带的[工作簿](vminsights-workbooks.md)进一步分析性能和网络指标。  

- 若要了解已发现的应用程序依赖关系，请参阅[查看用于 VM 的 Azure Monitor 映射](vminsights-maps.md)。
