---
title: "比较 Azure Mobile Engagement 和其他相似的 Azure 服务"
description: "比较 Azure Mobile Engagement 和其他相似的 Azure 服务 - HockeyApp、AppInsights 和通知中心"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 1f114775-3a9a-4dd4-8d59-b10d1da9a68b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 7df2eb9ecebe3313dad9c15171552a084787f6b8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="comparing-azure-mobile-engagement-with-other-similar-azure-services"></a>比较 Azure Mobile Engagement 和其他相似的 Azure 服务
Microsoft Azure 提供的服务列表在不断扩展，有时你可能会疑惑 Azure Mobile Engagement 与你刚刚看到或听说过的其他服务有何不同。 本文试图消除这种疑惑，并指导用户在最佳使用时机选择 Azure Mobile Engagement。 

Azure Mobile Engagement 是专门面向数字营销人员/CMO 的服务，但是也可用于任何想要提高其移动应用使用率、延长保留期、增加盈利的移动应用所有者或发布者。 

*它是一个软件即服务 (SaaS) 用户参与平台，可针对应用使用、实时用户细分提供数据驱动型的深入见解，并支持可感知情景的推送通知和应用内消息传送。* 

深入分析此定义，以下关键特征也突出其独特的价值主张：

1. **可感知情景的推送通知和应用内消息传送**
   
   这是此产品的核心焦点 - 执行有针对性的个性化推送通知。 要实现此目的，则要收集丰富的行为分析数据。 
2. **针对应用使用情况提供数据驱动型的深入见解**
   
   我们提供了跨平台 SDK 来收集关于应用用户的行为分析。 请注意“行为分析”这个术语（与“性能分析”相对），因为我们关注应用用户如何使用应用。 我们也收集关于错误、崩溃等基本的性能分析数据，但这不是此产品的核心焦点。 
3. **实时用户细分**
   
   收集应用用户的行为分析数据后，可基于各种参数和收集的数据来细分受众，以运行有针对性的推送活动。 
4. **软件即服务 (SaaS)：**
   
   我们拥有一个独立于 Azure 管理门户的门户，对其进行了优化以交互和查看有关应用用户的大量行为分析并运行营销推送市场活动。 产品旨在帮助你在没有时间 ！   

产品具备了这一系列的特色，下面则与其他看似相似的 Azure 服务进行比较（注意，本文不进行功能级别的比较，而采用更加侧重基于方案的方法来定义哪种产品效果最好）：

1. [HockeyApp](https://azure.microsoft.com/services/hockeyapp/) 是 Microsoft 的移动 DevOps 解决方案。 借助它，可以将内部版本分发给 Beta 版测试人员、收集崩溃数据并获取用户反馈。 它还可以与 Visual Studio Team Services 集成，轻松实现内部版本部署和工作项目集成。 
   
   此处的重点在于 DevOps 以及收集与移动应用有关的性能分析数据。 最终可以在应用中同时集成 HockeyApp 和 Mobile Engagement，这种结果并不罕见，因为即使收集的数据中存在重叠，但此产品的核心焦点不同，它们有助于实现不同的目的。  
2. [Application Insights](../application-insights/app-insights-overview.md) 如果移动应用具有服务器端，则会使用 Application Insights 监视应用的 Web 服务器端，但是对于客户端移动应用，应使用 HockeyApp。 
3. [通知中心](https://azure.microsoft.com/services/notification-hubs/)提供轻型服务，即无需在移动应用中集成 SDK 就可完全掌控移动应用，并支持借助基本标记功能发送推送通知。 这非常适用于不太关注针对性而更侧重于将信息迅速发送/传递至他们的应用用户（向大量受众进行广播）的任何应用所有者。 
   
   请注意，此处的重点在于使用基本细分功能快速地发送通知。 

让我们来看一些方案：

1. Tim 是 Adventure Works 应用商店的数字营销团队成员，该应用商店为用户发布移动应用。 Tim 的目标是保证下载他们的移动应用的用户继续并频繁使用他们的应用。 这不仅可以增加与应用用户的品牌关联，应用用户使用移动应用购物后还可增加盈利。 为实现此目的，Tim 需要向应用用户发送用户会认为对自己有帮助的针对性通知，来鼓励用户打开应用并频繁返回使用该应用。 在这种情况下，Tim 会发现 Mobile Engagement 非常有用。 
2. Joann 是 Adventure Works 的移动应用开发团队成员，正在寻找一个产品来记录有关崩溃、异常的详细信息，并从应用中获得性能遥测。 在这种情况下，Joann 会发现 HockeyApp 非常有用。 Joann 可以在同一个应用中既集成 HockeyApp 来用于以开发人员中心的方案又集成 Azure Mobile Engagement 来供 Tim 使用，以获得两者的优势。 
3. Robin 是 Contoso News 网络的移动应用开发团队成员，她要实现的目标是在触发新闻提醒后不进行很多细分即向所有用户发送重大新闻提醒。 在这种情况下，Robin 会发现通知中心非常有用。 
   但在此方案中，有可能随着移动应用的成熟，需要进行详细得多的细分并获取应用用户行为的详细信息。 此时，Robin 必须对 Azure Mobile Engagement 进行评估。 

概括来说，Mobile Engagement 的用途不仅在于收集分析 - 它不是“Microsoft 中的另一个分析产品”。 它旨在发送针对性推送通知，为了实现这种针对性，我们会收集行为分析数据，但是重点仍是发送对应用用户具有最大意义的推送通知，以便其不会被视为垃圾邮件。 

有关更多详细信息，请参阅关于 Mobile Engagement 简述的[快速视频](mobile-engagement-overview.md)。 

