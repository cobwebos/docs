---
title: "Azure 通知中心的已删除通知诊断"
description: "了解如何诊断 Azure 通知中心的已删除通知的常见问题。"
services: notification-hubs
documentationcenter: Mobile
author: jwhitedev
manager: kpiteira
editor: 
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 3925208fe56bcd9513ec4c0f21aa1e2dd8fbf9c5
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2018
---
# <a name="diagnose-dropped-notifications-in-notification-hubs"></a>诊断通知中心的已删除通知

Azure 通知中心客户提出的最常见问题之一是如何排查以下问题：客户端设备上不显示从应用程序发送的通知。 他们想知道删除通知的位置和原因以及如何修复该问题。 本文标识了通知被删除或设备收不到通知的各种原因。 了解如何分析并确定根本原因。 

首先务必了解通知中心如何将通知推送到设备。

![通知中心体系结构][0]

在典型的发送通知流中，消息从*应用程序后端*发送到通知中心。 通知中心对所有注册进行一些处理。 处理时会考虑配置的标记和标记表达式，以确定“目标”。 目标是指需要接收推送通知的所有注册。 这些注册可以横跨各种受支持的平台：iOS、Google、Windows、Windows Phone、Kindle 和 Baidu for China Android。

确定目标之后，通知中心将通知推送到设备平台的*推送通知服务*。 示例包括适用于 Apple 的 Apple Push Notification 服务 (APNs) 和适用于 Google 的 Firebase Cloud Messaging (FCM)。 通知中心推送跨多批注册拆分的通知。 通知中心基于你在 Azure 门户的“配置通知中心”下设置的凭据，向各自的推送通知服务验证身份。 然后，推送通知服务将通知转发到各自的*客户端设备*。 

请注意，通知传递的最后一步在平台推送通知服务与设备之间进行。 推送通知过程中四个主要组件（客户端、应用程序后端、通知中心和平台推送通知服务）的任意一个都有可能导致通知被删除。 有关通知中心体系结构的详细信息，请参阅[通知中心概述]。

在初始测试/暂存阶段，可能出现无法传递通知的情况。 于此阶段删除的通知可能表示存在配置问题。 如果在生产阶段出现无法传递通知的情况，则可能删除部分或全部通知。 这种情况表示存在更深层次的应用程序或消息模式问题。 

下一节将着眼于各种可能删除通知的场景，从常见类型到更加稀有的类型一应俱全。

## <a name="notification-hubs-misconfiguration"></a>通知中心配置错误
若要成功将通知发送到各自的推送通知服务，通知中心需要在开发人员的应用程序环境中对自身进行身份验证。 为此，开发人员可在各自的平台（Google、Apple、Windows 等）中创建开发人员帐户。 然后，开发人员向从中获取凭据的平台注册自己的应用程序。 

你必须将平台凭据添加到 Azure 门户中。 如果设备未收到任何通知，第一步应该确保在通知中心配置正确的凭据。 凭据必须与在平台特定开发人员帐户下创建的应用程序相匹配。 

有关如何完成此过程的分步说明，请参阅 [Azure 通知中心入门]。

下面是一些需要检查的常见错误配置：

* **常规**
   
    * 确保以下各个位置中的通知中心名称（不含错字）均相同：

        * 从客户端注册的位置。
        * 从后端发送通知的位置。
        * 配置推送通知服务凭据的位置。
    
    * 确保在客户端和应用程序后端上使用正确的共享访问签名配置字符串。 一般说来，必须在客户端上使用 **DefaultListenSharedAccessSignature**，在应用程序后端上使用 **DefaultFullSharedAccessSignature**（授予向通知中心发送通知的权限）。

* **APN 配置**
   
    必须维护两个不同的中心：一个用于生产，另一个用于测试。 这意味着必须将在沙盒环境中使用的证书上传到一个中心，将要在生产环境中使用的证书上传到另一个中心。 请勿尝试将不同类型的证书上传到相同的中心。 这可能导致通知失败。 
    
    如果无意中将不同类型的证书上传到相同的中心，建议删除该中心并重新上传到新的中心。 如果出于某种原因无法删除该中心，最起码必须从该中心删除所有现有注册。 

