---
title: "Application Insights 中的应用程序映射 | Microsoft Docs"
description: "应用组件（带有 KPI 和警报标签）之间的依赖项的可视化展示。"
services: application-insights
documentationcenter: 
author: SoubhagyaDash
manager: douge
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/18/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fc2d3c3fee5abbab0eab16c106c88c8753e703cc
ms.openlocfilehash: f7ee8a43c83b1bad53acdb71de7a66d8afaa09b2


---
# <a name="application-map-in-application-insights"></a>Application Insights 中的应用程序映射
在 [Azure Application Insights](app-insights-overview.md) 中，应用程序映射是应用程序组件的依赖项关系的可视化布局。 每个组件显示负载、性能、故障和警报等 KPI，有助于发现导致性能问题或故障的任何组件。 可从任何组件单击以获得更详细的诊断，如 Application Insights 事件。 如果应用使用了 Azure 服务，还可以单击获得 Azure 诊断，如 SQL 数据库顾问建议。

与其他图一样，你可以将应用程序映射固定到 Azure 仪表板，该映射可在其中完全正常运行。 

## <a name="open-the-application-map"></a>打开应用程序映射
从你的应用程序的概述边栏选项卡中打开映射：

![打开应用映射](./media/app-insights-app-map/01.png)

![应用映射](./media/app-insights-app-map/02.png)

该映射显示：

* 可用性测试
* 客户端组件（使用 JavaScript SDK 监视）
* 服务器端组件
* 客户端和服务器组件的依赖项

可展开和折叠依赖项链接组：

![折叠](./media/app-insights-app-map/03.png)

如果一种类型（SQL、HTTP 等）有许多依赖项，它们可能分组显示。 

![分组依赖项](./media/app-insights-app-map/03-2.png)

## <a name="spot-problems"></a>发现问题
每个节点都有相关的性能指标，例如该组件的负载、性能和故障率。 

警告图标突出显示可能的问题。 橙色警告表示请求、页面视图或依赖项调用中出现故障。 红色表示故障率高于 5%。

![故障图标](./media/app-insights-app-map/04.png)

活动警报还显示： 

![活动警报](./media/app-insights-app-map/05.png)

如果使用 SQL Azure，在对性能改进方式有建议时，会显示一个图标。 

![Azure 建议](./media/app-insights-app-map/06.png)

单击任何图标，获取更多详细信息：

![Azure 建议](./media/app-insights-app-map/07.png)

## <a name="diagnostic-click-through"></a>诊断单击
映射上的每个节点都提供诊断的定向单击。 选项因节点类型而异。

![服务器选项](./media/app-insights-app-map/09.png)

对于在 Azure 中托管的组件，选项包括指向它们的直接链接。

## <a name="filters-and-time-range"></a>筛选器和时间范围
默认情况下，映射概括了可用于所选时间范围的所有数据。 但你可以进行筛选，以便仅包含特定操作名称或依赖项。

* 操作名称：这包括页面视图和服务器端请求类型。 使用此选项，映射将仅显示所选操作的服务器端/客户端节点上的 KPI。 它显示在这些特定操作的上下文中调用的依赖项。
* 依赖项基名称：这包括 AJAX 浏览器依赖项和服务器端依赖项。 如果使用 TrackDependency API 报告自定义依赖项遥测，它们也会显示在此处。 你可以选择要显示在映射上的依赖项。 此选择当前不会筛选服务器端请求或客户端页面视图。

![设置筛选器](./media/app-insights-app-map/11.png)

## <a name="save-filters"></a>保存筛选器
若要保存已应用的筛选器，请将筛选的视图固定到[仪表板](app-insights-dashboards.md)。

![固定到仪表板](./media/app-insights-app-map/12.png)

## <a name="end-to-end-system-app-maps"></a>端到端系统应用映射

如果应用程序具有多个组件（例如，除 Web 应用外还有后端服务），则通过一个集成的应用映射将其全部显示。

![设置筛选器](./media/app-insights-app-map/multi-component-app-map.png)

应用映射通过在当前资源组中查找所有 Application Insights 资源寻找服务器节点。 它还通过在资源组中跟进由 Application Insights 资源跟踪的任何依赖项调用来检测服务器节点。


### <a name="setting-up"></a>设置

> [!NOTE] 
> 端到端系统应用映射处于预览状态。 需要通过特殊版本的 SDK 检测组件，并需使用特殊 URL 查看应用映射。 [了解如何设置端到端系统应用映射](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-app-map-preview.md)。


## <a name="feedback"></a>反馈
请[通过门户反馈选项提供反馈](app-insights-get-dev-support.md)。

![MapLink-1 图](./media/app-insights-app-map/13.png)




<!--HONumber=Nov16_HO4-->


