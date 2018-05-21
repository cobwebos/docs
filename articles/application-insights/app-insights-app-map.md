---
title: Azure Application Insights 中的应用程序映射 | Microsoft 文档
description: 使用应用程序映射监视复杂的应用程序拓扑
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin; Soubhagya.Dash
ms.openlocfilehash: a02d345c0f624f2d1694b4b9622bf3c82c009429
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
---
# <a name="application-map-triage-distributed-applications"></a>应用程序映射：会审分布式应用程序
应用程序映射可帮助你发现的性能瓶颈或热点失败的所有组件的分布式应用程序。 在地图上的每个节点表示应用程序组件或其依赖项;并且有运行状况 KPI 和警报状态。 可从任何组件单击以获得更详细的诊断，如 Application Insights 事件。 如果应用使用了 Azure 服务，还可以单击获得 Azure 诊断，如 SQL 数据库顾问建议。

## <a name="what-is-a-component"></a>组件是什么？

组件是分布式/微服务应用程序的可独立部署的部件。 开发者或操作团队具有代码级可见性，或有权访问由这些应用程序组件生成的遥测。 

* 组件与“观察到的”外部依赖项（如 SQL 和 EventHub 等）不同，团队/组织可能无权访问（代码或遥测）。
* 组件在任意数目的服务器/角色/容器实例上运行。
* 组件可以是单独的 Application Insights 检测密钥（即使订阅并不相同），或是向单个 Application Insights 检测密钥报告的不同角色。 预览地图体验显示了组件而不考虑如何设置它们。

## <a name="composite-application-map-preview"></a>复合应用程序映射 （预览版）
*这是早期预览版中，并且我们将将更多的功能添加到此映射。我们期待你的反馈获取新体验。可以轻松地切换之间的预览和经典经验。*

从启用"复合应用程序映射"[预览列表](app-insights-previews.md)，或者在右上角的切换按钮中单击"预览图"上。 此开关可用于切换回经典体验。
![启用预览映射](media/app-insights-app-map/preview-from-classic.png)

>[!Note]
此预览版将替换以前的“多角色应用程序映射”预览版。 在此期间，使用此跨多个级别的应用程序组件依赖项查看整个拓扑。 向我们提供反馈，我们将添加更多的功能类似于经典映射的支持。

你可以跨多个级别的相关应用程序组件中查看完整的应用程序拓扑。 组件可以是不同的 Application Insights 资源或不同的角色在单个资源。 应用映射通过跟踪已安装 Application Insights SDK 的服务器之间进行的任何 HTTP 依赖项调用来查找服务器节点。 

这种体验开头渐进式发现的组件。 当首次加载预览时，将触发一组查询发现与此组件相关的组件。 在左上角的按钮将使用更新你的应用程序中的组件数量发现它们。 
![预览映射](media/app-insights-app-map/preview.png)

单击"更新映射组件"，发现在该点之前的所有组件刷新映射。
![预览加载的映射](media/app-insights-app-map/components-loaded-hierarchical.png)

如果所有组件都是单个的 Application Insights 资源中的角色，则不需要此发现步骤。 这样的应用程序的初始负载将具有所有组件。

与新体验的主要目标之一是能够实现可视化效果具有数百个组件的复杂拓扑。 新体验支持缩放，并根据你放大添加详细信息。 可以缩小以查看详细一眼的组件和仍发现组件具有更高版本的失败率。 

![缩放级别](media/app-insights-app-map/zoom-levels.png)

单击以查看相关的见解，转到的性能和失败的该组件的会审体验的任何组件。

![浮出控件](media/app-insights-app-map/preview-flyout.png)


## <a name="classic-application-map"></a>经典应用程序映射

该映射显示：

* 可用性测试
* 客户端组件（使用 JavaScript SDK 监视）
* 服务器端组件
* 客户端和服务器组件的依赖项

![应用映射](./media/app-insights-app-map/02.png)

可展开和折叠依赖项链接组：

![折叠](./media/app-insights-app-map/03.png)

如果一种类型（SQL、HTTP 等）有许多依赖项，它们可能分组显示。 

![分组依赖项](./media/app-insights-app-map/03-2.png)

## <a name="spot-problems"></a>发现问题
每个节点都有相关的性能指标，例如该组件的负载、性能和故障率。 

警告图标突出显示可能的问题。 橙色警告表示请求、页面视图或依赖项调用中出现故障。 红色表示故障率高于 5%。 如果要调整这些阈值，请打开“选项”。

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
默认情况下，映射概括了可用于所选时间范围的所有数据。 但可以进行筛选，以便仅包含特定操作名称或依赖项。

* 操作名称：这包括页面视图和服务器端请求类型。 使用此选项，映射将仅显示所选操作的服务器端/客户端节点上的 KPI。 它显示在这些特定操作的上下文中调用的依赖项。
* 依赖项基名称：这包括 AJAX 浏览器依赖项和服务器端依赖项。 如果使用 TrackDependency API 报告自定义依赖项遥测，它们也会显示在此处。 可以选择要显示在映射上的依赖项。 此选择当前不会筛选服务器端请求或客户端页面视图。

![设置筛选器](./media/app-insights-app-map/11.png)

## <a name="save-filters"></a>保存筛选器
要保存已应用的筛选器，请将筛选的视图固定到[仪表板](app-insights-dashboards.md)。

![固定到仪表板](./media/app-insights-app-map/12.png)

## <a name="error-pane"></a>错误窗格
单击映射中的节点，会在右侧显示汇总该节点故障的错误窗格。 故障首先按操作 ID 分组，然后按问题 ID 分组。

![错误窗格](./media/app-insights-app-map/error-pane.png)

单击故障可转到该故障的最新实例。

## <a name="resource-health"></a>资源运行状况
对于某些资源类型，资源运行状况会显示在错误窗格的顶部。 例如，单击 SQL 节点会显示数据库运行状况和触发的任何警报。

![资源运行状况](./media/app-insights-app-map/resource-health.png)

可以单击资源名称，查看该资源的标准概述指标。

## <a name="video"></a>视频

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="feedback"></a>反馈
请通过门户反馈选项提供反馈。

![MapLink-1 图](./media/app-insights-app-map/13.png)


## <a name="next-steps"></a>后续步骤

* [Azure 门户](https://portal.azure.com)
