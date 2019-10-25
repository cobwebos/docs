---
title: 了解 Visual Studio App Center 和 Azure 服务的移动应用程序使用情况和用户行为
description: 了解 App Center 的服务，这些服务可帮助你通过了解用户如何使用你的移动应用程序来做出明智的业务决策。
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: d60e9437a2f33788eb183cfcad0f3a10d71fb79d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795791"
---
# <a name="analyze-and-understand-usage-of-mobile-applications"></a>分析和了解移动应用程序的使用情况
你了解你的用户如何使用你的应用程序？ 您的应用程序有多少活动用户，以及在一段时间内的使用情况如何变化？ 他们使用哪些功能，哪些功能最常用？ 这些用户基于何处？ 多少用户正在使用最新版本的应用程序？ 所有这些问题都非常重要，可帮助您将应用程序转变为成功的业务。 若要回答这些类型的使用情况分析问题，需要从应用收集使用情况数据。

向下查找数据的方式越多，就可以找到改善应用程序的方法并使用户感到满意。 使用数据查找可操作的见解并使用户满意，这一点很重要。

## <a name="importance-of-analytics"></a>分析的重要性
- **了解**你的用户、他们与你的应用程序的交互方式，并使他们重新调整应用程序，并为发展你的业务提供丰富的体验。
- **跟踪**使用情况指标，以便明智地决定如何营销应用程序并为客户提供更好的服务。
- **衡量**应用程序性能。
- **了解**应用程序的哪些部分会带来价值和性能。
- **数据驱动的深入**了解有关变动和保留的问题。

使用以下服务启用移动应用程序分析。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
利用[App Center Analytics](/appcenter/analytics/) ，你可以将重点放在重要内容上，并深入了解用户会话、热门设备、OS 版本和行为分析。 轻松创建自定义事件以跟踪具有大量应用程序分析的任何内容。

   **关键功能**
   - 免费**跟踪**使用模式、用户采用情况和其他合约指标。
   - 通过自定义事件**确定**趋势、用户行为和参与情况。
   - 在**单个仪表板**中的应用程序使用情况（每天、每周、每月）、会话、设备属性和用户人口统计信息的**现成指标**和**详细见解**。
   - **将所有分析数据持续导出到 Azure**以进行无限制保留。 支持导出到 Azure Blob 存储并 Azure 应用程序 Insights。
   - **与 Azure 应用程序 Insights 集成**，以获得更深入的见解，如保留、漏斗分析和队列
   - 在数分钟内开始实现**单行 SDK 集成**。
   - **平台支持**-IOS、Android、MacOS、TvOS、Xamarin、响应本机、Unity 和 Cordova。

   **参考**
   - [注册 App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
   - [App Center Analytics 入门](/appcenter/analytics/)

## <a name="azure-monitor"></a>Azure Monitor
Azure Monitor 包含[Application Insights](/azure/azure-monitor/app/app-insights-overview) ，它提供了用于收集和分析遥测以最大程度提高性能和监视移动应用程序的工具。 你可以通过使用 App Center 分析来利用 Application Insights，并设置导出到 Application Insights。 Application Insights 可以从应用程序查询、分段、筛选和分析自定义事件遥测，而不是 App Center 提供的分析工具。

**关键功能**
   - **查询**自定义事件遥测。
   - 利用功能强大的分段功能**筛选**事件遥测。
   - 在应用程序中**分析**转换、保留和导航模式。
     - 漏斗图，用于分析和监视转换率。
     - 用于分析应用程序在一段时间内保留多长时间的**保留期**。
     - 工作簿，用于将可视化效果和文本组合到可共享的报表中。
     - 队列，用于命名和保存特定用户或事件组，以便可以轻松地通过其他分析工具引用它们。

**参考**
- [Azure 门户](https://portal.azure.com/)
- [通过 App Center 和 Application Insights 分析移动应用程序](/azure/azure-monitor/learn/mobile-center-quickstart)
- [使用 Application Insights 的使用情况分析](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure PlayFab
[Azure PlayFab](https://playfab.com/)提供了一个完整的后端平台，其中包含游戏服务、实时分析和 LiveOps，你需要创建世界级云连接的游戏。 这些服务降低了为游戏开发人员启动的障碍，同时提供了大型和小型录音室经济高效的开发解决方案，这些解决方案可随游戏一起缩放，并帮助他们参与、留住和盈利播放器。 PlayFab 使开发人员能够使用智能云来构建和运行游戏，分析游戏数据并改进整体游戏体验。

**关键功能**
   - **监视**实时仪表板。
   - 通过热门指标**评估**游戏性能。
   - **报告**可通过自动生成的报告查看游戏的每日和每月性能的摘要，这些报告可在游戏管理器中查看并每天下载或交付到收件箱。
   - **A/B 测试**以运行试验并确定特定变量的最佳设置。
   - 利用播放机的**细分**，可以定义播放机的自动分组。
    
**参考**
- [PlayFab 门户](https://developer.playfab.com/en-US/sign-up)
- [分析](/gaming/playfab/#pivot=documentation&panel=analytics)
- [快速入门](/gaming/playfab/#pivot=documentation&panel=quickstarts)    
