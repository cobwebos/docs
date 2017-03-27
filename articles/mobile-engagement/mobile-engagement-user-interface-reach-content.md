---
title: "Azure Mobile Engagement 用户界面 - 市场宣传内容"
description: "了解如何在 Azure Mobile Engagement 中管理不同类型的推送通知活动的独特内容"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: add64f06-43c9-475c-8722-51cd00bb844b
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 06e16033435ed0a37d5688055743875827d3aec2
ms.openlocfilehash: 3741a43b74af5846e95e42d8a7b533621e780f2d
ms.lasthandoff: 03/01/2017


---
# <a name="how-to-manage-the-unique-content-of-the-different-types-of-push-notification-campaigns"></a>如何管理不同类型的推送通知活动的独特内容
你可以使用新的市场宣传活动的内容部分修改公告、投票、数据推送和磁贴（仅 Windows Phone）的内容。 推送活动的内容设置是与活动类型相关的。 

### <a name="content-types"></a>内容类型：
* 公告
* 投票
* 数据推送
* 磁贴（仅 Windows Phone）

## <a name="content-of-announcements"></a>公告的内容
 ![市场宣传内容&1;][30] 

### <a name="choose-the-type-of-your-announcement"></a>选择公告的类型：
* 仅通知：这是一个简单的标准通知。 也就是说，如果用户单击它，不会出现其他视图，但会发生仅与其关联的操作
* 文本公告：这是一个使用户能够查看文本视图的通知。
* Web 公告：这是一个使用户能够查看 Web 视图的通知。

### <a name="see-also"></a>另请参阅
* [市场宣传 - 操作说明 - 公告][Link 3] 

### <a name="about-web-view-announcements"></a>关于 Web 视图公告：
你在此处提供的 HTML 代码或 JavaScript 代码中出现的“{deviceid}”模式都将由显示该公告的设备的标识符自动替换。 这是一种检索在你的后端办公系统上托管的外部 Web 服务中的 Azure Mobile Engagement 设备标识符的简单方式。
如果你想要创建全屏 Web 视图（不带我们提供的默认操作按钮和退出按钮），则可以使用 Web 视图公告的 JavaScript 代码中的以下函数： 

* 执行公告操作：ReachContent.actionContent()
* 退出公告：ReachContent.exitContent()

### <a name="choose-your-action"></a>选择操作：
### <a name="about-action-urls"></a>关于操作 URL：
可由目标设备的操作系统解释的任何 URL 都可用作操作 URL。
你的应用程序可能支持的任何专用 URL（例如，让用户跳转到特定屏幕）也可用作操作 URL。
出现的每个 {deviceid} 模式都将由执行该操作的设备的标识符自动替换。 这可用于通过在你的后端办公系统上托管的外部 Web 服务轻松地检索 Azure Mobile Engagement 设备标识符。

* **Android + iOS 操作**
  * 打开网页
  * http://\[网站域\] 
  * 示例：http://www.azure.com
  * 发送电子邮件
  * mailto:\[电子邮件收件人\]?subject=\[主题\]&body=\[消息\] 
  * Example:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
  * 发送短信
  * sms:\[电话号码\] 
  * 示例：sms:2125551212
  * 拨打电话号码
  * tel:\[电话号码\] 
  * 示例：tel:2125551212
* **仅限 Android 操作**
  * 从 Play Store 下载应用程序
  * market://details?id=\[应用包\] 
  * 示例：market://details?id=com.microsoft.office.word
  * 开始局部地区搜索
  * geo:0,0?q=\[搜索查询\] 
  * 示例：geo:0,0?q=starbucks,paris
