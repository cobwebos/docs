---
title: "Azure Mobile Engagement 故障排除指南 - SDK"
description: "Azure Mobile Engagement 中的 SDK 集成问题故障排除"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: dwrede
editor: 
ms.assetid: de265cf1-2f88-43ef-8616-156ada5be7b6
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 46a86bf99c1afd09ae3921a205d27532246171c9


---
# <a name="troubleshooting-guide-for-sdk-integration-issues"></a>SDK 集成问题故障排除指南
以下是在将 Azure Mobile Engagement 集成到应用程序中时可能会遇到的问题。

## <a name="sdk-issues-discovered-by-a-failure-in-another-area-of-your-application"></a>通过应用程序另一个区域中的某个故障所发现的 SDK 问题
### <a name="issue"></a>问题
* UI 数据收集故障（在分析、监视、分段或仪表板中）。
* 推送故障（推送不能在应用中和/或应用外正常工作）。
* 高级功能故障（跟踪、地理位置或平台特定推送不能正常工作）。
* API 故障（API 出故障时通常没有提示，不显示任何错误消息）。
* 服务故障（没有任何 Azure Mobile Engagement 适合你的应用程序）。

### <a name="causes"></a>原因
* 大多数需要解决的 Azure Mobile Engagement SDK 问题都会是通过应用程序中的某个故障发现的（如 UI 数据收集故障、推送故障、高级功能故障、API 故障、应用程序崩溃或明显的服务中断）。  
* 如果 Azure Mobile Engagement 的某个特定功能之前从未在应用中正常工作过，那么你将需要完成该集成。 
* 如果 Azure Mobile Engagement 的某个特定功能曾经正常工作过并被停止，你可能需要升级到 Azure Mobile Engagement SDK 的最新版本。 请记住，对于 Azure Mobile Engagement 所支持的每种平台（Android、iOS、Windows 和 Windows Phone），都有一个不同版本的 Azure Mobile Engagement SDK。

#### <a name="sdk-integration"></a>SDK 集成
* Azure Mobile Engagement 未正确在 SDK 中集成（分析）。
* 市场宣传未正确在 SDK 中集成（应用内推送和应用外推送）。
* 证书过期或生产版本不正确。开发（仅限 iOS）。
* GCM 或 ADM 未正确在 SDK 中集成（仅 Android - 服务特定推送）。
* 跟踪未正确在 SDK 中集成（安装存储跟踪）。
* 简易位置或 GPS 位置未正确在 SDK 中集成（按地理位置进行定向）。

**另请参阅：**

* [SDK 文档 - 集成指南][Link 5] 
* [故障排除指南 - 推送][Link 23]

#### <a name="sdk-upgrade"></a>SDK 升级
* 需要先升级 SDK 以解决 SDK 版本较旧的问题（通常与设备 OS 的较新版本相关）。
* 从设备卸载应用的所有先前版本，并重新安装应用的最新版本，然后从 Azure Mobile Engagement UI 重新注册你的设备 ID，以确认你的设备使用的是最新版本的应用。

**另请参阅：**

* [SDK 文档 - 发行说明](http://go.microsoft.com/fwlink/?LinkId= 525554) 
* [SDK 文档 - 升级指南](http://go.microsoft.com/fwlink/?LinkId= 525554)

#### <a name="sdk-other"></a>SDK 其他
* 在应用程序清单 AndroidManifest.xml 中的错误可能导致 Azure Mobile Engagement 无法正常工作（仅针对 Android）。
* SDK 集成和 API 使用方面的一个常见问题是混淆 SDK 密钥和 API 密钥。

**另请参阅：**

* [概念 - 术语表][Link 6]

## <a name="advanced-coding-issues"></a>高级编码问题
### <a name="issue"></a>问题
* 不直接与 Azure Mobile Engagement 相关的平台特定代码会导致在 iOS、Android 和 Windows Phone 上的问题。

### <a name="causes"></a>原因
* Azure Mobile Engagement 的很多高级编码问题都是由未正确编写的、不直接与 Azure Mobile Engagement 相关的平台特定代码引起的。 除了 Azure Mobile Engagement 文档之外，你还将需要参考特定于你正开发的平台文档（Android、iOS、Web、Windows 和 Windows Phone）。
* 未正确配置“类别”，阻止了从某个通知到应用内部或外部的另一个位置的链接（仅针对 Android）。 
* 在 iOS 代码中未将“UIKit.framework”设置为“可选”，会显示一个“找不到符号错误”和/或在较旧的 iOS 设备上崩溃（仅针对 iOS）。
* 证书过期或未正确使用证书的开发版本或生产版本，会导致推送问题（仅针对 iOS）。
* 存在某些 Azure Mobile Engagement 无法控制的平台固有限制（比如，在 Android 和 iOS 中，系统中心如何适应应用外推送）。
* Azure Mobile Engagement 发布了由 Azure Mobile Engagement for iOS and Android 所使用的内部软件包的一个完整列表以供参考。 请记住，Azure Mobile Engagement 的某些功能是特定于平台的（Android、iOS、Web、Windows 和 Windows Phone）。

### <a name="see-also"></a>另请参阅
* [故障排除指南 - 推送][Link 23] 
* [SDK 文档 - 发行说明][Link 5]
* [SDK 文档 - 升级指南][Link 5]

## <a name="application-crashes"></a>应用程序崩溃
### <a name="issue"></a>问题
* 你的应用程序在最终用户的设备上崩溃。

### <a name="causes"></a>原因
* 可以在“*分析 UI*”或“*分析 API*”中查看崩溃信息
* 你可以查找测试设备的设备 ID，并对最终用户执行导致你的应用程序崩溃的相同操作，以帮助识别你的崩溃的原因。
* 有时可以通过升级到最新版本的 SDK，解决导致应用程序崩溃的 Azure Mobile Engagement SDK 已知问题。 请确保在调查崩溃时查看关于你的平台发行说明。

### <a name="see-also"></a>另请参阅
* [SDK 文档 - 发行说明][Link 5]
* [SDK 文档 - 升级指南][Link 5]

## <a name="app-store-upload-failures"></a>应用商店上传故障
### <a name="issue"></a>问题
* 与将你的应用的最新版本上载到 Apple、Google 或 Windows 应用商店相关的错误。

### <a name="causes"></a>原因
* 应用商店有时会阻止应用启用某些功能（例如 Apple 应用商店会禁止在商店中的应用中使用 IDFV，而 GooglePlay 应用商店会禁止在应用之间共享应用程序信息）。 
* 如果在将应用上载到应用商店时遇到困难，请务必查看一下你的平台发行说明和 SDK 的当前版本。

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/en-us/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/en-us/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/en-us/pricing/details/mobile-engagement/
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




<!--HONumber=Dec16_HO2-->


