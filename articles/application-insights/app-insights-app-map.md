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
ms.topic: conceptual
ms.date: 06/14/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 9b39eef5accec4764f61ab31dd894d368242ee3d
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2018
ms.locfileid: "47094644"
---
# <a name="application-map-triage-distributed-applications"></a>应用程序映射：会审分布式应用程序

应用程序映射可帮助你发现的性能瓶颈或热点失败的所有组件的分布式应用程序。 在地图上的每个节点表示应用程序组件或其依赖项;并且有运行状况 KPI 和警报状态。 可从任何组件单击以获得更详细的诊断，如 Application Insights 事件。 如果应用使用了 Azure 服务，还可以单击获得 Azure 诊断，如 SQL 数据库顾问建议。

## <a name="what-is-a-component"></a>组件是什么？

组件是分布式/微服务应用程序的可独立部署的部件。 开发者或操作团队具有代码级可见性，或有权访问由这些应用程序组件生成的遥测。 

* 组件与“观察到的”外部依赖项（如 SQL 和 EventHub 等）不同，团队/组织可能无权访问（代码或遥测）。
* 组件在任意数目的服务器/角色/容器实例上运行。
* 组件可以是单独的 Application Insights 检测密钥（即使订阅并不相同），或是向单个 Application Insights 检测密钥报告的不同角色。 预览地图体验显示了组件而不考虑如何设置它们。

## <a name="composite-application-map"></a>复合应用程序映射

你可以跨多个级别的相关应用程序组件中查看完整的应用程序拓扑。 组件可以是不同的 Application Insights 资源或不同的角色在单个资源。 应用映射通过跟踪已安装 Application Insights SDK 的服务器之间进行的任何 HTTP 依赖项调用来查找服务器节点。 

这种体验开头渐进式发现的组件。 当首次加载应用程序映射时，将触发一组查询来发现与此组件相关的组件。 在左上角的按钮将使用更新你的应用程序中的组件数量发现它们。 

单击"更新映射组件"，发现在该点之前的所有组件刷新映射。

如果所有组件都是单个的 Application Insights 资源中的角色，则不需要此发现步骤。 这样的应用程序的初始负载将具有所有组件。

![应用程序映射屏幕截图](media/app-insights-app-map/001.png)

使用此体验的主要目标之一是能够实现可视化效果具有数百个组件的复杂拓扑。

单击以查看相关的见解，转到的性能和失败的该组件的会审体验的任何组件。

![浮出控件](media/app-insights-app-map/application-map-001.png)

### <a name="investigate-failures"></a>调查故障

选择“调查故障”以启动故障窗格。

![“调查故障”按钮的屏幕截图](media/app-insights-app-map/investigate-failures.png)

![故障体验的屏幕截图](media/app-insights-app-map/failures.png)

### <a name="investigate-performance"></a>调查性能

若要排查性能问题，请选择“调查性能”

![“调查性能”按钮的屏幕截图](media/app-insights-app-map/investigate-performance.png)

![性能体验的屏幕截图](media/app-insights-app-map/performance.png)

### <a name="go-to-details"></a>转到详细信息

选择“转到详细信息”可探索端到端事务体验，从而可以查看深入到调用堆栈级别的信息。

![“转到详细信息”按钮的屏幕截图](media/app-insights-app-map/go-to-details.png)

![端到端事务详细信息的屏幕截图](media/app-insights-app-map/end-to-end-transaction.png)

### <a name="view-in-analytics"></a>在 Analytics 中查看

若要进一步查询和调查应用程序数据，请单击“在 Analytics 中查看”。

![“在 Analytics 中查看”按钮的屏幕截图](media/app-insights-app-map/view-in-analytics.png)

![分析体验的屏幕截图](media/app-insights-app-map/analytics.png)

### <a name="alerts"></a>警报

若要查看活动警报和导致警报触发的基础规则，请选择“警报”。

![“警报”按钮的屏幕截图](media/app-insights-app-map/alerts.png)

![分析体验的屏幕截图](media/app-insights-app-map/alerts-view.png)

## <a name="video"></a>视频

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="feedback"></a>反馈
请通过门户反馈选项提供反馈。

![MapLink-1 图](./media/app-insights-app-map/13.png)

## <a name="next-steps"></a>后续步骤

* [Azure 门户](https://portal.azure.com)
