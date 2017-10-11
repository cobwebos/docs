---
title: "Azure 通知中心"
description: "了解如何添加推送通知功能使用 Azure 通知中心。"
author: ysxu
manager: erikre
editor: 
services: notification-hubs
documentationcenter: 
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 1/17/2017
ms.author: yuaxu
ms.openlocfilehash: a1be0b13cd1feb582a23965df142e44d90ac6851
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="azure-notification-hubs"></a>Azure 通知中心
## <a name="overview"></a>概述
Azure 通知中心提供了易于使用、 多平台、 向外扩展的推送引擎。 使用单一跨平台 API 调用，你可以轻松发送目标和个性化推送通知向任何移动平台从任何云或本地后端。

通知中心的工作原理非常适用于企业和使用者方案。 下面是客户使用通知中心的几个示例：

* 发送到较低的延迟数以百万计的突发新闻通知。
* 向感的用户群发送基于位置的优惠券。
* 将与事件相关的通知发送到用户或组媒体/体育/财务/游戏应用程序。
* 将促销内容推送到应用程序以面向和赢得客户。
* 通知企业事件，如新消息的用户和工作项。
* 发送多因素身份验证的代码。

## <a name="what-are-push-notifications"></a>什么是推送通知？
推送通知是信息的一种形式的应用程序用户通信其中的某些所需，通常在一个弹出窗口或对话框框，通知用户的移动应用。 用户通常可以选择要查看或关闭消息，并选择前者将打开沟通的通知的移动应用程序。

推送通知是提高应用程序用户参与策略和使用情况，使用者应用程序和适用于通信最新的业务信息的企业应用至关重要的。 它是最适合应用到用户通信，因为它是高能效对于移动设备，用于通知发送方，灵活和可用时相应的应用处于非活动状态。

有关的几个常用的平台的推送通知的详细信息：
* [iOS](https://developer.apple.com/notifications/)
* [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
* [Windows](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx)

## <a name="how-push-notifications-work"></a>如何将推送通知的工作
推送通知都通过特定于平台的基础结构调用*平台通知系统*(PNSes)。 它们提供精简将邮件传递到具有提供的设备的推送功能处理，并且没有常见界面。 若要将通知发送到所有客户，在 iOS、 Android 和 Windows 版本的应用程序，开发人员必须工作 APNS （Apple 推送通知服务）、 FCM （Firebase 云消息传送），与 WNS （Windows 通知服务） 时批处理发送。

在高级别，以下是推送的工作原理：

1. 客户端应用程序决定它想要接收推送因此联系相应 PNS 以检索其唯一的临时推送句柄。 句柄类型取决于的系统的证书 （如 WNS 有 Uri 而 APNS 有令牌时间）。
2. 客户端应用程序将此句柄存储在应用后端或提供程序。
3. 若要发送推送通知，应用后端，请联系 PNS 使用此句柄面向特定的客户端应用程序。
4. PNS 将转发到句柄指定的设备的通知。

![][0]

## <a name="the-challenges-of-push-notifications"></a>推送通知的难题
虽然 PNSes 强大，它们将极大的工作留给应用开发人员为了实现常见推送通知方案，如广播或将推送通知发送给分段用户。

推送是移动云服务中最常请求的功能之一，因为其工作需要复杂的基础结构，与应用程序的主业务逻辑无关。 下面是一些基础挑战：

* **平台依赖性**: 

  * 后端需要具有复杂且硬维护平台相关逻辑，将通知发送到各种平台上的设备，如 PNSes 不相一致。
* **缩放**:

  * 根据 PNS 准则，必须在每个应用程序启动时刷新设备令牌。 这意味着后端处理大量的流量和数据库的访问权限，只是为了保持最新的令牌。 当的设备数量增长到数百和数千种数以百万计时，创建和维护此基础结构的成本是大规模。
  * 大多数 PNSes 不支持广播到多个设备。 这意味着简单广播到 100 万个设备 PNSes 100 万个调用的结果。 以最少延迟缩放的流量此量就非同小可了。
* **路由**:
  
  * 尽管 PNSes 提供了如何将消息发送到设备，大多数应用程序通知将定向到用户或兴趣组中。 这意味着后端必须维护要将设备与兴趣组、 用户、 属性等相关联的注册表。此开销增加市场和维护的成本，应用程序中的时间。

## <a name="why-use-notification-hubs"></a>为何使用通知中心？
通知中心消除了与启用相关的所有复杂性推入自己。 其多平台、 向外扩展的推送通知基础结构减少推送相关的代码，并简化了后端。 借助通知中心的设备是只负责注册其 PNS 句柄的中心，而后端将消息发送给用户或兴趣组，如下图中所示：

![][1]

通知中心是你已准备可用的推送引擎了下列优势：

* **跨平台**

  * 支持所有主要推送平台，包括 iOS、 Android、 Windows 和 Kindle 和百度。
  * 公共接口，以将推送到所有平台特定于平台的或独立于平台的格式，无特定于平台的工作。
  * 设备句柄管理在一个位置。
* **跨后端**
  
  * 云或本地
  * .NET、 Node.js、 Java 等。
* **丰富的传送模式集**:

  * *将广播到一个或多个平台*： 你可以立即广播到数百万台设备跨平台使用单个 API 调用。
  * *将推送到设备*： 则可以面向到单个设备的通知。
  * *推送到用户*： 标记和模板功能可帮助您访问所有的跨平台设备的用户。
  * *推送到与动态标记段*： 标记功能可帮助你段设备和推送到它们根据需要，是否要发送到一个段或段 （例如 active AND 都存在于不西雅图新用户） 的表达式。 而不是限于发布订阅，你可以更新设备标记任何地方和任何时候。
  * *本地化推送*： 模板功能可帮助实现而不会影响后端代码的本地化。
  * *无提示推送*： 你可以通过向设备发送无提示通知并触发它们完成某些拉伸或操作使推送请求模式。
  * *计划推送*： 你可以计划随时发出通知。
  * *直接推送*： 你可以跳过与我们的服务的注册设备并直接批处理推送到设备句柄的列表。
  * *个性化推送*： 设备推送变量可帮助你发送特定于设备的个性化推送通知使用自定义的键 / 值对。
* **丰富的遥测功能**
  
  * 在 Azure 门户中并且以编程方式，常规的推送、 设备、 错误和操作遥测才可用。
  * 每条消息遥测从对成功批处理推送出我们的服务的初始请求调用跟踪每个推送。
  * 平台通知系统反馈通信来自平台通知系统为了帮助调试的所有反馈。
* **可伸缩性** 
  
  * 将快速消息发送到数百万台设备，无需重新架构或设备的分片。
* **安全**

  * 共享访问机密 (SAS) 或联合身份验证。

## <a name="integration-with-app-service-mobile-apps"></a>使用 App Service 移动应用程序集成
若要方便无缝和统一体验跨 Azure 服务， [App Service Mobile Apps]提供内置支持，以使用通知中心发送推送通知。 [App Service Mobile Apps]为企业开发人员和系统集成商，将一组丰富的功能引入到移动开发人员提供高度可扩展、 全局可用的移动应用程序开发平台。

移动应用开发人员可以使用以下工作流中利用通知中心：

1. 检索设备 PNS 句柄
2. 与通过方便移动的应用程序客户端 SDK 注册 API 的通知中心注册设备
   * 请注意，Mobile Apps 消除之外的所有标记上出于安全目的的注册。 使用通知中心从后端直接以将标记与设备关联。
3. 从应用后端使用通知中心发送通知

下面是一些很便利，转到开发人员提供此集成：

* **移动应用客户端 Sdk**： 这些多平台 Sdk 提供简单的 Api 来注册和与自动链接使用移动应用通知中心通信。 开发人员不需要更深入地介绍通过通知中心凭据和与其他服务一起使用。

  * *推送到用户*: Sdk 自动标记给定的设备使用移动应用程序经过身份验证的用户 ID 以启用推送到用户方案。
  * *将推送到设备*: Sdk 自动使用移动应用程序安装 ID 作为 GUID 来注册通知中心，保存维护多个服务 Guid 的问题的开发人员。
* **安装模型**: Mobile Apps 配合通知中心的最新推送模型来表示与执行推送通知服务与一致和易于使用的 JSON 安装中的设备关联的所有请求属性。
* **灵活性**： 开发人员始终可以选择使用通知中心直接即使使用位置中的集成。
* **集成中的体验[Azure 门户]**： 推送移动应用中以可视方式表示一项功能以及开发人员可以轻松地通过移动应用程序关联的通知中心使用。

## <a name="next-steps"></a>后续步骤
您可以在以下主题中找到有关通知中心的更多信息：

* **[客户如何使用通知中心]**
* **[通知中心教程和指南]**
* **通知中心入门教程**: [iOS]， [Android]， [Windows 通用]， [Windows Phone]， [Kindle]， [Xamarin.iOS]， [Xamarin.Android]

[0]: ./media/notification-hubs-overview/registration-diagram.png
[1]: ./media/notification-hubs-overview/notification-hub-diagram.png
[客户如何使用通知中心]: http://azure.microsoft.com/services/notification-hubs
[通知中心教程和指南]: http://azure.microsoft.com/documentation/services/notification-hubs
[iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
[Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
[Windows 通用]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
[Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
[Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
[Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
[Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
[Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
[Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
[App Service Mobile Apps]: https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-value-prop/
[templates]: notification-hubs-templates-cross-platform-push-messages.md
[Azure 门户]: https://portal.azure.com
[tags]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)
