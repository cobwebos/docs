---
title: "Azure Mobile Engagement 用户界面 - 分析"
description: "了解如何使用 Azure Mobile Engagement 分析有关应用程序的历史记录数据"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 6b2533ac-b8ec-4e35-872c-d563895bdc0c
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.translationtype: Human Translation
ms.sourcegitcommit: 06e16033435ed0a37d5688055743875827d3aec2
ms.openlocfilehash: ad05676919d6c254d60fd010c3f589f663c4745d
ms.contentlocale: zh-cn
ms.lasthandoff: 03/01/2017

---
# <a name="how-to-analyze-historical-data-about-your-application"></a>如何分析有关应用程序的历史记录数据
本文介绍了 **Mobile Engagement** 门户的“**分析**”选项卡。 使用 **Mobile Engagement** 门户来监视和管理移动应用。 请注意，若要开始使用该门户，你首先需要创建 **Azure Mobile Engagement** 帐户。

UI 的“分析”部分提供关于你的应用程序的聚合信息，此信息基于每 24 小时更新一次的历史记录数据。 此信息显示在由直线图/条形图/饼图、网格和地图组成的不同仪表板上。 该数据还可以提供 .csv 文件下载。 此相同信息中的大部分会在 UI 的“监视”部分中实时提供，也可以从“分析 API”访问。

> [!NOTE]
> **Mobile Engagement** 门户 UI 的很多部分中都包含“**显示帮助**”按钮。 按此按钮可获取关于某一部分的更多上下文信息。

## <a name="standard-and-custom-analytics"></a>标准和自定义分析
Azure Mobile Engagement 提供了一组关于应用的基本、标准分析信息，只要将你的应用与 SDK 集成到一起，就可以用图形表示这些信息。 使用 Azure Mobile Engagement，还可以收集你需要的关于最终用户行为的其他自定义分析信息。 你可以通过创建自定义“标记（应用信息）”的一个标记计划来完成此操作，该标记计划是从“**设置**”创建的，以便 Azure Mobile Engagement 可以为你收集此附加数据。

## <a name="analytics"></a>分析
* 仪表板︰显示关于你的新用户和活动用户及其趋势的一般信息。
* 用户︰用户通过其设备标识符进行标识，每个设备有一个唯一的标识符（一个新用户实际上就是一台新设备）。 如果某个用户在一个给定时间间隔内执行了其第一个会话，则会将该用户视为该时间间隔上的新用户。 如果某个用户在最近 7 天内至少执行了一个会话，则会将该用户视为保留用户。 活动用户是在给定时间段内至少执行了一个会话的用户。 你可以按每月、每周、每日、或每小时时间段进行排序。 所有图表看起来很相似，但可允许按不同的功能（比如应用程序的版本）进行筛选，然后再按时间段进行排序。 通过集成 SDK 收集的标准信息包括以下各项︰活动用户、新用户、会话数、 每个会话的长度、关于国家/地区的技术信息、局部变量、位置、语言载体、设备、固件、 网络 (WIFI)、应用和 SDK 的版本、由客户使用。 可以从“监视”部分实时查看此信息。

> [!NOTE]
> 时间段基于用户设备设置中的日期，因此，未正确设置手机日期的用户可能会在错误的时间段中显示。

