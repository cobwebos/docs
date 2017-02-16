---
title: "Azure Mobile Engagement 用户界面 - 主页"
description: "了解如何使用 Azure Mobile Engagement 管理现有的应用程序和项目"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: dwrede
editor: 
ms.assetid: aff578d2-40f6-43e4-b0ea-7d2674cb28a1
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 1e3e4825d06486e6e8dfa874271ec82d203f7a7b


---
# <a name="how-to-manage-your-existing-application-and-projects"></a>如何管理现有的应用程序和项目
本文介绍了 **Mobile Engagement** 门户的“**主页**”。 使用 **Mobile Engagement** 门户来监视和管理移动应用。 请注意，若要开始使用该门户，你首先需要创建 **Azure Mobile Engagement** 帐户。 

要访问主页，请单击页面左上角的“**主页**”。 它包含作为所选集合的一部分的所有应用程序的列表。 在此页面上，你可以看到应用程序的快速概述。

主页还包含你的所有项目，这些项目可以包括帐户中的任意应用程序。 请注意，任何人都可以通过创建帐户访问 UI 的主页，但你需要向其他用户授予权限，以便他们能够访问“**我的项目**”中的自定义应用程序。

你还可以查看所选应用程序的比较图。 或者，选择查看项目中选定应用程序的比较图。

![主页 1][0]

## <a name="my-applications"></a>我的应用程序
应用程序的快速概述允许你选择要打开的应用程序以查看详细的功能区选项。 单击应用程序的名称即可返回应用程序中最近访问的功能区位置，或单击齿轮图标以直接转到应用程序的“设置”页面。 你可以对应用程序表上显示的信息进行搜索、筛选或排序。 你还可以拖放列标题以更改顺序。

除此之外，应用程序概述还包括：

* **新用户趋势**：过去两周内新用户的发展趋势。
* **活跃用户**：过去 30 天内的活跃用户数。
* **活跃用户趋势**：过去两周内活跃用户的发展趋势。
* **会话**：会话是指对一个用户执行的应用程序进行一次使用，从用户开始使用到用户停止。
* **会话趋势**：过去两周内会话的发展趋势。

单击应用程序后，你就可以通过 UI 开始监视和管理应用程序。 例如：    

* [监视有关应用程序的实时数据](mobile-engagement-user-interface-monitor.md)
* [分析有关应用程序的历史记录数据](mobile-engagement-user-interface-analytics.md)
* [创建和管理用户段以确定使用模式](mobile-engagement-user-interface-segments.md)
* [通过推送通知与应用程序的用户联系](mobile-engagement-user-interface-reach.md)

## <a name="my-projects"></a>我的项目
你可以使用项目对应用程序进行分组，并向其他用户授予访问应用程序的权限。 你通过提供电子邮件地址向其他用户授予权限。 “**新建项目**”按钮允许你通过只输入新项目的“名称”和“描述”来创建新项目。 创建项目后，可以单击项目名称以编辑产品的名称和描述，并选择要在此项目中查看的所有应用程序。

![主页 6][60]

角色包括：

* **查看者**：查看者是只能查看与项目关联的应用程序的用户。 查看者可以访问分析和监视数据，并查看市场宣传结果。 查看者不能更改任何信息，也不能管理应用程序或用户。 查看者无法创建或更改市场宣传活动状态。
* **开发者**：开发者是一个用户，能够执行查看者可以执行的所有操作，并且能管理应用程序。 开发者可以启用和禁用应用程序、更改应用程序的信息（如包和签名）以及创建市场宣传活动。 开发者不能管理用户。
* **管理员**：管理员是一个用户，能够执行开发者可以执行的所有操作，并且能管理用户。 管理员可以邀请用户加入项目、更改用户角色并且能更改项目的信息。 也可以在“设置”中设置应用程序级别权限。

单击项目以查看属于此项目的所有应用程序。 下图显示了所选应用程序的比较图。

![主页 2][3]

## <a name="see-also"></a>另请参阅
* [概念][Link 6]
* [故障排除指南服务][Link 24]

<!--Image references-->
[0]: ./media/mobile-engagement-user-interface-home/home0.png
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[60]: ./media/mobile-engagement-user-interface-home/home6.png
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


