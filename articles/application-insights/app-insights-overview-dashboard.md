---
title: Azure Application Insights 概览仪表板 | Microsoft 文档
description: 使用 Azure Application Insights 概览仪表板功能来监控应用程序。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: mbullwin
ms.openlocfilehash: 4a639b51fd930c5c74ee78b37fbe8e97e39dbc32
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "36336765"
---
# <a name="application-insights-overview-dashboard"></a>Application Insights 概述仪表板

Application Insights 一直都有一个总览窗格，可让用户快速、直接地评估应用程序的运行状况和性能。 新的概述仪表板提供了更快更灵活的体验。

## <a name="how-do-i-test-out-the-new-experience"></a>如何测试新体验？

现在默认情况下将启动新的概述仪表板：

![概览预览窗格](.\media\app-insights-overview-dashboard\overview-0001.png)

## <a name="better-performance"></a>性能更好

时间范围选择已简化为简单的一键式界面。

![时间范围](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-03.png)

总体性能已大大提高。 只需单击一次即可访问常用功能，例如**搜索**和**分析**。 每个默认动态更新的 KPI 磁贴都可让你深入了解相应的 Application Insights 功能。 若要了解有关失败请求的详细信息，请在“调查”标题下选择“失败”：

![失败数](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>应用程序仪表板

应用程序仪表板利用 Azure 内现有仪表板技术，为你的应用程序运行状况和性能提供了一个完全可自定义的单一窗格视图。

若要访问默认仪表板，请选择左上角的“应用程序仪表板”。

![仪表板视图](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-05.png)

如果这是你第一次访问仪表板，它将启动默认视图：

![仪表板视图](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-06.png)

如果你喜欢，可以保留默认视图。 或者，可以在仪表板中执行添加或删除操作，让仪表板契合团队的需求。

> [!NOTE]
> 所有有权访问 Application Insights 资源的用户共享相同的应用程序仪表板体验。 一个用户所做的更改将修改所有用户的视图。

若要导航回概览体验，只需选择：

![“概览”按钮](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-07.png)

## <a name="access-the-classic-overview-dashboard"></a>访问经典概述仪表板

如果缺少旧概述，请选择“经典概述”。

![经典概述](.\media\app-insights-overview-dashboard\overview-classic.png)

## <a name="next-steps"></a>后续步骤

- [漏斗图](usage-funnels.md)
- [保留](app-insights-usage-retention.md)
- [用户流](app-insights-usage-flows.md)
- [仪表板](app-insights-dashboards.md)
