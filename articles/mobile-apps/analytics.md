---
title: 了解 Visual Studio App Center 和 Azure 服务的移动应用程序使用情况和用户行为
description: 了解 App Center 的服务，这些服务可帮助你通过了解用户如何使用你的移动应用程序来做出明智的业务决策。
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: ff25df55e8489819ca9400205cdea1b8ceb8bf80
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454533"
---
# <a name="analyze-and-understand-mobile-application-use"></a>分析和了解移动应用程序使用情况
你了解你的用户如何使用你的应用程序？ 您的应用程序有多少活动用户，以及在一段时间内的使用情况如何变化？ 他们使用哪些功能，哪些功能最常用？ 这些用户基于何处？ 多少用户正在使用最新版本的应用程序？ 所有这些问题都非常重要，可帮助您将应用程序转变为成功的业务。 若要回答这些类型的使用情况分析问题，需要从应用收集使用情况数据。

向下查找数据越多，您可能会发现改进您的应用程序的方法并使用户感到满意。 使用数据查找可操作的见解并使用户保持满意，这一点很重要。

## <a name="importance-of-analytics"></a>分析的重要性
- 了解你的用户、他们与你的应用程序的交互方式，并使他们重新调整应用程序，并为发展你的业务提供丰富的体验。
- 跟踪使用情况指标，以便明智地决定如何营销应用程序并为客户提供更好的服务。
- 衡量应用程序性能。
- 了解应用程序驱动器值和性能的部分。
- 获取涉及变动和保留的问题的数据驱动见解。

使用以下服务启用移动应用程序分析。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
利用[App Center Analytics](/appcenter/analytics/) ，你可以通过将重点放在重要内容上来扩大你的受众。 它提供有关用户会话、热门设备、OS 版本和行为分析的深入报告和见解。 轻松创建自定义事件以跟踪具有大量应用程序分析的任何内容。

   **主要功能**
   - 免费跟踪使用模式、用户采用情况和其他合约指标。
   - 通过自定义事件确定趋势、用户行为和参与情况。
   - 在单个仪表板中获取有关应用程序使用情况（每天、每周、每月）、会话、设备属性和用户人口统计信息的现成指标和详细见解。
   - 将所有 App Center 分析数据连续导出到 Azure 中，以进行无限制保留。 App Center Analytics 支持导出到 Azure Blob 存储和 Azure 应用程序 Insights。
   - 与 Azure 应用程序 Insights 集成，以获得更深入的见解，如保留、漏斗分析和队列。
   - 在几分钟内即可开始使用单行 SDK 集成。
   - 获得适用于 iOS、Android、macOS、tvOS、Xamarin、响应本机、Unity 和 Cordova 的平台支持。

   **参考**
   - [注册 App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
   - [App Center Analytics 入门](/appcenter/analytics/)

## <a name="azure-monitor"></a>Azure 监视器
Azure Monitor 包括[Application Insights](/azure/azure-monitor/app/app-insights-overview)，它提供了收集和分析遥测以最大程度提高性能和监视移动应用程序的工具。 可以通过使用 App Center Analytics 设置到 Application Insights 的导出来利用 Application Insights。 Application Insights 可以从应用程序查询、分段、筛选和分析自定义事件遥测，而不是 App Center 提供的分析工具。

**主要功能**
   - 查询自定义事件遥测。
   - 利用功能强大的分段功能筛选事件遥测。
   - 在应用程序中分析转换、保留和导航模式。 可用工具如下：
     - 用于分析和监视转换速率的漏斗图。
     - 保持期，分析应用程序在一段时间内保留用户的程度。
     - 用于将可视化效果和文本合并到可共享报表中的工作簿。
     - 队列来命名并保存特定的用户或事件组，以便可以从其他分析工具轻松引用它们。

**参考**
- [Azure 门户](https://portal.azure.com/)
- [通过 App Center 和 Application Insights 分析移动应用程序](/azure/azure-monitor/learn/mobile-center-quickstart)
- [结合使用 App Center 分析 Application Insights](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure PlayFab
[Azure PlayFab](https://playfab.com/)提供了一个完整的后端平台，其中包含游戏服务、实时分析和 LiveOps，你需要创建世界级连接到云的游戏。 这些服务可减少为游戏开发人员启动的障碍。 它们提供了大型和小型工作室经济高效的开发解决方案，这些解决方案随游戏一起扩展。 这些服务可以帮助录音室参与、保留和盈利播放器。 借助 PlayFab，开发人员可以使用智能云来构建和运行游戏，分析游戏数据，并改进整体游戏体验。

**主要功能**
   - 监视实时仪表板。
   - 通过热门指标评估游戏性能。
   - 通过自动生成的报表查看游戏的每日和每月性能的摘要。 您可以在游戏管理器中查看报表，并将其下载或交付到收件箱。
   - 使用 A/B 测试来运行试验，并确定特定变量的最佳设置。
   - 使用播放机的分段定义播放机的自动分组。
    
**参考**
- [PlayFab 门户](https://developer.playfab.com/en-US/sign-up)
- [分析](/gaming/playfab/#pivot=documentation&panel=analytics)
- [快速入门](/gaming/playfab/#pivot=documentation&panel=quickstarts) 
