---
title: "Azure Mobile Engagement 用户界面 - 市场宣传方法"
description: "Azure Mobile Engagement 用户界面概述"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 30af87e6-c816-4cce-8609-6cbd3e83de14
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.translationtype: Human Translation
ms.sourcegitcommit: 3a423e42441c6b3b2f1917ba02f80078c759ec69
ms.openlocfilehash: 4c114f470631ae6b06968c3cc8601bc1c76bc1da
ms.contentlocale: zh-cn
ms.lasthandoff: 02/02/2017


---
# <a name="how-to-get-started-using-and-managing-pushes-to-reach-out-to-your-end-users"></a>如何开始使用和管理推送来向最终用户进行市场宣传
将 SDK 完全集成到应用中后，则可以开始使用 UI 的市场宣传部分来向应用的用户推送通知。  

## <a name="do-your-first-push-notification-campaign"></a>执行第一个推送通知市场活动
* 请确认已使用 SDK 将市场宣传集成到应用中。 
* 选择应用程序

![First1][1]

* 转至“市场宣传”部分并单击“新建公告”

![First2][2]

* 新建一个市场活动并为其命名
  
![First3][3]

* 选择传递通知的方式，例如“仅应用内”

![First4][4]

* 创建要推送的消息

![First5][5]

* 可以在通知中编写一个标题（可选）。
* 编写推送消息内容。
* 可以上载一个映像。 注意文件的大小不能超出 32,768 字节。
* 还可以选择更多选项，我们稍后再进行了解，以便先专注于本教程的重点。
* 将内容类型选择为“仅通知”

![First6][6]

* 创建推送市场活动，然后该活动会出现在市场活动列表中。

![First7][7]

## <a name="test-your-push-notification-campaign"></a>测试推送通知市场活动
![Test1][8]

* 注册设备。
* 单击想要推送的设备的复选框。
* 单击“测试”按钮向设备发送推送。

![Test2][9]

* 激活市场活动

![Test3][10]

* 既然已经创建自己的市场活动，则只需将其激活以用于要推送到用户的通知。

## <a name="send-personalized-pushes"></a>发送个性化推送
* 本示例创建可将自定义返利代码输入推送通知的推送。

![Personalize1][11]

通过替换应用信息标记中的标记进行个性化设置，因此首先必须确保用户已定义正确的应用信息。 在本示例中，目标用户将定义名称为 rebate_code 的应用信息标记。
如上所示，推送通知内容中包含标记 ${rebate_code}，这指示该标记将被应用信息标记的实际内容替代。

> [!WARNING]
> 如果没有为用户定义该应用信息标记，则用户不会收到推送。

* 结果

![Personalize2][12]

### <a name="you-can-further-personalize-the-text-your-notification"></a>可以进一步个性化设置通知的文本
![Personalize3][13]

* 其中包括通知的标题
* 和消息的内容。
* 选择公告类型（文本视图或 Web 视图）

![Personalize4][14]

### <a name="the-body-of-an-announcement-may-also-be-personalized-with"></a>公告的正文还可进行以下个性化设置：
* 操作 URL（如果想要自定义登录页）；
* 标题；
* 消息正文。

## <a name="differentiate-your-push-notification-in-or-out-of-app"></a>区分推送通知（应用内或外）
* 选择要推送的通知类型，选择应用程序，转至“市场宣传”部分，选择或创建一个推送市场活动，然后转至“通知”部分。
* 单击需要的“传递模式”。
* 如果想要通知出现在特定活动（屏幕）上，则单击“限制活动”复选框。

![Differentiate1][15]

### <a name="out-of-app-only-delivery-mode"></a>“仅应用外“传递模式
![Differentiate2][16]

“仅应用外”传递模式在应用程序处于关闭状态时提供推送通知。 这是标准的推送通知。
选择“仅应用外”时，必须已在构建应用程序所基于的平台（APNS 或 GCM）中提供证书。

