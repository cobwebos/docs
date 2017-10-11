---
title: "故障排除指南-SDK 的 azure Mobile Engagement"
description: "在 Azure Mobile Engagement SDK 集成问题疑难解答"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: de265cf1-2f88-43ef-8616-156ada5be7b6
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 4d9d6165deb4bd0c65f1841aa7c457363a1f2865
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshooting-guide-for-sdk-integration-issues"></a>SDK 集成问题的疑难解答指南
以下是可能存在的问题，你可能会遇到 Azure Mobile Engagement 是如何集成到你的应用程序。

## <a name="sdk-issues-discovered-by-a-failure-in-another-area-of-your-application"></a>发现你的应用程序的另一个区域中的失败的 SDK 问题
### <a name="issue"></a>问题
* UI （中分析、 监视、 分段，或仪表板中） 的数据集合失败。
* 推送失败 （推送在应用中，流出应用，还是情况下不起作用）。
* 高级功能失败 （跟踪、 地理位置或平台特定的推送不起作用）。
* API 失败 （Api 失败通常以无提示方式并且没有错误消息）。
* （无 Azure Mobile Engagement 适合于你的应用程序） 的服务失败。

### <a name="causes"></a>原因
* 将你的应用程序 （如 UI 数据集合失败、 推送失败、 高级的功能失败、 API 失败、 应用程序崩溃或明显的服务中断中） 中的失败发现需要解决与 Azure Mobile Engagement SDK 的大多数问题。  
* 如果在应用程序之前，特定功能的 Azure Mobile Engagement 一直永远不会正常工作，你将需要完成集成。 
* 如果 Azure Mobile Engagement 的特定功能运行正常，并且停止，你可能需要升级到最后一个与 Azure Mobile Engagement SDK 的版本。 请记住，Azure Mobile Engagement SDK for Azure Mobile Engagement （Android、 iOS、 Windows 和 Windows Phone） 支持每个平台的不同版本。

#### <a name="sdk-integration"></a>SDK 集成
* Azure Mobile Engagement 未正确集成 SDK （分析） 中。
* 访问未正确集成 SDK （应用内和应用程序推送外） 中。
* 证书已过期或不正确 PROD vs。开发人员 (仅限 iOS)。
* GCM 或 ADM 未正确集成 SDK 中 (Android 只-服务特定推送安装)。
* 跟踪未正确集成 SDK （跟踪的安装应用商店） 中。
* 延迟的位置或 GPS 位置不正确集成 SDK (Targeting 通过地理位置) 中。

**另请参阅：**

* [SDK 文档-集成指南][Link 5] 
* [故障排除指南-推送][Link 23]

#### <a name="sdk-upgrade"></a>SDK 升级
* 需要升级以解决问题 （通常与较新版本的设备操作系统） 的 sdk 的较旧版本的 SDK。
* 从你的设备上卸载所有以前版本的应用程序并重新安装最新版本的应用程序，重新注册设备 ID 从 Azure Mobile Engagement UI，以确认你的设备正在使用你的应用程序的最新版本。

**另请参阅：**

* [SDK 文档-发行说明](http://go.microsoft.com/fwlink/?LinkId= 525554) 
* [SDK 文档-升级指南](http://go.microsoft.com/fwlink/?LinkId= 525554)

#### <a name="sdk-other"></a>其他 SDK
* 应用程序清单"AndroidManifest.xml"中的错误可能会导致 Azure Mobile Engagement 无法正常工作 (仅 Android)。
* 使用 SDK 集成和 API 使用情况的一个常见问题是混淆 SDK 密钥和 API 密钥。

**另请参阅：**

* [概念-术语表][Link 6]

## <a name="advanced-coding-issues"></a>高级编码问题
### <a name="issue"></a>问题
* 在 iOS、 Android 和 Windows Phone 上，不是直接与 Azure Mobile Engagement 平台特定代码可能会导致问题。

### <a name="causes"></a>原因
* 很多高级编码问题与 Azure Mobile Engagement 是由编写不当的平台特有的代码不是直接与 Azure Mobile Engagement 引起的。 你将需要查阅特定于平台的开发除了 Azure Mobile Engagement 文档 （Android、 iOS、 Web、 Windows 和 Windows Phone） 的文档。
* 未正确配置"类别"，可以防止从通知链接到另一个位置内部或外部应用 (仅 Android)。 
* 未设置"UIKit.framework""可选"在 iOS 代码中，显示了一个"未找到错误的符号"和/或在较旧的 iOS 设备 (仅限 iOS) 上崩溃。
* 已过期的证书，或未正确使用 cert 原因的开发人员或 Prod 版本推送问题 (仅限 iOS)。
* 有一些限制所固有的一个平台，Azure Mobile Engagement 后才能控制它们 （例如 system center 工作原理的应用程序外推送以 Android 和 iOS）。
* Azure Mobile Engagement 发布内部由 Azure Mobile Engagement 适用于 iOS 和 Android 用于引用的包的完整列表。 请记住，Azure Mobile Engagement 的某些功能是特定于平台 （Android、 iOS、 Web、 Windows 和 Windows Phone）。

### <a name="see-also"></a>另请参阅
* [故障排除指南-推送][Link 23] 
* [SDK 文档-发行说明][Link 5]
* [SDK 文档-升级指南][Link 5]

## <a name="application-crashes"></a>应用程序崩溃
### <a name="issue"></a>问题
* 在最终用户的设备上，你的应用程序崩溃。

### <a name="causes"></a>原因
* 故障转储信息可在*分析 UI*或*分析 API*
* 你可以查找测试设备的设备 ID，并充分导致你的应用程序崩溃最终用户以帮助确定你的故障原因的相同操作。
* 通过升级到最新版本的 SDK 有时解析与 Azure Mobile Engagement SDK 会导致应用程序崩溃的已知的问题。 请确保在调查崩溃时检查有关你的平台的发行说明。

### <a name="see-also"></a>另请参阅
* [SDK 文档-发行说明][Link 5]
* [SDK 文档-升级指南][Link 5]

## <a name="app-store-upload-failures"></a>应用程序存储上载失败
### <a name="issue"></a>问题
* 与将您的应用程序的最新版本上载到 Apple、 Google 或 Windows 应用商店相关的错误。

### <a name="causes"></a>原因
* 应用程序将阻止应用存储有时启用某些功能 （例如 Apple 应用商店可防止在应用商店中应用使用 IDFV 和 GooglePlay 存储阻止的应用程序信息的应用之间共享）。 
* 请确保你检查有关你的平台和当前版本的 SDK 的发行说明，如果你有困难上载到应用商店应用。

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

