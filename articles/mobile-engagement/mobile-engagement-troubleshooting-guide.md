---
title: "Azure Mobile Engagement 故障排除指南"
description: "Azure Mobile Engagement 故障排除指南"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 31134a29-a513-4e5e-b626-f6cf6fe04769
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 93b5e3f4892f974bf9df28955956136528470e03
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="azure-mobile-engagement---troubleshooting-guide"></a>Azure Mobile Engagement - 故障排除指南
## <a name="introduction"></a>介绍
以下故障排除指南将帮助用户了解一些常见问题的根本原因，并使用户能够自己进行故障排除。 

## <a name="general"></a>一般信息
一般来说，必须先达到以下要求：

1. 确保已完成[入门教程](mobile-engagement-windows-store-dotnet-get-started.md)中所述的关于集成的所有步骤
2. 使用的是最新版本的平台 SDK。 
3. 在实际设备和仿真程序上测试，因为某些问题只会在仿真程序中出现。 
4. 不会达到 Mobile Engagement 中的任何限制，[此处](../azure-subscription-service-limits.md)记录了这些限制
5. 如果无法连接到 Mobile Engagement 服务后端或看到数据未连续加载，请通过查看[此处](https://azure.microsoft.com/status/)来确保不存在任何正在进行的服务事件

## <a name="monitor-issues"></a>“监视器”问题
### <a name="i-am-not-seeing-my-device-showing-up-on-the-monitor-tab"></a>我没有在“监视器”选项卡上看到我的设备
“监视器”选项卡会实时显示连接到 Mobile Engagement 平台的设备。 如果在仿真程序和设备上调试，那么应该会在这里看到至少一个会话。 如果应用已分发，则会看到活动会话仪表实时反映连接到平台的设备。 

如果没有在“监视器”选项卡上看到设备，则很可能是 SDK 集成问题。 故障排除的一些常见步骤如下：

1. 请确保在移动应用中使用了正确的连接字符串，并且该字符串来自 SDK 密钥部分，而不是 API 密钥部分。 连接字符串会将移动应用连接到 Mobile Engagement 应用的实例，可在“监视器”选项卡上看到设备。 
2. 对于 Windows 平台 - 如果页面重写了 `OnNavigatedTo` 方法，请务必调用 `base.OnNavigatedTo(e)`。
3. 如果要将 Mobile Engagement 集成到现有的移动应用中，则还可以通过查看[此处](mobile-engagement-windows-store-integrate-engagement.md)的高级集成步骤，确保没有遗漏任何步骤
4. 根据[入门教程](mobile-engagement-windows-store-dotnet-get-started.md)中描述的平台，确保通过使用 EngagementActivity 重写页面至少发送一个屏幕/活动。

### <a name="i-am-seeing-the-monitor-tab-showing-a-session-even-when-i-have-disconnected-or-closed-my-app-emulator"></a>即使我已断开或关闭我的应用/仿真程序，我还是看到“监视器”选项卡显示会话。
如果你是唯一一个连接到该平台在此时并且使用仿真程序中打开应用这是有可能是因为仿真程序 quirks。 一般情况下，需要确保返回到仿真程序上的主屏幕，以便应用会话成功断开连接。 此外，在 Windows 平台上，在使用 Visual Studio 进行调试时，可能需要确保在 Visual Studio 中，转到“**生命周期事件**”菜单栏，并单击“**挂起**”才能真正关闭会话。 有关详细信息，请参阅 [Windows 教程](mobile-engagement-windows-store-dotnet-get-started.md)。 

## <a name="analytics-issues"></a>“分析”问题
### <a name="i-am-not-seeing-any-data-refreshed-data-on-analytics-tab"></a>我在“分析”选项卡上看不到任何数据/刷新的数据
分析数据是定期重新计算的，刷新可能需要最多 24 小时。 此数据不会实时更新，但 24 小时内会刷新。
但是，请确保通过使用 `EngagementActivity` 重写至少一个页面或显式调用 `SendActivity` 来向平台后端发送至少一个屏幕或“活动”。 

### <a name="i-am-seeing-incorrectly-captured-datetime-for-a-device-on-the-analytics-tab"></a>我在“分析”选项卡上看到错误捕获的设备日期/时间
分析的时间段取决于用户的设备设置的日期。 因此，请确保设备的日期设置正确。 

## <a name="segment-issues"></a>“分段”问题
### <a name="i-created-a-segment-and-it-is-showing-up-as-greyed-out-or-not-showing-any-data"></a>我创建了一个分段，它显示为灰色或不显示任何数据
分段创建目前不是实时的。 分段的计算与分析数据的聚合同时进行，因此可能最多需要 24 小时。 应稍后再回来进行查看，但同时还应该确保移动应用确实在发送形成分段所基于的数据。 例如 如果带有“foo”的事件没有被任何移动设备发送，则以 EventName = foo 作为条件创建的分段将不包含任何分段数据。 还应检查 SDK 集成，以确保移动应用正确地发送数据。 

## <a name="reach-or-push-notifications-issues"></a>“市场宣传”或推送通知问题
### <a name="my-push-messages-are-not-being-delivered"></a>我的推送消息未传递
1. 请首先尝试向测试设备发送通知，以确保所有组件（移动应用、SDK 和服务）正确连接，并能够传递推送通知。 
2. 始终先通过市场活动发送最简单的“应用外通知”，此类通知无需预定并且没有指定的受众条件。 这也是为了证明通知连接正常工作。 
3. 如果在传递应用内通知时遇到问题，那么先尝试发送应用外通知也是一个不错的开始。 
4. 确保为移动应用正确配置了“原生推送”。 根据平台的不同，它将涉及密钥（Android、Windows）或证书 (iOS)。 请参阅[用户界面 - 设置](mobile-engagement-user-interface-settings.md)
5. 用户还可以通过移动 OS 阻止应用外通知，因此请确保此问题不属于这种情况。 
6. 确保未在市场宣传活动的“**市场活动**”部分中设置“*忽略受众，推送将通过 API 发送给用户*”选项，因为这样可确保只能通过 API 发送推送通知。 
7. 确保同时使用通过 WIFI 和电话运营商网络连接的设备测试推送市场活动，以消除网络连接成为问题来源的可能性。
8. 确保设备/仿真程序上的系统日期/时间正确，因为任何不同步设备也会干扰推送通知服务传递通知的能力。 

更多平台特定故障排除说明如下：

1. **iOS** 
   
   * 对于 iOS 推送通知，请确保证书有效且未过期。 
   * 确保在 Mobile Engagement 应用中正确配置*生产*证书。 
   * 确保在*真实的物理设备*上进行测试 iOS 仿真程序无法处理推送消息。
   * 确保在移动应用中正确配置了捆绑标识符。 请参阅[此处](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)的说明
   * 测试时，在移动预配配置文件中使用“临时”分发。 如果应用使用“调试”进行编译，则无法接收通知
2. **Android**
   
   * 确保在移动应用的 AndroidManifest.xml 文件中指定了正确的项目编号，并且后面紧跟 \n 字符。 
     
           <meta-data android:name="engagement:gcm:sender" android:value="************\n" />
   * 确保未遗漏或错误配置 Android 清单文件中的任何权限 
   * 确保要添加到客户端应用的项目编号来自获取 GCM 服务器密钥的同一帐户。 两者之间的任何不匹配都将阻止推送的传出。 
   * 如果接收到系统通知，但不是应用内通知，请检查[指定通知图标部分](mobile-engagement-android-get-started.md)，因为很有可能没有在 Android 清单文件中指定正确的图标。 
   * 如果要发送 BigPicture 通知，请确保在具有外部图像服务器的情况下，这些服务器需要能够支持 HTTP“GET”和“HEAD”。
3. **Windows**
   
   * 确保已将该应用与有效的 Windows 应用商店应用相关联。 在 Visual Studio 中 - 必须右键单击项目并选择“关联应用与应用商店”选项，并选择在 Windows 应用商店中创建的应用。 此 Windows 应用商店应用应与在 Mobile Engagement 门户中获取要配置的原生推送凭据的应用相同。
   * 如果在使用 `EngagementOverlay` 集成时收到应用外推送通知，但未收到应用内通知，则需要确保页面中存在根网格元素。 Engagement Overlay 使用它在 xaml 文件中找到的第一个“网格”元素来在页面上添加两个 Web 视图。 如果想要定位将设置 Web 视图的位置，可以像这样定义一个名为“EngagementGrid”的网格，但是必须确保能够为两个后续 Web 视图提供足够的高度和宽度，这两个视图以应用内通知形式显示通知和以下公告：
     
           <Grid x:Name="EngagementGrid"></Grid>

### <a name="i-created-a-push-notificationannouncement-campaign-and-even-after-it-sent-me-the-notification-it-is-showing-as-active-what-does-it-mean"></a>我创建了推送通知/公告/市场活动，即便在向我发送通知后，它仍然显示为“活动”。 它意味着什么？
在 Mobile Engagement 中创建的**市场活动**之所以获得这样的名称，是因为它是一个长时间运行的推送通知，这意味着只要新设备满足在市场活动中设置的条件，当它们连接到 Mobile Engagement 平台时，系统就会自动向它们发送在此处配置的通知。 这不是一个一次性的通知设置。 必须手动单击“**完成**”按钮来终止市场活动，以使其不再发送通知。 

### <a name="i-created-a-push-campaign-and-i-am-receiving-notifications-successfully-however-whenever-i-open-up-the-app-i-get-the-same-notification-even-when-i-had-actioned-it-before"></a>我创建了推送活动并且成功收到了通知，但是每当我打开应用时，我就会收到相同的通知，即使我以前已对其执行了操作？
如果使用仿真程序或者某些测试框架（如 TestFlight），就可能在测试期间出现这种情况。 出现这种情况的原因是，每当应用运行实例时，它就会获取一个新的 DeviceID并将其发送到我们的后端，这会导致 Mobile Engagement 平台将其视为新设备并发送通知。 

## <a name="getting-support"></a>获取支持
如果无法自行解决问题，则可以：

1. 在 StackOverflow 论坛和 [MSDN 论坛](https://social.msdn.microsoft.com/Forums/windows/en-US/home?forum=azuremobileengagement)中的现有会话中搜索问题，如果没有，可在那里提出问题。 
2. 如果发现缺少某个功能，请在我们的 [UserVoice 论坛](https://feedback.azure.com/forums/285737-mobile-engagement/)上添加请求/为请求投票
3. 如果提供以下详细信息，则可以让 Microsoft 支持打开一个支持事件： 
   * Azure 订阅 ID
   * 平台（如 iOS、Android 等）
   * 应用 ID
   * 市场活动 ID（适用于推送通知问题）
   * 设备 ID
   * Mobile Engagement SDK 版本（例如 Android SDK v2.1.0）
   * 包含具体错误消息和方案的错误详细信息