### <a name="see-also"></a>另请参阅
* [Apple Push Notification 服务 - 证书](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9)、[Google Cloud Messaging - 证书](http://developer.android.com/google/gcm/index.html) 

### <a name="in-app-only-delivery-mode"></a>“仅应用内”传递模式
![Differentiate3][17]

“仅应用内”传递模式在应用程序正在运行时提供推送通知。
对于此类通知，无需进入 APNS 或 GCM 系统。
可以使用应用内系统对最终用户进行市场宣传。
可以完全自定义通知并决定通知出现在哪个活动（屏幕）中。

### <a name="anytime-delivery-mode"></a>“随时”传递模式
可以选择“随时”传递模式，无论应用程序是否正在运行，均确保可对最终用户进行市场宣传。
选择“随时”时，必须已在构建应用程序所基于的平台（APNS 或 GCM）中提供证书。 

## <a name="schedule-a-push-campaign"></a>计划推送市场活动
### <a name="plan-to-start-a-campaign"></a>计划启动市场活动
![Shedule1][18]

今天是 3 月 21 日，要制作一个公告，计划于 3 月 22 日午夜发布。 无需守候在界面前等待进行推送！ 可以提前计划发送通知的准确时间。

* 取消选中“无”复选框，并选择启动时间 
* 选择启动推送市场活动的日期和时间。

### <a name="plan-to-end-a-campaign"></a>计划结束市场活动
![Shedule2][19]

要在 3 月 25 日下午 3 点中止市场活动，但是那时无法在界面前执行此操作。
无需守候在界面前等待进行推送！ 可以提前计划市场活动中止的准确时间。

* 单击“无”复选框或选择一个结束时间
* 选择想要结束推送市场活动的日期和时间。

### <a name="end-a-campaign-manually"></a>手动结束市场活动
![Shedule3][20]

默认情况下，“无”复选框处于选中状态。
这表示在市场宣传部分激活市场活动后，该活动会立即启动，在市场宣传部分结束此活动后，该活动会结束。

> [!NOTE]
> 创建的市场活动无结束日期时，该活动会将推送存储在设备本机，即使该市场活动被手动中止，在下次打开应用程序时也会显示此推送。

## <a name="enhance-a-push-notification-with-a-text-view"></a>使用文本视图优化推送通知
### <a name="what-is-a-text-view"></a>什么是文本视图？
![TextView1][21]

文本视图是包含文本内容的弹出窗口。 在最终用户单击推送通知后，会显示该弹出窗口。
可通过文本视图向最终用户显示更多内容。 还可以提供对一些操作的调用，例如跳转到应用的页面、重定向到应用商店、打开 Web 页面、发送电子邮件、启动局部地区搜索等。

### <a name="example-text-view"></a>示例：文本视图
* 在“市场宣传”部分中创建推送通知市场活动，并为市场活动命名

![TextView2][22]

* 编写要在通知上显示的消息。
* 将“公告内容类型”选择为“文本”

![TextView3][23]

> [!NOTE]
> 推送文本视图时，始终先显示一个通知。 

* 定义文本（选择文本公告内容后，将会显示子分区以允许定义要显示的文本）。

![TextView4][24]

* 编写标题，它会在消息的最上方出现。
* 编写文本视图的主要内容。
* 编写要在“操作”按钮上显示的内容（“操作”按钮允许应用程序执行特定操作，例如打开应用程序的页面、重定向到应用商店或可以提供的任意种类的源）。
* 编写要在“退出”按钮上显示的内容（单击“退出”按钮，文本视图则会消失）。
* 创建推送通知市场活动，然后该活动会出现在市场活动列表中。

![TextView5][25]

* 激活推送通知市场活动以向用户发送文本视图。

![TextView6][26]

* 结果

![TextView7][27]

* 用户收到通知并单击通知。
* 文本视图以弹出窗口显示，允许用户与其交互。

## <a name="enhance-a-push-notification-with-a-web-view"></a>使用 Web 视图优化推送通知
### <a name="what-is-a-web-view"></a>什么是 Web 视图？
![WebView1][28]

Web 视图是包含 Web 内容的弹出窗口。 当最终用户单击了推送通知以后，会显示该弹出窗口。
可通过 Web 视图与最终用户进行更多的交互。
还可以提供对一些操作的调用，例如重定向到应用商店、打开 Web 页面、发送电子邮件、启动局部地区搜索等。

### <a name="example-web-view"></a>示例：Web 视图
* 在“市场宣传”部分中创建推送市场活动，并为市场活动命名。

![WebView2][29]

* 编写要在通知上显示的消息。
* 将“公告内容类型”选择为“Web”

![WebView3][30]

### <a name="about-announcement-types"></a>关于公告类型：
* 仅通知：这是一个简单的标准通知。 也就是说，如果用户单击它，不会出现其他视图，但会发生仅与其关联的操作
* 文本公告：这是一个使用户能够查看文本视图的通知。
* Web 公告：这是一个使用户能够查看 Web 视图的通知。
  将内容选择为“Web 公告”。

> [!NOTE]
> 推送 Web 视图时，始终先显示一个通知。

* 定义 Web 内容（选择 Web 公告内容后，将会显示子分区以允许定义要显示的 Web 视图内容）。

![WebView4][31]

* 编写标题，它会在消息的最上方出现（可选）。
* 在此处编写 HTML 代码。
* 单击“源编辑模式”按钮来切换编辑模式并查看其外观。
* 编写要在“操作”按钮上显示的内容（“操作”按钮允许应用程序执行特定操作，例如打开应用程序的页面、重定向到应用商店或可以提供的任意种类的源）。
* 编写要在“退出”按钮上显示的内容（单击“退出”按钮，Web 视图则会消失）。
* 结果

![WebView5][32]

* 用户收到通知并单击通知。
* 文本视图以弹出窗口显示，允许用户与其交互。

<!--Image references-->
[1]: ./media/mobile-engagement-how-tos/First1.png
[2]: ./media/mobile-engagement-how-tos/First2.png
[3]: ./media/mobile-engagement-how-tos/First3.png
[4]: ./media/mobile-engagement-how-tos/First4.png
[5]: ./media/mobile-engagement-how-tos/First5.png
[6]: ./media/mobile-engagement-how-tos/First6.png
[7]: ./media/mobile-engagement-how-tos/First7.png
[8]: ./media/mobile-engagement-how-tos/Test1.png
[9]: ./media/mobile-engagement-how-tos/Test2.png
[10]: ./media/mobile-engagement-how-tos/Test3.png
[11]: ./media/mobile-engagement-how-tos/Personalize1.png
[12]: ./media/mobile-engagement-how-tos/Personalize2.png
[13]: ./media/mobile-engagement-how-tos/Personalize3.png
[14]: ./media/mobile-engagement-how-tos/Personalize4.png
[15]: ./media/mobile-engagement-how-tos/Differentiate1.png
[16]: ./media/mobile-engagement-how-tos/Differentiate2.png
[17]: ./media/mobile-engagement-how-tos/Differentiate3.png
[18]: ./media/mobile-engagement-how-tos/Schedule1.png
[19]: ./media/mobile-engagement-how-tos/Schedule2.png
[20]: ./media/mobile-engagement-how-tos/Schedule3.png
[21]: ./media/mobile-engagement-how-tos/TextView1.png
[22]: ./media/mobile-engagement-how-tos/TextView2.png
[23]: ./media/mobile-engagement-how-tos/TextView3.png
[24]: ./media/mobile-engagement-how-tos/TextView4.png
[25]: ./media/mobile-engagement-how-tos/TextView5.png
[26]: ./media/mobile-engagement-how-tos/TextView6.png
[27]: ./media/mobile-engagement-how-tos/TextView7.png
[28]: ./media/mobile-engagement-how-tos/WebView1.png
[29]: ./media/mobile-engagement-how-tos/WebView2.png
[30]: ./media/mobile-engagement-how-tos/WebView3.png
[31]: ./media/mobile-engagement-how-tos/WebView4.png
[32]: ./media/mobile-engagement-how-tos/WebView5.png

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


