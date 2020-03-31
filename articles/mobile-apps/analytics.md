---
title: 使用 Visual Studio 应用中心和 Azure 服务了解移动应用程序使用情况和用户行为
description: 了解应用中心等服务，这些服务通过了解用户如何使用移动应用程序，帮助您做出明智的业务决策。
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 4dc3cea4497c55dda0d8da2ca29201615dadff19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241079"
---
# <a name="analyze-and-understand-mobile-application-use"></a>分析和理解移动应用程序的使用
您了解用户如何使用您的应用程序？ 您的应用程序有多少活跃用户，使用情况如何随时间而变化？ 他们使用哪些功能，哪些功能使用最多？ 这些用户位于何处？ 有多少用户正在使用最新版本的应用程序？ 所有这些问题对于了解将应用转变为成功的业务非常重要。 要回答这些类型的使用情况分析问题，您需要从应用收集使用情况数据。

您看中数据越多，就越可能找到改进应用程序和让用户满意的方法。 请务必使用数据来查找可操作的见解并让用户满意。

## <a name="importance-of-analytics"></a>分析的重要性
- 了解您的用户、他们如何与您的应用程序交互，以及是什么让他们重新微调您的应用程序，并提供出色的体验来发展您的业务。
- 跟踪使用情况指标，就如何销售应用程序并更好地为客户服务做出明智的决策。
- 测量应用程序性能。
- 了解应用程序驱动器价值和性能的哪些部分。
- 获得数据驱动的洞察，了解与流失和保留有关的问题。

使用以下服务启用移动应用程序分析。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[应用中心分析](/appcenter/analytics/)可让您专注于重要内容，从而扩大受众。 它提供了有关用户会话、顶级设备、操作系统版本和行为分析的深入报告和见解。 通过广泛的应用程序分析，轻松创建自定义事件来跟踪任何内容。

   **主要功能**
   - 免费跟踪使用模式、用户采用和其他参与度指标。
   - 通过自定义事件确定趋势、用户行为和参与。
   - 在单个仪表板中获取开箱即用的指标和有关应用程序使用情况（每日、每周、每月）、会话、设备属性和用户人口统计的详细见解。
   - 持续将所有应用中心分析数据导出到 Azure 中，以无限制保留。 应用中心分析支持导出到 Azure Blob 存储和 Azure 应用程序见解。
   - 与 Azure 应用程序见解集成，获得更深入的见解，如保留、漏斗分析和队列。
   - 使用单行 SDK 集成在几分钟内即可开始。
   - 获得对 iOS、Android、macOS、tvOS、Xamarin、反应原生、团结和科尔多瓦的平台支持。

   **引用**
   - [使用 App Center 注册](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
   - [开始应用中心分析](/appcenter/analytics/)

## <a name="azure-monitor"></a>Azure Monitor
Azure 监视器包括[应用程序见解](/azure/azure-monitor/app/app-insights-overview)，它提供了收集和分析遥测工具，以最大限度地提高性能和监视移动应用程序。 您可以使用应用中心分析设置导出到应用程序见解，从而利用应用程序见解。 应用程序见解可以查询、细分、筛选和分析应用程序中的自定义事件遥测，超出 App Center 提供的分析工具。

**主要功能**
   - 查询自定义事件遥测。
   - 使用强大的分段功能筛选事件遥测。
   - 分析应用程序中的转换、保留和导航模式。 可用工具如下：
     - 用于分析和监控转化率的漏斗。
     - 保留以分析应用程序在一段时间内保留用户的能力。
     - 将可视化和文本合并到可共享报表中的工作簿。
     - 用于命名和保存特定用户或事件组，以便可以轻松地从其他分析工具引用它们。

**引用**
- [Azure 门户](https://portal.azure.com/)
- [使用应用中心和应用程序见解分析您的移动应用程序](/azure/azure-monitor/learn/mobile-center-quickstart)
- [使用应用中心分析与应用程序见解](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure PlayFab
[Azure PlayFab](https://playfab.com/)提供了完整的后端平台，提供游戏服务、实时分析和 LiveOps，您需要创建世界一流的云连接游戏。 这些服务减少了游戏开发人员的启动障碍。 他们提供大型和小型工作室经济高效的开发解决方案，可随游戏扩展规模。 这些服务可以帮助工作室吸引、保留和获利。 借助 PlayFab，开发人员可以使用智能云构建和操作游戏、分析游戏数据并改善整体游戏体验。

**主要功能**
   - 监控实时仪表板。
   - 通过顶级指标评估游戏的性能。
   - 通过自动生成的报告查看游戏每日和每月性能摘要。 您可以在游戏管理器中查看报告，并每天下载或将其发送到您的收件箱。
   - 使用 A/B 测试运行实验并确定特定变量的最佳设置。
   - 使用玩家细分定义玩家的自动分组。
    
**引用**
- [PlayFab 门户](https://developer.playfab.com/en-US/sign-up)
- [分析](/gaming/playfab/#pivot=documentation&panel=analytics)
- [快速入门](/gaming/playfab/#pivot=documentation&panel=quickstarts) 
