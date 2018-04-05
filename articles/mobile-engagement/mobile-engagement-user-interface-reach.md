---
title: Azure Mobile Engagement 用户界面 - 市场宣传
description: 了解如何使用 Azure Mobile Engagement 通过推送通知与应用程序的用户联系
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: d96e2590-08dd-4481-a352-2c18f26a1643
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: d999b83df7d9d467f08ce8ec72468c738e8acfa5
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-reach-out-to-the-users-of-your-application-with-push-notifications"></a>如何通过推送通知与应用程序的用户联系
> [!IMPORTANT]
> Azure Mobile Engagement 已在 2018 年 3 月 31 日停用。 此页将在不久之后删除。
> 

本文介绍了 **Mobile Engagement** 门户的“市场宣传”选项卡。 使用 Mobile Engagement 门户来监视和管理移动应用。 请注意，要开始使用该门户，首先需要创建 **Azure Mobile Engagement** 帐户。 有关详细信息，请参阅[创建 Azure Mobile Engagement 帐户](mobile-engagement-create.md)。

UI 的“市场宣传”部分是可以在其中创建/编辑/激活/完成/监视推送通知市场活动和功能，以及获取其相关统计信息的推送市场活动管理工具，这些推送通知市场活动和功能也可以通过 Reach API（和低级别 Push API 的某些元素）进行访问。 请记住，无论你使用 API 或 UI，对于使用 SDK 的每个平台，都需要将 Azure Mobile Engagement 和 Reach 集成到你的应用程序中，才可以使用市场宣传活动。

> [!NOTE]
> **Mobile Engagement** 门户 UI 的很多部分中都包含“显示帮助”按钮。 按此按钮可获取关于某一部分的更多上下文信息。
> 
> 

## <a name="four-types-of-push-notifications"></a>推送通知的四种类型
1. 公告 - 使你能够将广告消息发送给用户，这会将用户重定向到应用内的另一个位置，或者将他们带到应用外部的某个网页或应用商店。 
2. 轮询 - 使你能够通过提问从最终用户收集信息。
3. 数据推送 - 允许发送二进制或 base64 数据文件。 包含在数据推送中的信息会发送到应用程序，改变用户在应用中的当前体验。 应用程序需要能够处理数据推送中的数据。

## <a name="campaign-details"></a>市场活动详细信息
可以通过将鼠标悬停在那些尚未激活的市场活动名称上方，编辑、克隆、删除或激活它们，或者可以单击来打开它们。 可以通过将鼠标悬停在那些已经激活的市场活动名称上方，克隆它们，或者可以单击来打开它们。 但是，市场活动被激活之后，就不能再更改了。

![Reach1][18]

## <a name="reach-feedback"></a>市场宣传反馈
单击“统计信息”以查看市场宣传活动的详细信息。 “简单”视图以列柱状图的形式提供有关在激活市场活动之后所发生的事项的可视化表示。 “**高级”**视图提供了有关推送市场活动的更精细的详细信息。 如果正在发送某个测试市场活动（即发送给某个测试设备的推送），那么这些详细信息将不可用。 下面介绍应该如何解释这些详细信息：

1. **已推送** - 这会指定推送到设备的消息数量。 此数量将取决于创建推送市场活动时指定的目标受众。 如果未指定任何目标受众，则会将此推送发送到所有已注册的设备。 像所有其他推送服务一样，我们不直接将通知推送到设备，而是将它们推送到相应平台特定的推送通知服务 (PNS - APNS/GCM/WNS)，这些服务可以将通知传送给设备。 
2. **已送达** - 这指定由 PNS 成功传送给设备并已确认由 Mobile Engagement SDK 接收的消息数量。 
   
   *已送达数量小于已推送数量的原因：*
   
   1. 如果用户已从设备卸载应用，但在我们向 PNS 发送推送时，PNS 不知道应用已卸载，那么消息会被丢弃。
   2. 如果设备有应用，但设备本身长时间处于脱机状态，那么 PNS 也无法将消息传送给设备。 
   3. 如果消息确实已传送给设备，但应用中的 Mobile Engagement SDK 无法识别消息的内容，那么 Mobile Engagement SDK 将删除该消息。 如果在应用中通知的自定义出现异常，我们在 SDK 中捕获到这一异常并丢弃了消息，则可能会发生这种情况。 如果设备上的应用正在使用的 Mobile Engagement SDK 版本无法理解从平台发送的推送消息的较新版本，则可能会发生这种情况，但仅当应用是在从服务平台分派通知之后升级时才会这样。 “高级”选项卡会显示丢弃了多少条消息。 
   4. 在 iOS 设备上，如果设备的电量低，或者如果应用在处理远程通知时电量消耗很显著，有时就不会传送消息。 这是 iOS 设备的一项限制。   
3. **已显示** - 这会指定在通知中心以系统推送/应用外通知的形式，或以移动应用内的应用内通知形式成功显示给设备上的应用用户的消息数量。  在“高级”选项卡中，可以查看有多少通知是系统通知，以及有多少通知是应用内通知。 
   
   *已显示数量小于已送达数量（等待进行显示）的原因*
   
   1. 如果通知市场活动有一个结束日期，那么就有可能通知已送达，但到了打开通知并向应用用户显示时，通知已经过期，因此，将永远不会显示该通知。   
   2. 如果通知是应用内通知，那么仅当应用用户打开应用时才会显示该通知。 在应用用户未打开应用的情况下，SDK 将报告通知已送达，但直到打开应用之后才会显示通知。 
   3. 如果通知是应用内通知，并被配置为在某个特定活动/屏幕上显示，那么此时也将报告通知已送达，但直到用户在特定屏幕上打开应用时才会送达。 
4. **用户交互** - 这会指定与应用用户交互的消息数量，并将包含已操作或已退出的消息。 
   
   * *应用用户可以通过以下方式之一操作通知︰*
     
     1. 如果通知是系统通知/应用外通知，或者是作为仅通知发送的应用内通知，那么应用用户会单击此通知。
     2. 如果通知是包含文本或 Web 视图或投票的应用内通知，那么应用用户会单击通知中的“操作”按钮。
     3. 如果通知是包含 Web 视图的应用内通知，那么应用用户会单击 Web 视图中的 URL [仅 Android]
   * *应用用户可以采用以下方法之一退出通知︰*
     
     1. 直接单击通知上的关闭按钮。 
     2. 轻扫或删除通知。 
     3. 包含文本/Web 内容和投票的应用内通知通常分两步显示给应用用户。 用户首先会看到一个通知，当其单击它时，会看到随后的文本/Web/投票内容。 应用用户可以使用上述任一步骤退出通知，“高级”视图中的详细信息会捕获此操作。 
5. **已操作** - 这指定由应用用户明确操作过的消息数量。 这是最有意义的数字，因为这会显示有多少应用用户对你在通知中推送的消息感兴趣。 

> [!NOTE]
> 在 iOS 和 Windows 平台上，如果用户已打开应用，并且市场活动是一个“随时”市场活动，那么就有可能同时显示应用外通知和应用内通知。 这可能导致已显示数量高于已送达数量。 如果用户与操作进行了交互或操作了通知，那么甚至用户交互数量/已操作数量也可能会大于已送达数量。 
> 
> 

![Reach2][19]

## <a name="see-also"></a>另请参阅
* [概念][Link 6]
* [故障排除指南服务][Link 24]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md

