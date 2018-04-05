---
title: Azure Mobile Engagement 用户界面 - 监视器
description: 了解如何使用 Azure Mobile Engagement 监视有关应用程序的实时数据
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: b91ad89a-b89d-4377-abb0-cc2d16a2836d
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: b370a5db1acee37ce234f5ec3f004be20719cfea
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-monitor-real-time-data-about-your-application"></a>如何监视有关应用程序的实时数据
> [!IMPORTANT]
> Azure Mobile Engagement 已在 2018 年 3 月 31 日停用。 此页将在不久之后删除。
> 

本文介绍了 **Mobile Engagement** 门户的“**监视器**”选项卡。 使用 Mobile Engagement 门户来监视和管理移动应用。 请注意，要开始使用该门户，首先需要创建 **Azure Mobile Engagement** 帐户。 

UI 的“监视器”部分提供了实时分析信息，而且允许设置达到阈值时的警报，获取 UI 的“[分析](mobile-engagement-user-interface-analytics.md)”部分中过去提供的大部分相同信息。 请参阅[概念](http://go.microsoft.com/fwlink/?LinkId=525555)主题中的**词汇表**部分，了解分析和监视中术语和缩写的定义（如下所示：活动用户、新用户、保留用户、会话、用户路径图形、用户映射、跟踪 URL、趋势、活动、事件、作业、错误、额外的信息、崩溃和应用信息）。

> [!NOTE]
> **Mobile Engagement** 门户 UI 的很多部分中都包含“显示帮助”按钮。 按此按钮可获取关于某一部分的更多上下文信息。
> 
> 

## <a name="monitor---sessions-jobs-events-errors-and-crashes"></a>监视器 - 会话、作业、事件、错误和崩溃
可以看到会话中和特定屏幕上当前的用户数量或正在执行特定操作的用户数量。 可以查看按会话、作业、事件、错误和崩溃划分的用户活动。 可以看到最新信息，并显示过去一小时、一天或一周内的信息。 可以看到每个类别中或按特定会话、作业、事件、错误和崩溃排序的所有信息。  实时监视有助于利用事件（例如推送营销活动）发生过程来查看是否在发送推送通知后操作立即见效。

![监视器 1][14]  

## <a name="troubleshooting-with-monitor---events---details"></a>监视器 - 事件 - 详细信息疑难解答
从测试设备的应用程序中生成事件并在“监视器 - 事件 - 详细信息”中找到它是查找测试设备的设备 ID 以及确认分析、监视和段的 Azure Mobile Engagement 集成正在应用程序中工作的最简单方法之一。 获得测试设备的设备 ID 后，可以在“我的帐户 - 设备”中将其添加到测试设备。 如果不能生成事件，请确保 Azure Mobile Engagement 在 Android/iOS/Web/Windows/Windows Phone 应用中与 SDK 正确地集成。

有关详细信息，请参阅：[SDK 文档][Link 5]

![监视器 2][15]  

## <a name="troubleshooting-with-monitor---crashes---details"></a>监视器 - 崩溃 - 详细信息疑难解答
可以从“监视器 - 崩溃 - 详细信息”中查看有关应用的崩溃信息，以帮助确定应用崩溃的原因。 还应在每个版本的 SDK for Android/iOS/Web/Windows/Windows Phone 的发行说明中查找每个版本的 SDK 的已知问题。

有关详细信息，请参阅：[SDK 文档 - 发行说明][Link 5]

![监视器 3][16]

## <a name="monitor---alerts"></a>监视器 - 警报
还可以指定将通过电子邮件或即时消息自动发送给警报的条件。 （支持任何符合 XMPP 的服务，如 Google 的 GTalk 或 Apple 的 iChat。）警报基于预定义的检测阈值大于 (>) 或小于 (<) 每秒、每分钟或每小时会话、作业、事件、错误或崩溃的特定数目。 警报可以监视某个给定类型的所有活动，或仅监视某个特定的作业、事件或错误活动。 

还可以指定最低检测率，即针对同一个警报分离两个通知以确定何时出发警报的最少分钟数，在每个指定的时间间隔内你永远不会收到多个通知。

![监视器 4][17]

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
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md
