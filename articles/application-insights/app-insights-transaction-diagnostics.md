---
title: "Azure Application Insights 事务诊断 | Microsoft Docs"
description: "Application Insights 端到端事务诊断"
services: application-insights
documentationcenter: .net
author: SoubhagyaDash
manager: victormu
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 01/19/2018
ms.author: sdash
ms.openlocfilehash: 8c1d8600b7f4aaa1e95f4acfbbdd55fdbfebb8fb
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="unified-cross-component-transaction-diagnostics"></a>统一的跨组件事务诊断

此体验当前以预览版提供，它替换了用于服务器端请求、依赖项和异常的现有诊断边栏选项卡。

此预览版引入了一种全新的统一诊断体验，将所有受 Application Insights 监视的组件中的服务器端遥测关联到一个单独的视图。 是否拥有多个资源和单独的检测密钥并不重要；Application Insights 可检测基础关系，并可用于轻松诊断导致事务缓慢或失败的应用程序组件、依赖项或异常。

## <a name="what-does-component-mean-in-the-context-of-application-insights"></a>在 Application Insights 的上下文中，组件意味着什么？

组件是分布式/微服务应用程序的可独立部署的部件。 开发者或操作团队具有代码级可见性，或有权访问由这些应用程序组件生成的遥测。

* 组件与“观察到的”外部依赖项（如 SQL 和 EventHub 等）不同，团队/组织可能无权访问（代码或遥测）。
* 组件在任意数目的服务器/角色/容器实例上运行。
* 组件可以是单独的 Application Insights 检测密钥（即使订阅并不相同），或是向单个 Application Insights 检测密钥报告的不同角色。 新体验显示了所有组件的详细信息，而不论其设置方式。

> [!Tip]
> 要获得最佳结果，请确保使用最新 Application Insights 稳定 SDK 检测了所有组件。 如果存在不同的 Application Insights 资源，请确保具有查看其遥测的相应权限。

## <a name="enable-and-access"></a>启用和访问
从[预览列表](app-insights-previews.md)启用“统一详细信息：E2E 事务诊断”

![启用预览](media/app-insights-e2eTxn-diagnostics/previews.png)

此预览当前适用于服务器端请求、依赖项及异常。 可以从“搜索结果”、“性能”或“失败”会审体验访问新体验。 预览版替换了相应的经典详细信息边栏选项卡。

![性能示例](media/app-insights-e2eTxn-diagnostics/performanceSamplesClickThrough.png)

## <a name="transaction-diagnostics-experience"></a>事务诊断体验
此视图包含三个主要部分：跨组件事务图、来自特定组件操作的所有遥测的时序列表，以及位于左侧的详细信息窗格（显示任意选定遥测项的详细信息）。

![关键部分](media/app-insights-e2eTxn-diagnostics/3partsCrossComponent.png)

### <a name="1-cross-component-transaction-chart"></a>[1] 跨组件事务图

此图提供使用水平条的时间线，表示请求的持续时间及各组件间的依赖项。 收集的任何异常也会标记在时间线上。

* 此图的最上面一行表示入口点，即在此事务中调用的第一个组件的传入请求。 持续时间是指完成事务所用的总时间。
* 对外部依赖项的任何调用都是简单的不可折叠行，以图标表示依赖项类型。
* 对其它组件的调用均属于可折叠行。 每一行都与在组件处调用的特定操作对应。
* 默认情况下，图上会显示最初选择的请求、依赖项或异常。
* 选择任意行，在右侧查看其详细信息。 单击“打开探查器跟踪”或“打开调试快照”，在相应的详细信息窗格中查看代码级诊断信息。

> [!NOTE]
对其他组件的调用包括两行：一行表示来自调用方组件的出站调用（依赖项），另一行对应于调用的组件处的入站请求。 可通过前导图标和样式不同的持续时间条来区分它们。

### <a name="2-time-sequenced-telemetry-of-the-selected-component-operation"></a>[2] 所选组件操作的时序遥测

在跨组件事务图中选择的任何行都与在特定组件调用的操作相关。 底部的标题中反映了此所选组件操作。 打开此部分，可查看与该特定操作相关的所有遥测的平面时间序列。 可以在此列表中选择任意遥测项，并在右侧查看相应的详细信息。

![所有遥测的时间序列](media/app-insights-e2eTxn-diagnostics/allTelemetryDrawerOpened.png)

### <a name="3-details-pane"></a>[3] 详细信息窗格

此窗格在左侧两部分的任一部分中显示所选项的详细信息。 “显示全部”列出了收集的所有标准属性。 所有自定义属性都单独列在标准集之下。

![异常详细信息](media/app-insights-e2eTxn-diagnostics/exceptiondetail.png)

## <a name="profiler-and-snapshot-debugger"></a>探查器和快照调试器

[Application Insights 探查器](app-insights-profiler.md)或[快照调试器](app-insights-snapshot-debugger.md)有助于对性能和失败问题进行代码级诊断。 通过此体验，仅需一次单击即可查看任意组件的探查器跟踪或快照。

如果无法让 Profiler 正常工作，请联系 **serviceprofilerhelp@microsoft.com**

如果无法让快照调试器正常工作，请联系 **snapshothelp@microsoft.com**

![调试程序集成](media/app-insights-e2eTxn-diagnostics/debugSnapshot.png)

## <a name="faq"></a>常见问题

图中只显示了一个组件，其余组件仅显示为外部依赖项，没有显示有关这些组件内发生的情况的详细信息。

可能的原因：

* 是否使用 Application Insights 检测了其他组件？
* 是否使用了最新的稳定 Application Insights SDK？
* 如果这些组件是单独的 Application Insights 资源，你是否具有访问其遥测的所需权限？

如果确实具有权限，且使用了最新的 Application Insights SDK 来检测组件，请通过右上方的反馈通道告知我们。

我只有外部依赖项。我需要了解此预览版吗？

是的。 该新体验将所有相关服务器端遥测统一成单个视图。 将来，旧版详细信息边栏选项卡会替换为此体验，因此请试用此体验，并向我们提供反馈。 以下是只有单个组件事务时的屏幕：

![单组件体验](media/app-insights-e2eTxn-diagnostics/singleComponent.png)

依赖项具有重复的行。这是正常情况吗？

当前，出站依赖项调用与入站请求分开显示。 通常情况下，这两种调用大致相同，但持续时间值因网络往返而不同。 可通过前导图标和样式不同的持续时间条来区分它们。 这份数据演示文稿是否包含令人困惑的内容？ 欢迎提供反馈！

不同组件实例间的时钟偏差情况如何？

在事务图中针对时钟偏差调整了时间线。 可以在详细信息窗格中查看确切的时间戳，或使用“Analytics”进行查看。

为什么新体验中缺少大部分相关项查询？

这是设计使然。 所有组件的全部相关项都已显示在左侧（顶部和底部）。 新体验中有两个左侧未包含的相关项：此事件发生前后五分钟内的所有遥测，以及用户时间线。

为什么新体验不提供旧版边栏选项卡中我喜欢的功能？

欢迎提供反馈！ 我们希望在此体验正式发布前解决你关切的问题，届时，旧版视图会被弃用。 目前可以禁用预览版，并返回到旧版边栏选项卡。

## <a name="known-issues"></a>已知问题

* 应用程序映射的失败示例链接至旧版详细信息边栏选项卡。
* 搜索结果中基于 autocluster 的见解链接至旧版详细信息边栏选项卡。
* 工作项集成在新体验中不可用。
