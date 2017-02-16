---
title: "Azure Mobile Engagement 用户界面 - 段"
description: "了解如何使用 Azure Mobile Engagement 来创建和管理用户段以标识使用模式"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: dwrede
editor: 
ms.assetid: 6a4f2205-4a3c-406e-a04f-5e6f2a36653f
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: b4f1df6f46b7b8349fcea649ebcdf34fc363491d


---
# <a name="how-to-create-and-manage-segments-of-users-to-identify-usage-patterns"></a>如何创建和管理用户段以标识使用模式
本文介绍了 **Mobile Engagement** 门户的**段**选项卡。 使用 **Mobile Engagement** 门户来监视和管理移动应用。

在 UI 的“段”部分，可基于从应用程序获取的不同行为和分析对用户进行分段，并可通过“段 API”访问。 段首先在创建后的 24 小时进行计算，然后基于最新分析信息每隔 24 小时重新计算。 计算完一个段后，该段将立即在每天显示“每日记录”图表。

> [!NOTE]
> **Mobile Engagement** 门户 UI 的很多部分中都包含“**显示帮助**”按钮。 按此按钮可获取关于某一部分的更多上下文信息。
> 
> 

## <a name="create-segments"></a>创建段
从分析部分中，可基于某个特定时间段（过去至多 60 天）的最多 10 个条件创建一个段。 例如，可基于过去 10 天内，在应用中查看了某些页面或搜索了特定内容的用户创建一个段。 此信息在分析部分提供。 因此，你可使用它创建一个段，然后针对该用户子集设置推送通知，以使这些用户继续使用该应用程序。 

> [!NOTE]
> 计算出某个段后，不能对其进行编辑；仅能进行克隆（复制）或销毁（删除）。 可在同一应用程序中复制某个段（使用同一 AppID），也可将其复制到其他应用程序中（使用不同的 AppID）。 

 ![segments1][35] 

## <a name="examples-segments"></a>示例段
 ![segments2][36]

使用段可对你的应用程序的最终用户进行细分。
对用户细分是一项重要的市场营销策略。 通过 Azure Mobile Engagement，可获取历史数据并创建自定义段。 借助这一强大的工具，可让你了解客户在你的应用程序中的体验。 你可以很方便地对这些段进行分析，并将其用作推送目标。
一个常见的使用案例是：你想发送一条推送通知，鼓励最终用户在应用商店中对你的应用程序进行评级。 这种情况下，你无需向所有最终用户发送通知，而是可以创建一个段，计算出上个月仅每天都在使用你的应用程序并且有很好用户体验的那部分用户。 这样可通过发送更少、更具针对性的推送通知，让你获得更好的投资回报率。

 ![segments3][37]

### <a name="segments-you-can-create-based-on-the-major-azure-mobile-engagement-elements"></a>可基于主要 Azure Mobile Engagement 元素进行创建的段：
* 事件：针对应用程序的一个特定事件创建段的操作每周进行两次。 
* 会话：针对上周使用应用程序超过 5 次的用户创建段。
* 活动：针对上个月使用过一个页面或内容在 10 次左右的用户创建段。
* 作业：针对一天完成作业两次以上的用户创建段。
* 故障：针对上周发生故障超过 10 次的所有用户创建段。 （可向该段的用户推送道歉信息甚至是优惠券！）
* 错误：针对过去 3 天出现错误超过 100 次的所有用户创建段。
* 应用信息：针对过去 25 天中发生的自定义应用信息创建段。
  
  ![segments4][38]

若要以最佳方式使用段，必须使用“应用信息”标记的标签体系和代码部署计划在你的应用程序中完成 SDK 的自定义集成。
然后，转到界面的主页，选择所需应用程序并单击“段”部分。

1. 选择“段”部分。
2. 单击“新建段”按钮创建新段。

## <a name="real-life-example-create-a-simple-segment-based-on-session-information"></a>真实示例：基于“会话”信息创建一个简单的段。
针对上周使用你应用至少 50 次的最终用户创建一个段。 从中找出在应用中的每个会话中至少停留了 30 秒的最终用户。 该结果将显示在应用中有正面体验的所有最终用户。 然后，创建的这个段可被用于向这些最终用户推送通知，让他们在应用商店中为你的应用评级。

 ![segments5][39]

1. 给你的段指定一个名称，以便在“段”列表中快速找到它。
2. 单击“创建”按钮。
   
   ![segments6][40]

选择会话。

 ![segments7][41]

1. 选择“上周”时间段。
2. 单击“下一步”。
   
   ![segments8][42]
3. 选择列表中相关的运算符： =、 ≥、 ≤。
4. 输入所需“计数”。
5. 选择所需“次数”。 
6. 单击“下一步”。
   对此示例进行了如上设置，即在上一周内，匹配的用户至少发起了 50 次会话。
   
   ![segments9][43]

在“会话”分段中，可选择每个会话的长度作为条件。

1. 从列表中选择运算符。
2. 提供每个会话的长度。
3. 单击“下一步”。
   在此示例中，它表示对于在次数部分上进行分段的所有会话，仅选择在每个会话中停留的时间超过 30 秒的用户。
   
   ![segments10][44]

为你的条件输入名称，以在完整漏斗中对其检索，然后单击“完成”。

 ![segments11][45]

完成条件设置后，它会在段漏斗中显示。
因为段是基于分析数据的，所以段会每天计算一次。
在此示例中，最终用户总数中的 47.7% 与该条件匹配。 他们应是有良好体验的用户，如果向这些客户推送一条让他们在应用商店中对应用进行评级的通知，他们可能会提供更高的评级。

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




<!--HONumber=Dec16_HO2-->