* **仅限 iOS 操作**
  * 从 App Store 下载应用程序
  * http://itunes.apple.com/[country]/app/[app name]/id[app id]?mt=8 
  * 示例:http://itunes.apple.com/fr/app/briquet-virtuel/id430154748?mt=8
  * Windows 操作
  * 打开网页
  * http://\[网站域\] 
  * 示例：http://www.azure.com
  * 发送电子邮件
  * mailto:\[电子邮件收件人\]?subject=\[主题\]&body=\[消息\] 
  * Example:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
  * 发送短信（需要 Skype 应用商店应用）
  * sms:\[电话号码\] 
  * 示例：sms:2125551212
  * 拨打电话号码（需要 Skype 应用商店应用）
  * tel:\[电话号码\] 
  * 示例：tel:2125551212
  * 从 Play Store 下载应用程序
  * ms-windows-store:PDP?PFN=\[应用包 ID\] 
  * 示例：ms-windows-store:PDP?PFN=4d91298a-07cb-40fb-aecc-4cb5615d53c1
  * 开始 Bing Maps 搜索
  * bingmaps:?q=\[搜索查询\] 
  * 示例：bingmaps:?q=starbucks,paris
  * 使用自定义方案
  * \[自定义方案\]://\[自定义方案参数\] 
  * 示例：myCustomProtocol://myCustomParams
  * 使用程序包数据（需要用于扩展名读取的应用商店应用）
  * \[文件夹\]\[数据\].\[扩展名\] 
  * 示例：myfolderdata.txt

### <a name="build-a-tracking-url"></a>生成跟踪 URL：
* 请参阅 <UI Documentation>的“设置”部分，了解有关生成将允许用户下载其他应用程序之一的跟踪 URL 的说明。

### <a name="define-the-texts-of-your-announcement"></a>定义公告的文本
填写公告的标题、内容和按钮文本。 你可以基于用户对此活动的响应方式的市场宣传反馈确定未来活动的访问群体。 可以基于是只推送、回复、操作还是退出此活动的反馈来确定访问群体。

### <a name="see-also"></a>另请参阅
* [UI 文档 - 市场宣传 - 新推送条件][Link 28]

## <a name="content-of-polls"></a>投票内容
![市场宣传内容&2;][31] 

填写公告的标题、说明和按钮文本。 然后，添加问题以及问题答案的选项。
你可以基于用户对此活动的响应方式的市场宣传反馈确定未来活动的访问群体。 可以基于是只推送、回复、操作还是退出此活动确定访问群体。 也可以基于投票结果反馈（其中问题和答案选项用作条件）确定访问群体。

### <a name="see-also"></a>另请参阅
* [UI 文档 - 市场宣传 - 新推送条件][Link 28]

## <a name="content-of-data-pushes"></a>数据推送的内容
![市场宣传内容&3;][32] 

### <a name="choose-the-type-of-your-data"></a>选择数据的类型：
* 文本
* 二进制数据
* Base64 数据

### <a name="define-the-content-of-your-data"></a>定义数据的内容
* 如果选择推送文本数据，则将该文本复制并粘贴到“内容”框中。
* 如果选择推送二进制数据或 base64 数据，则使用“上载文件”按钮上载文件。
* 你可以基于用户对此活动的响应方式的市场宣传反馈确定未来活动的访问群体。 可以基于是只推送、回复、操作还是退出此活动确定访问群体。

### <a name="see-also"></a>另请参阅
* [UI 文档 - 市场宣传 - 新推送条件][Link 28]

## <a name="content-of-tiles-windows-phone-only"></a>磁贴的内容（仅 Windows Phone）
![市场宣传内容&4;][33]

### <a name="define-the-content-of-your-tile"></a>定义磁贴的内容
磁贴有效负载是要在 Windows Phone 设备上的应用的磁贴中显示的文本。
磁贴推送是适用于 Windows Phone 的 Microsoft 推送通知服务 (MPNS) 版本的本机推送。 磁贴推送类型是唯一没有响应的推送类型，因此未来活动的访问群体不能基于磁贴推送活动的结果。 

### <a name="see-also"></a>另请参阅
* [API 文档 - 市场宣传 API - 原生推送][Link 4]

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


