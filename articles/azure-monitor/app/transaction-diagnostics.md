---
title: Azure Application Insights 事务诊断 | Microsoft Docs
description: Application Insights 端到端事务诊断
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 01/19/2018
ms.reviewer: sdash
ms.openlocfilehash: 1ed3713fe4a6c9403be13f444d0409af459a1e70
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899576"
---
# <a name="unified-cross-component-transaction-diagnostics"></a>统一的跨组件事务诊断

统一诊断体验自动将所有受 Application Insights 监视的组件中的服务器端遥测关联到一个单独的视图。 是否拥有多个资源和单独的检测密钥并不重要。 Application Insights 可检测基础关系，并可用于轻松诊断导致事务缓慢或失败的应用程序组件、依赖项或异常。

## <a name="what-is-a-component"></a>组件是什么？

组件是分布式/微服务应用程序的可独立部署的部件。 开发者或操作团队具有代码级可见性，或有权访问由这些应用程序组件生成的遥测。

* 组件与“观察到的”外部依赖项（如 SQL 和 EventHub 等）不同，团队/组织可能无权访问（代码或遥测）。
* 组件在任意数目的服务器/角色/容器实例上运行。
* 组件可以是单独的 Application Insights 检测密钥（即使订阅并不相同），或是向单个 Application Insights 检测密钥报告的不同角色。 新体验显示了所有组件的详细信息，而不论其设置方式。

> [!NOTE]
> * **缺少相关的项链接？** 所有相关的遥测都位于左侧的[顶部](#cross-component-transaction-chart)和[底部](#all-telemetry-with-this-operation-id)部分。 

## <a name="transaction-diagnostics-experience"></a>事务诊断体验
此视图有四个关键部分：结果列表、跨组件事务图、与此操作相关的所有遥测的时序列表，以及左侧的详细信息窗格（显示任何选定遥测项的详细信息）。

![关键部分](media/transaction-diagnostics/4partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>跨组件事务图

此图提供使用水平条的时间线，表示请求的持续时间及各组件间的依赖项。 收集的任何异常也会标记在时间线上。

* 此图的最上面一行表示入口点，即在此事务中调用的第一个组件的传入请求。 持续时间是指完成事务所用的总时间。
* 对外部依赖项的任何调用都是简单的不可折叠行，以图标表示依赖项类型。
* 对其它组件的调用均属于可折叠行。 每一行都与在组件处调用的特定操作对应。
* 默认情况下，所选择的请求、依赖项或异常显示在右侧。
* 选择任意行，在[右侧查看其详细信息](#details-of-the-selected-telemetry)。 

> [!NOTE]
> 对其他组件的调用包括两行：一行表示来自调用方组件的出站调用（依赖项），另一行对应于调用的组件处的入站请求。 可通过前导图标和样式不同的持续时间条来区分它们。

## <a name="all-telemetry-with-this-operation-id"></a>所有使用此操作 ID 的遥测

本部分以与此事务相关的所有遥测的时间序列显示平面列表视图。 它还显示自定义事件以及未在事务图中显示的跟踪。 可以将此列表筛选为特定组件/调用生成的遥测。 可以在此列表中选择任意遥测项，并[在右侧查看相应的详细信息](#details-of-the-selected-telemetry)。

![所有遥测的时间序列](media/transaction-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>所选遥测数据的详细信息

此可折叠窗格显示事务图或列表中任何所选项的详细信息。 “显示全部”列出了收集的所有标准属性。 所有自定义属性都单独列在标准集之下。 单击堆栈跟踪窗口下方的“...”以获取复制跟踪的选项。 “打开探查器跟踪”或“打开调试快照”在相应的详细信息窗格中查看代码级诊断信息。

![异常详细信息](media/transaction-diagnostics/exceptiondetail.png)

## <a name="search-results"></a>搜索结果

此可折叠窗格会显示符合筛选条件的其他结果。 单击任何结果可更新上面列出的 3 个部分的相应详细信息。 我们试图找到最可能从所有组件中获得详细信息的样本，即使在任何一个组件中采样都是有效的。 这些显示为“建议的”样本。

![搜索结果](media/transaction-diagnostics/searchResults.png)

## <a name="profiler-and-snapshot-debugger"></a>探查器和快照调试器

[Application Insights 探查器](../../azure-monitor/app/profiler.md)或[快照调试器](snapshot-debugger.md)有助于对性能和失败问题进行代码级诊断。 通过此体验，仅需一次单击即可查看任意组件的探查器跟踪或快照。

如果无法运行探查器，请联系**serviceprofilerhelp\@microsoft.com**

如果无法 Snapshot Debugger 工作，请联系**snapshothelp\@microsoft.com**

![探查器集成](media/transaction-diagnostics/profilerTraces.png)

## <a name="faq"></a>常见问题解答

图中只显示了一个组件，其余组件仅显示为外部依赖项，没有显示有关这些组件内发生的情况的详细信息。

可能的原因：

* 是否使用 Application Insights 检测了其他组件？
* 是否使用了最新的稳定 Application Insights SDK？
* 如果这些组件是单独的 Application Insights 资源，你是否具有访问其遥测的所需权限？

如果确实具有权限，且使用了最新的 Application Insights SDK 来检测组件，请通过右上方的反馈通道告知我们。

*我看到依赖项的重复行。这是不是吗？*

当前，出站依赖项调用与入站请求分开显示。 通常情况下，这两种调用大致相同，但持续时间值因网络往返而不同。 可通过前导图标和样式不同的持续时间条来区分它们。 这份数据演示文稿是否包含令人困惑的内容？ 欢迎提供反馈！

不同组件实例间的时钟偏差情况如何？

在事务图中针对时钟偏差调整了时间线。 可以在详细信息窗格中查看确切的时间戳，或使用“Analytics”进行查看。

为什么新体验中缺少大部分相关项查询？

这是设计使然。 所有组件的全部相关项都已显示在左侧（顶部和底部）。 新体验中有两个左侧未包含的相关项：此事件发生前后五分钟内的所有遥测，以及用户时间线。