* **FCM 配置** 
   
    1. 确保从 Firebase 获取的*服务器密钥*与在 Azure 门户中注册的服务器密钥匹配。
   
    ![Firebase 服务器密钥][3]
   
    2. 确保在客户端上配置“项目 ID”。 可以从 Firebase 仪表板获取“项目 ID”的值。
   
    ![Firebase 项目 ID][1]

## <a name="application-issues"></a>应用程序问题
* **标记和标记表达式**

    如果使用标记或标记表达式来细分受众，那么，发送通知时，根据在发送调用中指定的标记或标记表达式，可能找不到任何目标。 
    
    发送通知时，请查看注册，确保有匹配的标记。 然后，确保仅从含有这些注册的客户端收到通知。 
    
    例如，如果使用“政治”标记向通知中心注册所有设备，但使用“体育”标记发送通知，那么通知将不会发送到任何设备。 复杂的用例可能涉及标记表达式：使用“标记 A”OR“标记 B”进行注册，但在发送通知时，定位的是“标记 A && 标记 B”。 在本文后面的自我诊断提示部分中，将介绍如何查看注册及其标记。 

* **模板问题**

    如果使用模板，请确保遵循[模板]中所述的准则。 

* **注册无效**

    如果通知中心配置正确，所有标记或标记表达式的使用也正确，则会找到有效的目标。 应将通知发送到这些目标。 接着，通知中心会并行启动几个处理批次。 每个批次将消息发送到一组注册。 

    > [!NOTE]
    > 由于处理操作是并行执行的，因此不保证传递通知的顺序。 

    通知中心已针对“最多一次”消息传递模型进行优化。 我们尝试执行重复数据消除，从而不向设备传递一次以上的通知。 为了确保这一点，在将消息发送到推送通知服务之前，我们会检查注册并确保每个设备标识符仅发送一条消息。 

    在将每批消息发送到推送通知服务时（后者转而接受和验证注册），推送通知服务有可能在某批注册的一个或多个注册中检测到错误。 在这种情况下，推送通知服务会向通知中心返回错误，使推送通知过程停止。 推送通知服务会完全删除该批次。 对于使用 TCP 流协议的 APNS 也是如此。 

    我们已对“最多一次”传递做了优化。 但在这种情况下，出错的注册会从数据库中删除。 然后，我们针对该批次中的其他设备重试通知传递。

    若要获取有关注册的失败传递尝试的更多错误信息，可使用通知中心 REST API [按照消息遥测数据：获取通知消息遥测数据](https://msdn.microsoft.com/library/azure/mt608135.aspx)和 [PNS 反馈](https://msdn.microsoft.com/library/azure/mt705560.aspx)。 有关示例代码，请参阅[发送 REST 示例](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample)。

## <a name="push-notification-service-issues"></a>推送通知服务问题
平台推送通知服务收到通知消息之后，需负责将通知传递到设备。 此时，通知中心是不相关的，它不会控制何时将通知传递到设备或者是否将通知传递到设备。 

由于平台通知服务非常强大，这些通知往往在几秒内就能从推送通知服务到达设备。 如果推送通知服务进行限制，通知中心会应用指数回退策略。 如果推送通知服务在 30 分钟之内都无法访问，我们会实施一个策略，以宣布这些消息过期并永久删除它们。 

如果推送通知服务尝试传递通知，但设备处于脱机状态，则推送通知服务会短暂存储通知。 等设备可用时再将通知传递到设备。 

对于每个应用，仅存储一个最新通知。 如果在设备处于脱机状态时发送了多个通知，则每个新通知将导致前一个通知被放弃。 只保留最新通知的行为在 APNs 中被称为*合并通知*，在 FCM（它使用折叠密钥）中被称为*折叠通知*。 如果设备长时间处于脱机状态，则放弃为它存储的所有通知。 有关详细信息，请参阅 [APNs overview]（APN 概述）和 [About FCM messages]（关于 FCM 消息）。

在 Azure 通知中心，可以使用泛型 SendNotification API 通过 HTTP 标头来传递合并密钥。 例如，对于 .NET SDK，你会使用 **SendNotificationAsync**。 SendNotification API 还会将按原样传递的 HTTP 标头传递到各自的推送通知服务。 

## <a name="self-diagnosis-tips"></a>自我诊断提示
下面介绍了诊断通知中心已删除通知的根本原因的各种途径：

### <a name="verify-credentials"></a>验证凭据
* **推送通知服务开发人员门户**
   
    在各自的推送通知服务开发人员门户（APNs、FCM、Windows 通知服务等）中验证凭据。 有关详细信息，请参阅 [Azure 通知中心入门]。

* **Azure 门户**
   
    若要查看凭据并将凭据与从推送通知服务开发人员门户获取的凭据进行匹配，请在 Azure 门户中转到“访问策略”选项卡。 
   
    ![Azure 门户访问策略][4]

### <a name="verify-registrations"></a>验证注册
* **Visual Studio**
   
    如果使用 Visual Studio 进行开发，则可以通过服务器资源管理器连接到 Azure，以查看和管理包括通知中心在内的多个 Azure 服务。 这主用于开发/测试环境。 
   
    ![Visual Studio 服务器资源管理器][9]
   
    可查看和管理中心的所有注册，这些注册已经按照平台、本机或模板注册、任意标记、推送通知服务标识符、注册 ID 以及过期日期进行分类。 还可以在此页面中编辑注册。 这对于编辑标记特别有用。 
   
    ![Visual Studio 设备注册][8]
   
   > [!NOTE]
   > 用于编辑注册的 Visual Studio 功能只能在开发/测试有限的注册时使用。 如果需要批量编辑注册，可以考虑使用[批量导出和修改注册](https://msdn.microsoft.com/library/dn790624.aspx)中所述的导出和导入注册功能。
   > 
   > 
* **服务总线资源管理器**
   
    许多客户使用[服务总线资源管理器]来查看和管理自己的通知中心。 服务总线资源管理器是一个开源项目。 有关示例，请参阅[服务总线资源管理器代码]。

### <a name="verify-message-notifications"></a>验证消息通知
* **Azure 门户**
   
    若要向客户端发送测试通知，而不启动和运行服务后端，请在“支持 + 故障排除”下选择“测试发送”。 
   
    ![Azure 中的测试发送功能][7]
* **Visual Studio**
   
    也可以从 Visual Studio 发送测试通知。
   
    ![Visual Studio 中的测试发送功能][10]
   
    有关将通知中心与 Visual Studio 服务器资源管理器搭配使用的详细信息，请参阅以下文章： 
   
   * [查看通知中心的设备注册]
   * [深入了解：Visual Studio 2013 Update 2 RC 和 Azure SDK 2.3]
   * [宣布推出 Visual Studio 2013 Update 3 和 Azure SDK 2.4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>调试失败的通知和查看通知结果
**EnableTestSend 属性**

通过通知中心发送通知时，通知最初在通知中心排队等待处理。 通知中心确定正确的目标后，便将通知发送到推送通知服务。 如果使用 REST API 或任意客户端 SDK，发送调用的成功返回只表示消息已成功在通知中心排队。 当通知中心最终将消息发送到推送通知服务时，你无法了解到底发生了什么。 

如果通知没有到达客户端设备，则可能在通知中心尝试将消息传递到推送通知服务时出现了错误。 例如，有效负载大小可能超出了推送通知服务允许的上限，或者在通知中心配置的凭据可能无效。 

若要深入了解推送通知服务错误，可以使用 [EnableTestSend] 属性。 从门户或 Visual Studio 客户端发送测试消息时，系统会自动启用此属性。 可以使用此属性查看详细的调试信息。 也可以通过 API 使用此属性。 目前可在 .NET SDK 中使用此属性。 最终，它将添加到所有客户端 SDK 中。 

若要和 REST 调用一起使用 **EnableTestSend** 属性，可在发送调用的末尾追加名为 *test* 的查询字符串参数。 例如： 

    https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test

**示例 (.NET SDK)**

下面是使用 .NET SDK 发送本机弹出 (toast) 通知的示例：

```csharp
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
    var result = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(result.State);
```

执行结束时，**result.State** 只声明 **Enqueued**。 结果未深入分析推送通知发生了什么。 

接下来，可以使用 **EnableTestSend** 布尔属性。 初始化 **NotificationHubClient** 时，可使用 **EnableTestSend** 属性获取有关发送通知时出现的推送通知服务错误的详细状态。 发送调用需要更多时间进行返回，因为它只在通知中心将通知传递到推送通知服务之后返回以确定结果。 

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

**示例输出**

    DetailedStateAvailable
    windows
    7619785862101227384-7840974832647865618-3
    The Token obtained from the Token Provider is wrong

此消息表示在通知中心配置的凭据无效，或中心的注册有问题。 建议删除此注册，让客户端重新创建注册后再发送消息。 

> [!NOTE]
> **EnableTestSend** 属性的使用受到极大限制。 此选项只能在开发/测试环境中与一组有限的注册结合使用。 我们仅向 10 台设备发送调试通知。 此外，我们每分钟向 10 台设备发送有限的处理调试。 
> 
> 

### <a name="review-telemetry"></a>查看遥测
* **使用 Azure 门户**
   
    在该门户中，可以快速了解通知中心的所有活动。 
   
    1. 在“概述”选项卡上，可以查看每个平台的注册、通知和错误的汇总视图。 
   
        ![通知中心概述仪表板][5]
   
    2. 在“监视器”选项卡上，可以添加许多其他平台特定指标，以便进行深入了解。 可以查看在通知中心尝试将通知发送给推送通知服务时返回的所有推送通知服务特有错误。 
   
        ![Azure 门户活动日志][6]
   
    3. 首先查看“传入消息”、“注册操作”和“成功通知”。 然后转到每个平台选项卡查看特定于推送通知服务的错误。 
   
    4. 如果通知中心的身份验证设置不正确，则出现“PNS 身份验证错误”消息。 这表示要检查推送通知服务凭据。 

* **以编程方式访问**

    有关编程访问的详细信息，请参阅以下文章： 

    * [以编程方式遥测访问]  
    * [通过 API 遥测访问示例] 

    > [!NOTE]
    > 与遥测相关的多项功能（例如，导出和导入注册、通过 API 进行遥测访问）只能在标准服务层使用。 如果尝试从免费或基本服务层使用这些功能，那么在使用 SDK 时会收到异常消息，在从 REST API 直接使用这些功能时会收到 HTTP 403（已禁止）错误。 
    >
    >若要使用与遥测相关的功能，首先确保在 Azure 门户中使用标准服务层。  
> 
> 

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
[APNs overview]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html（APNs 概述）
[About FCM messages]: https://firebase.google.com/docs/cloud-messaging/concept-options（关于 FCM 消息）
[Export and modify registrations in bulk]: http://msdn.microsoft.com/library/dn790624.aspx
[服务总线资源管理器]: https://msdn.microsoft.com/library/dn530751.aspx#sb_explorer
[服务总线资源管理器代码]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[查看通知中心的设备注册]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx 
[深入了解：Visual Studio 2013 Update 2 RC 和 Azure SDK 2.3]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs 
[宣布推出 Visual Studio 2013 Update 3 和 Azure SDK 2.4]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/ 
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[以编程方式遥测访问]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[通过 API 遥测访问示例]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel

