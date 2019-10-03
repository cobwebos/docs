---
title: 诊断 Azure 通知中心内删除通知的问题
description: 了解如何诊断 Azure 通知中心的已删除通知的常见问题。
services: notification-hubs
documentationcenter: Mobile
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 04/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/04/2019
ms.openlocfilehash: c9754c1d7fee5af13de6176dbf8a1ca6e57a71eb
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213154"
---
# <a name="diagnose-dropped-notifications-in-azure-notification-hubs"></a>诊断 Azure 通知中心内删除通知的问题

对于 Azure 通知中心，一个常见问题是如何排查客户端设备上不显示应用程序发出的通知的问题。 客户想要知道删除通知的位置和原因以及如何修复该问题。 本文标识了通知被删除或设备收不到通知的各种原因。 此外还介绍了如何确定根本原因。

首先务必了解通知中心如何将通知推送到设备。

![通知中心体系结构][0]

在典型的发送通知流中，消息从*应用程序后端*发送到通知中心。 通知中心处理所有注册。 它会考虑配置的标记和标记表达式，以确定目标。 目标是指需要接收推送通知的注册。 这些注册可能跨越所有受支持的平台：Android、百度（中国的 Android 设备）、Fire OS (Amazon) iOS、Windows 和 Windows Phone。

确定目标之后，通知中心将通知推送到设备平台的*推送通知服务*。 示例包括适用于 Apple 的 Apple Push Notification 服务 (APNs) 和适用于 Google 的 Firebase Cloud Messaging (FCM)。 通知中心推送跨多批注册拆分的通知。 它通过相应的推送通知服务进行身份验证，具体取决于你在 "**配置通知中心**" 下 Azure 门户中设置的凭据。 然后，推送通知服务将通知转发到各自的*客户端设备*。

通知传递的最后一步在平台推送通知服务与设备之间进行。 通知传送可能会在推送通知过程的四个阶段（客户端、应用程序后端、通知中心和平台推送通知服务）中的任何一个阶段失败。 有关通知中心体系结构的详细信息，请参阅[通知中心概述]。

在初始测试/暂存阶段，可能出现无法传递通知的情况。 于此阶段删除的通知可能表示存在配置问题。 如果在生产阶段出现无法传递通知的情况，则可能删除部分或全部通知。 这种情况表示存在更深层次的应用程序或消息模式问题。

下一部分将着眼于各种可能删除通知的场景，从常见类型到更加稀有的类型一应俱全。

## <a name="notification-hubs-misconfiguration"></a>通知中心配置错误 ##

若要将通知发送到相应的推送通知服务，通知中心必须在应用程序的上下文中对其自身进行身份验证。 必须使用目标平台的通知服务（Microsoft、Apple、Google 等）创建开发人员帐户。 然后，你必须将应用程序注册到操作系统，你可以在其中获取用于处理目标 PNS 的令牌或密钥。

你必须将平台凭据添加到 Azure 门户中。 如果设备未收到任何通知，第一步是确保在通知中心配置正确的凭据。 凭据必须与在平台特定开发人员帐户下创建的应用程序相匹配。

有关如何完成此过程的分步说明，请参阅 [Azure 通知中心入门]。

下面是一些需要检查的常见错误配置：

### <a name="notification-hub-name-location"></a>通知中心名称位置

确保以下各个位置中的通知中心名称（不含错字）均相同：
   * 从客户端注册的位置
   * 从后端发送通知的位置
   * 配置推送通知服务凭据的位置

确保在客户端和应用程序后端上使用正确的共享访问签名配置字符串。 一般而言，必须在客户端上使用 **DefaultListenSharedAccessSignature**，在应用程序后端上使用 **DefaultFullSharedAccessSignature**。 这会授予向通知中心发送通知的权限。

### <a name="apn-configuration"></a>APN 配置 ###

必须维护两个不同的中心：一个用于生产目的，另一个用于测试。 必须将在沙盒环境中使用的证书上传到一个中心，将要在生产环境中使用的证书上传到另一个中心。 请勿尝试将不同类型的证书上传到相同的中心。 这会导致通知失败。