* 保留期：如果某个用户在一个给定时间间隔内执行了其第一个会话，则会将该用户视为该时间间隔上的保留用户。 你可以更改时间间隔，在此时间间隔内会计算保留用户（和新用户）的小时数、天数、周数或月数。 用户保留期分析是基于队列生成的。 一个队列就是在某个给定时间段内检测到的所有新用户的集合（即，在此时间段内执行其第一个会话的用户集）。 我们使用的队列类别有 1 天、2 天、4 天、7 天或 1 个月。 对于某个队列，每 1 天、2 天、4 天、7 天或 1 个月，Azure Mobile Engagement 都会计算属于该队列且仍处于活动状态的所有用户的集合（即，在该时间段内执行了至少一个会话的用户集）。 此用户集称为一个队列版本。 （Azure Mobile Engagement 可以向你显示有多少用户仍在使用你的应用，但仅平台特定的应用商店可以告诉你有多少用户卸载了你的应用 — 例如，GooglePlay、iTunes、Windows 应用商店等。）
* 会话：一个用户使用一次应用程序。 会话是从用户执行的活动序列生成的（活动通常与应用程序的一个屏幕的使用相关联，但这会根据 SDK 在应用程序中的集成方式而有所不同）。 用户一次仅可以执行一个活动：会话从用户开始其第一个活动开始，至用户完成其最后一个活动结束。 如果用户超过几秒未执行任何活动，则其活动序列将被拆分为两个不同的会话。
* 活动︰应用程序中每个屏幕的名称和用户在每个屏幕上花费的时间长度。 “活动”是一个自定义分析选项，该选项将与你为自己的应用设置的“应用信息”标记相对应：
* 用户路径︰显示用户如何浏览你的应用程序活动（屏幕）。 你可以移动滑块以调整详细信息的级别。 蓝色节点代表应用程序的活动。 它们的大小与用户在其中花费的时间成正比。 白色节点代表会话开始和停止。 红色节点代表崩溃。 链接代表在应用程序的活动之间的转换（或者在活动和崩溃之间的转换）。 单击节点或链接会显示一个工具提示，其中包含关于你的数据的详细信息︰在特定屏幕中花费的时间、转换的计数以及从源活动到目标活动的转换百分比。 （A ---60%---> B 表示处于活动 A 上的用户有 60%的时间转到活动 B。）如果需要澄清图形，可以重新组织它；每次你做更改，都会保存其位置。 您可以显示或隐藏崩溃来加亮图形。
* 事件︰在应用程序中由用户执行的特定操作。 事件分发会显示为每会话每用户的事件计数。 一个事件代表一个即时操作，例如，单击按钮或接收通知。 （事件的含义取决于在应用程序中集成 SDK 的方式。）事件可以在会话或作业期间发生，或者也可以是独立的。
* 作业︰除了侧重于操作的长度之外，与事件类似。 例如，作业可以告诉你有关加载内容要花费多长时间的技术信息，或者可以告诉你对 Web 服务的某个调用。 它还可以显示用户填写表格、创建帐户或进行购买所花费的时间。 作业代表某个任务的持续时间，例如，某个下载任务的持续时间或者某个横幅在屏幕上显示的时间。 （作业的含义取决于在应用程序中集成 SDK 的方式。）作业通常与在会话范围以外执行的后台任务相关联（即，没有任何用户活动）。
* 技术：关于你可以跟踪的应用用户的设备的技术信息，比如区域设置、运营商、网络设备、固件、用户设备的屏幕大小，以及你的应用版本和在应用中使用的 SDK 版本。
* 错误︰关于在应用程序内部、不会导致应用程序崩溃的技术错误的信息。 一个错误代表一个即时问题，例如，一个网络故障或一个错误操作。 （事件的含义取决于在应用程序中集成 SDK 的方式。）错误可以在会话或作业期间发生，或者也可以是独立的。
* 崩溃︰关于导致应用程序崩溃的错误的信息。 崩溃是一种意外情况，在这种情况下应用程序停止执行其预期的功能且必须被停止。 崩溃通常是由于应用程序中的 bug 引起的。

![Analytics2][11]

## <a name="accessing-the-retention-overview"></a>访问保留期概述
![Analytics3][12]

保留期概述可以从中间细分为几张卡片，其中每张卡片显示某个特定保留期的概述。 示例中显示的是 2 天保留期。 其他卡片会显示 4 天和 7 天保留期。

## <a name="understanding-the-retention-overview-cards"></a>了解保留期概述卡片
![Analytics4][13]

### <a name="each-card-is-composed-of-3-main-parts"></a>每张卡片由 3 个主要部分组成︰
1. 1: 要考虑的队列和期间
2. 2-4︰当前期间的保留期
3. 5︰历史记录的迷你图

### <a name="here-is-detailed-information-about-each-element"></a>以下是关于每个元素的详细信息︰
1. 队列和期间︰此标题提供队列的类型。 此处“2 天期间”意味着我们将查看用户在 2 天内的行为，在 2 天期间到达的用户，以及是否在下面的 2 天块内连接。 上面的示例考虑了 11 月 21 日和 22 日之间的用户的活动。
2. 这会为 11 月 19 日和 20 日到达的用户提供 11 月 21 日和 22 日上的保留率。 此处我们在 21 日和 22 日之间有 1 个活动用户，在 19 日和 20 日之间的新用户超过 3 个。
3. 此可视指示器提供的信息与上面的图形方式表示提供的信息相同。 （圆的第三个来自 33% 的数字。）颜色会提供附加信息︰绿色表示此数字是从先前的计算增长而来的。 黄色表示稳定，而红色表示减少。
4. 这表示用于计算的值。
5. 这是保留值的历史记录的迷你图。 使用该图可以查看过去的值，从而对它过去的发展一览无遗。

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

