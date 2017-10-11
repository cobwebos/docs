---
title: "Azure Mobile Engagement 用户界面 - 设置"
description: "了解如何使用 Azure Mobile Engagement 管理应用程序的全局设置"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 858f4cb4-14de-4bb5-826f-28cadbfc928b
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: af5c81df2b9f288161b38625d3ac2adde8fb195d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-manage-the-global-settings-of-your-application"></a>如何管理应用程序的全局设置
应用程序的“**设置**”菜单选项不尽相同，具体取决于应用程序所在的平台以及授予给应用程序权限。 设置包括以下内容：详细信息、项目、原生推送、推送速度、标记（应用信息） 和商业压力。 “设置”部分的“标记”（应用信息）菜单选项由应用程序通过 SDK 管理，或由后端通过设备 API 管理。 

> [!NOTE]
> **Mobile Engagement** 门户 UI 的很多部分中都包含“**显示帮助**”按钮。 按此按钮可获取关于某一部分的更多上下文信息。
> 
> 

## <a name="details"></a>详细信息
允许更改应用程序的名称和描述，查看应用程序的所有者和角色权限。 

分析配置允许查看或更改周开始日期和保留时间（天数）。

  ![settings1][46]

## <a name="projects"></a>项目
允许选择希望应用程序显示在其中的所有项目。 

你也可以搜索项目和查看的名称、 描述、 所有者和你的应用程序属于任何项目你角色的权限。

有关详细信息，请查看：[UI 文档 - 主页][Link 13]

  ![settings3][48]

## <a name="native-push"></a>原生推送
允许注册新证书或删除任何用于原生推送的现有证书。 原生推送允许 Azure Mobile Engagement 随时向应用程序推送，即使应用程序并未运行。 

在为至少一个原生推送服务提供凭据或证书后，创建 Reach Campaigns 时可以选择“随时”选项，还可以在 PUSH API 中使用“notifier”参数。

### <a name="apple-push-notification-service-apns"></a>Apple 推送通知服务 (APNS)
要使用 Apple 推送通知服务来启用原生推送，需要注册证书。 需要指定证书类型为开发 (DEV) 或生产 (PROD)。 然后需要上传证书和密码。

有关详细信息，请查看：[SDK 文档 - iOS - 如何为 Apple 推送通知准备应用程序][Link 5]

![settings4][49]

### <a name="windows-push-notification-service-wpns"></a>Windows 推送通知服务 (WPNS)
要使用 Windows 通知服务来启用原生推送，必须提供应用程序的凭据。 需要程序包安全标识符 (SID) 和密钥。

![settings5][50]

### <a name="google-cloud-messaging-for-android-gcm"></a>Google Cloud Messaging for Android (GCM)
要使用 GCM 来启用原生推送，需要遵循来自 Google 的操作说明。 然后必须粘贴服务器简单 API 密钥，配置为无 IP 限制。 要求与 SDK for Android v1.12.0 + 相集成。

有关详细信息，请参阅： 

* [SDK 文档 - Android - 如何集成 GCM][Link 5]
* [Google 开发人员 GCM 指南](http://developer.android.com/guide/google/gcm/gs.html)

### <a name="amazon-device-messaging-for-android-adm"></a>Amazon Device Messaging for Android (ADM)
要使用 ADM 来启用原生推送，必须提供由客户端 ID 和客户端密钥组成的 Amazon <OAuth credentials>（要求与 SDK for Android v2.1.0 + 相集成）。

有关详细信息，请参阅： 

* [SDK 文档 - Android - 如何集成 ADM][Link 5]
* [Amazon 开发人员 ADM 文档](https://developer.amazon.com/sdk/adm/credentials.html#Getting)

![settings6][51]

## <a name="push-speed"></a>推送速度
显示应用程序当前的推送速度，并允许定义应用程序的推送速度。

  ![settings7][52]

## <a name="tag-app-info"></a>标记（应用信息）
![settings11][56]

## <a name="commercial-pressure"></a>商业压力
![settings12][57]

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