如果无意中将不同类型的证书上传到相同的中心，应删除该中心并重新上传到新的中心。 如果出于某种原因无法删除该中心，最起码必须从该中心删除所有现有注册。

### <a name="fcm-configuration"></a>FCM 配置 ###

1. 确保从 Firebase 获取的*服务器密钥*与你在 Azure 门户中注册的服务器密钥匹配。

   ![Firebase 服务器密钥][3]

2. 确保在客户端上配置“项目 ID”。 可以从 Firebase 仪表板获取“项目 ID”的值。

   ![Firebase 项目 ID][1]

## <a name="application-issues"></a>应用程序问题 ##

### <a name="tags-and-tag-expressions"></a>标记和标记表达式 ###

如果使用标记或标记表达式来细分受众，在发送通知时可能找不到目标。 此错误根据 send 调用中指定的标记或标记表达式而定。

发送通知时，请查看注册，确保有匹配的标记。 然后，确保仅从含有这些注册的客户端收到通知。

例如，假设使用“政治”标记向通知中心注册了所有设备。 如果使用“体育”标记发送通知，则通知将不会发送到任何设备。 复杂的用例可能涉及到标记表达式，其中已使用“标记 A”或“标记 B”进行注册，但目标是“标记 A 和 标记 B”。 本文稍后的自我诊断提示部分将介绍如何查看注册及其标记。

### <a name="template-issues"></a>模板问题 ###

如果使用模板，请确保遵循[模板]中所述的准则。

### <a name="invalid-registrations"></a>注册无效 ###

如果通知中心配置正确，所有标记或标记表达式的用法也正确，则会找到有效的目标。 应将通知发送到这些目标。 接着，通知中心会并行启动几个处理批次。 每个批次将消息发送到一组注册。

> [!NOTE]
> 由于通知中心并行处理批次，因此不保证通知的传送顺序。

通知中心已针对“最多一次”消息传递模型进行优化。 我们尝试执行重复数据消除，从而不向设备传递一次以上的通知。 在将消息发送到推送通知服务之前，会检查注册以确保仅为每个设备标识符发送一条消息。

每个批次将发送到推送通知服务，后者接受并验证注册。 在此过程中，推送通知服务可能会在某个批次中检测到一个或多个注册的错误。 然后，推送通知服务会向通知中心返回错误，而推送过程将会停止。 推送通知服务会完全删除该批次。 对于使用 TCP 流协议的 APNs 也是如此。

在这种情况下，出错的注册会从数据库中删除。 然后，我们针对该批次中的其他设备重试通知传递。

若要获取有关针对注册的失败传递尝试的更多错误信息，可以使用通知中心 REST API [按消息遥测：获取通知消息遥测数据](https://msdn.microsoft.com/library/azure/mt608135.aspx)和 [PNS 反馈](https://msdn.microsoft.com/library/azure/mt705560.aspx)。 有关示例代码，请参阅[发送 REST 示例](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/SendRestExample/)。

## <a name="push-notification-service-issues"></a>推送通知服务问题

推送通知服务收到通知后，会将通知传送到设备。 此时，通知中心无法控制向设备传送通知的操作。

由于平台通知服务非常强大，通知往往在几秒内即可抵达设备。 如果推送通知服务进行限制，通知中心会应用指数回退策略。 如果推送通知服务在 30 分钟之内都无法访问，会实施一个策略，让消息过期并永久删除它们。

如果推送通知服务尝试传递通知，但设备处于脱机状态，则推送通知服务会存储通知。 通知只会存储有限的一段时间。 等设备可用时再将通知传递到设备。

每个应用仅存储一个最近通知。 如果在设备处于脱机状态时发送了多个通知，则每个新通知将导致最后一个通知被放弃。 仅保留最新通知称为 FCM 中的在 APNs 和*折叠*中的*合并*。 （FCM 使用折叠键。）如果设备长时间处于脱机状态，则会放弃为该设备存储的通知。 有关详细信息，请参阅[APNs overview]和[About FCM messages]。

在通知中心，可以使用泛型 SendNotification API 通过 HTTP 标头来传递合并密钥。 例如，对于 .NET SDK，你会使用 `SendNotificationAsync`。 SendNotification API 还会将按原样传递的 HTTP 标头传递到各自的推送通知服务。

## <a name="self-diagnosis-tips"></a>自我诊断提示

下面介绍了诊断通知中心已删除通知的根本原因的各种途径。

### <a name="verify-credentials"></a>验证凭据 ###

#### <a name="push-notification-service-developer-portal"></a>推送通知服务开发人员门户 ####

在各自的推送通知服务开发人员门户（APNs、FCM、Windows 通知服务等）中验证凭据。 有关详细信息，请参阅[教程：使用 Azure 通知中心向通用 Windows 平台应用发送通知](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification)创建的解决方案。

#### <a name="azure-portal"></a>Azure 门户 ####

若要查看凭据并将凭据与从推送通知服务开发人员门户获取的凭据进行匹配，请在 Azure 门户中转到“访问策略”选项卡。

![Azure 门户访问策略][4]

### <a name="verify-registrations"></a>验证注册

#### <a name="visual-studio"></a>Visual Studio ####

在 Visual Studio 中，可以通过服务器资源管理器连接到 Azure，以查看和管理包括通知中心在内的多个 Azure 服务。 此快捷方式主要用于开发/测试环境。

![Visual Studio 服务器资源管理器][9]

可以查看和管理中心内的所有注册。 这些注册可按平台、本机或模板注册、标记、推送通知服务标识符、注册 ID 及过期日期分类。 还可以在此页面中编辑注册。 它对于编辑标记特别有用。

在“服务器资源管理器”中右键单击你的通知中心，然后选择“诊断”。 

![Visual Studio 服务器资源管理器：“诊断”菜单](./media/notification-hubs-diagnosing/diagnose-menu.png)

会看到以下页面：

![Visual Studio：“诊断”页](./media/notification-hubs-diagnosing/diagnose-page.png)

切换到“设备注册”页：

![Visual Studio：设备注册](./media/notification-hubs-diagnosing/VSRegistrations.png)

若要发送测试通知消息，可以使用“测试性发送”页：

![Visual Studio：测试发送](./media/notification-hubs-diagnosing/test-send-vs.png)

> [!NOTE]
> 用于编辑注册的 Visual Studio 功能只能在开发/测试有限的注册时使用。 如果需要批量编辑注册，请考虑使用以下教程中所述的导出和导入注册功能：[如何：批量导出和修改注册](https://msdn.microsoft.com/library/dn790624.aspx)。

#### <a name="service-bus-explorer"></a>服务总线资源管理器 ####

许多客户使用[服务总线资源管理器](https://github.com/paolosalvatori/ServiceBusExplorer)来查看和管理自己的通知中心。 服务总线资源管理器是一个开源项目。 

### <a name="verify-message-notifications"></a>验证消息通知

#### <a name="azure-portal"></a>Azure 门户 ####

若要向客户端发送测试通知，而不启动和运行服务后端，请在“支持 + 故障排除”下选择“测试发送”。

![Azure 中的测试发送功能][7]

#### <a name="visual-studio"></a>Visual Studio ####

也可以从 Visual Studio 发送测试通知。

![Visual Studio 中的测试发送功能][10]

有关将通知中心与 Visual Studio 服务器资源管理器搭配使用的详细信息，请参阅以下文章：

* [如何查看通知中心的设备注册](https://docs.microsoft.com/previous-versions/windows/apps/dn792122(v=win.10))
* [深入探讨：Visual Studio 2013 Update 2 RC 和 Azure SDK 2.3]
* [宣布推出 Visual Studio 2013 Update 3 和 Azure SDK 2.4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>调试失败的通知和查看通知结果

#### <a name="enabletestsend-property"></a>EnableTestSend 属性 ####

通过通知中心发送通知时，通知最初将会排队。 通知中心确定正确的目标后，便将通知发送到推送通知服务。 如果使用 REST API 或任意客户端 SDK，send 调用的返回只表示消息已在通知中心排队。 至于通知中心最终将消息发送到推送通知服务时发生了什么情况，此调用不提供任何深入信息。

如果通知未抵达客户端设备，原因可能是当通知中心尝试将消息传送到推送通知服务时发生了错误。 例如，有效负载大小可能超出了推送通知服务允许的上限，或者在通知中心配置的凭据可能无效。

若要深入了解推送通知服务错误，可以使用 [EnableTestSend] 属性。 从门户或 Visual Studio 客户端发送测试消息时，系统会自动启用此属性。 可以使用此属性以及通过 API 查看详细的调试信息。 目前可在 .NET SDK 中使用此属性。 最终，它将添加到所有客户端 SDK 中。

若要结合使用 `EnableTestSend` 属性和 REST 调用，可在发送调用的末尾追加名为 test 的查询字符串参数。 例如：

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

#### <a name="net-sdk-example"></a>.NET SDK 示例 ####

下面是使用 .NET SDK 发送本机弹出 (toast) 通知的示例：

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

在执行结束时，`result.State` 只表示 `Enqueued`。 结果未深入分析推送通知发生了什么。

接下来，可以使用 `EnableTestSend` 布尔属性。 初始化 `NotificationHubClient` 时，可使用 `EnableTestSend` 属性获取有关发送通知时出现的推送通知服务错误的详细状态。 send 调用需要更多时间才能返回，因为它首先需要通知中心将通知传送到推送通知服务。

```csharp
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }
```

#### <a name="sample-output"></a>示例输出 ####

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

此消息表示在通知中心内配置的凭据无效，或者中心内的注册有问题。 请删除此注册，让客户端重新创建注册后再发送消息。

> [!NOTE]
> 使用 `EnableTestSend` 属性受到严重限制。 此选项只能在开发/测试环境中与一组有限的注册结合使用。 调试通知只会发送到 10 个设备。 此外，限制为每分钟最多处理 10 个调试发送操作。

### <a name="review-telemetry"></a>查看遥测 ###

#### <a name="azure-portal"></a>Azure 门户 ####

在该门户中，可以快速了解通知中心的所有活动。

1. 在“概述”选项卡上，可以查看每个平台的注册、通知和错误的汇总视图。

   ![通知中心概述仪表板][5]

2. 在“监视器”选项卡上，可以添加许多其他平台特定指标，以便进行深入了解。 可以专门查看当通知中心尝试将通知发送到推送通知服务时返回的错误。

   ![Azure 门户活动日志][6]

3. 首先查看“传入消息”、“注册操作”和“成功通知”。 然后转到每个平台选项卡查看特定于推送通知服务的错误。

4. 如果通知中心的身份验证设置不正确，则出现“PNS 身份验证错误”消息。 它表示要检查推送通知服务凭据。

#### <a name="programmatic-access"></a>以编程方式访问 ####

有关以编程方式访问的详细信息，请参阅[编程访问](https://docs.microsoft.com/previous-versions/azure/azure-services/dn458823(v=azure.100))。

> [!NOTE]
> 与遥测相关的多项功能（例如，导出和导入注册、通过 API 进行遥测访问）只能在“标准”服务层级使用。 如果尝试从“免费”或“基本”服务层级使用这些功能，则在使用 SDK 时会收到异常消息。 如果直接从 REST API 使用这些功能，将会收到 HTTP 403（已禁止）错误。
>
> 若要使用与遥测相关的功能，首先确保在 Azure 门户中使用“标准”服务层级。  

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/FCMConfigure.png
[3]: ./media/notification-hubs-diagnosing/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png

<!-- LINKS -->
[通知中心概述]: notification-hubs-push-notification-overview.md
[Azure 通知中心入门]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[模板]: https://msdn.microsoft.com/library/dn530748.aspx
[APNs overview]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[About FCM messages]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: https://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[View device registrations for notification hubs]: https://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[深入探讨：Visual Studio 2013 Update 2 RC 和 Azure SDK 2.3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[宣布推出 Visual Studio 2013 Update 3 和 Azure SDK 2.4]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Programmatic telemetry access]: https://msdn.microsoft.com/library/azure/dn458823.aspx
