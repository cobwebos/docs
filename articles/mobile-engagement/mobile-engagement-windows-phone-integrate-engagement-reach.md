---
title: "Windows Phone Silverlight Reach SDK 集成"
description: "如何将 Azure Mobile Reach Engagement 与 Windows Phone Silverlight 应用集成"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: d3516a6b-db9f-4cdb-a475-4148edf81af1
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 0738f33df94d14fbb393bfaaf09e94c6560213cc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="windows-phone-silverlight-reach-sdk-integration"></a>Windows Phone Silverlight Reach SDK 集成
在按照本指南操作之前，必须完成 [Windows Phone Silverlight Engagement SDK 集成](mobile-engagement-windows-phone-integrate-engagement.md)中所述的集成过程。

## <a name="embed-the-engagement-reach-sdk-into-your-windows-phone-silverlight-project"></a>将 Engagement Reach SDK 嵌入到 Windows Phone Silverlight 项目
无需添加任何内容。 `EngagementReach` 参考和资源已在项目中。

> [!TIP]
> 可以自定义项目文件夹 `Resources` 中的图像，尤其是品牌图标（默认为 Engagement 图标）。
> 
> 

## <a name="add-the-capabilities"></a>新增功能
Engagement Reach SDK 需要一些额外功能。

打开 `WMAppManifest.xml` 文件，确保其中明确列出了以下功能：

* `ID_CAP_PUSH_NOTIFICATION`
* `ID_CAP_WEBBROWSERCOMPONENT`

第一个是 MPNS 服务用于允许显示 toast 通知。 第二个是用于将浏览器任务嵌入到 SDK 中的功能。

编辑 `WMAppManifest.xml` 文件，并在 `<Capabilities />` 标记内添加：

    <Capability Name="ID_CAP_PUSH_NOTIFICATION" />
    <Capability Name="ID_CAP_WEBBROWSERCOMPONENT" />

## <a name="enable-the-microsoft-push-notification-service"></a>启用 Microsoft 推送通知服务
要使用 **Microsoft 推送通知服务**（简称为 MPNS），`WMAppManifest.xml` 文件必须具有 `<App />` 标记，且将 `Publisher` 属性设置为项目的名称。

## <a name="initialize-the-engagement-reach-sdk"></a>初始化 Engagement Reach SDK
### <a name="engagement-configuration"></a>Engagement 配置
Engagement 配置在项目的 `Resources\EngagementConfiguration.xml` 文件中集中管理。

编辑此文件以指定市场宣传配置：

* *可选*，在 `<enableNativePush>` 和 `</enableNativePush>` 标记之间指示是否激活原生推送 (MPNS) 的设置（默认值为 `true`）。
* *可选*，在 `<channelName>` 和 `</channelName>` 标记之间指示推送通道的名称，提供应用程序当前可使用的相同名称或留空。

如果想在运行时指定连接字符串，则可以在 Engagement 代理初始化之前调用以下方法：

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    engagementConfiguration.Reach.EnableNativePush = true;                  
    /* [Optional] whether the native push (MPNS) is activated or not. */

    engagementConfiguration.Reach.ChannelName = "YOUR_PUSH_CHANNEL_NAME";   
    /* [Optional] Provide the same channel name that your application may currently use. */

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

> [!TIP]
> 可以指定应用程序的 MPNS 推送通道的名称。 默认情况下，Engagement 基于 appId 创建名称。 无需自行指定名称，除非打算在 Engagement 外部使用该推送通道。
> 
> 

### <a name="engagement-initialization"></a>Engagement 初始化
修改 `App.xaml.cs`：

* 添加到 `using` 语句：
  
      using Microsoft.Azure.Engagement;
* 在 `Application_Launching` 中，将 `EngagementAgent.Instance.Init` 插入 `EngagementReach.Instance.Init` 之后：
  
      private void Application_Launching(object sender, LaunchingEventArgs e)
      {
         EngagementAgent.Instance.Init();
         EngagementReach.Instance.Init();
      }
* 在 `Application_Activated` 方法中插入 `EngagementReach.Instance.OnActivated`：
  
      private void Application_Activated(object sender, ActivatedEventArgs e)
      {
         EngagementAgent.Instance.OnActivated(e);
         EngagementReach.Instance.OnActivated(e);
      }

> [!IMPORTANT]
> `EngagementReach.Instance.Init` 会在专用的线程中运行。 不需要你亲自执行。
> 
> 

## <a name="app-store-submission-considerations"></a>应用商店提交注意事项
Microsoft 对于推送通知的使用有一些规定：

摘自 Microsoft [应用程序策略]文档的第 2.9 节中：

1) 必须询问用户是否要接收推送通知。 然后，在设置添加禁用推送通知的方法。

EngagementReach 对象提供两种方法来管理选择加入/选择退出、`EnableNativePush()` 和 `DisableNativePush()`。 例如，可以在设置中创建一个有切换功能的选项，以禁用或启用 MPNS。

也可以决定通过 Engagement 配置 \<windows phone-sdk-reach-configuration\> 来停用 MPNS。

> 2.9.1) 应用程序必须首先描述要提供的通知，并**获取用户的明确许可（选择加入）**，并且**必须提供用户可以选择不接收推送通知的机制**。 使用 Microsoft 推送通知服务提供的所有通知都必须与提供给用户的说明一致，并且必须遵守所有适用的[应用程序策略][Content Policies]和[特定应用程序类型的其他要求]。
> 
> 

2) 不应使用太多的推送通知。 Engagement 将处理通知。

> 2.9.2) 应用程序及其对 Microsoft 推送通知服务的使用，不得过度使用 Microsoft 推送通知服务的网络容量和带宽，或者过多的推送通知会对 Windows Phone 或其他 Microsoft 设备或服务造成大量的负载（由 Microsoft 以其合理的自由裁量权决定），而且不得损害或干扰任何 Microsoft 网络或服务器，或任何第三方服务器，或连接到 Microsoft 推送通知服务的网络。
> 
> 

3) 请不要依赖 MPNS 发送重要信息。 Engagement 使用 MPNS，因此，此规则也适用于在 Engagement 前端内部创建的市场活动。

> 2.9.3) Microsoft 推送通知服务不能用于发送紧急或生死攸关的通知，包括但不限于与医疗设备或条件相关的重要通知。 MICROSOFT 明确声明对于 MICROSOFT 推送通知服务服务或 MICROSOFT 推送通知服务的传送，不提供不会有任何中断、错误，或实时运行的任何担保。
> 
> 

**如果不遵守这些建议，我们无法保证应用程序将通过验证过程。**

## <a name="handle-data-push-optional"></a>处理数据推送（可选）
如果希望应用程序能够接收 Reach 数据推送，则必须实现 EngagementReach 类的两个事件：

    EngagementReach.Instance.DataPushStringReceived += (body) =>
    {
       Debug.WriteLine("String data push message received: " + body);
       return true;
    };

    EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
    {
       Debug.WriteLine("Base64 data push message received: " + encodedBody);
       // Do something useful with decodedBody like updating an image view
       return true;
    };

可以看到每个方法的回调都返回一个布尔值。 Engagement 会在分发数据推送之后将反馈发送到其后端。 如果回调返回 false，则发送 `exit` 反馈。 否则，会是 `action`。 如果没有为事件设置回调，则 `drop` 反馈会被返回到 Engagement。

> [!WARNING]
> Engagement 不能接收一个数据推送的多个反馈。 如果打算在一个事件上设置多个句柄，请注意反馈将对应最后发送的那个。 在这种情况下，我们建议始终返回同一值，以避免在前端有令人困惑的反馈。
> 
> 

## <a name="customize-ui-optional"></a>自定义 UI（可选）
### <a name="first-step"></a>第一步
我们允许你自定义 Reach UI。

若要执行此操作，必须创建 `EngagementReachHandler` 类的一个子类。

**示例代码：**

    using Microsoft.Azure.Engagement;

    namespace Example
    {
       internal class ExampleReachHandler : EngagementReachHandler
       {
          // Override EngagementReachHandler methods depending on your needs
       }
    }

然后，使用 `Application_Launching` 方法中 `App.xaml.cs` 类的自定义对象，设置 `EngagementReach.Instance.Handler` 字段的内容。

**示例代码：**

    private void Application_Launching(object sender, LaunchingEventArgs e)
    {
       // your app initialization 
       EngagementReach.Instance.Handler = new ExampleReachHandler();
       // Engagement Agent and Reach initialization
    }

> [!NOTE]
> 默认情况下，Engagement 会利用其自己的 `EngagementReachHandler` 的实现。 不必创建自己的方法，即使创建了，也无需覆盖每个方法。 默认行为是选择 Engagement 基对象。
> 
> 

### <a name="layouts"></a>布局
默认情况下，Reach 会使用 DLL 的嵌入资源来显示通知和页面。

但是，可以在这些组件中决定使用自己的资源，以反映品牌。

可以在子类中覆盖 `EngagementReachHandler` 方法，以告诉 Engagement 使用布局：

**示例代码：**

    // In your subclass of EngagementReachHandler

    public override string GetTextViewAnnouncementUri()
    {
       // return the path of your own xaml
    }

    public override string GetWebViewAnnouncementUri()
    {
       // return the path of your own xaml
    }

    public override string GetPollUri()
    {
       // return the path of your own xaml
    }

    public override EngagementNotificationView CreateNotification(EngagementNotificationViewModel viewModel)
    {
       // return a new instance of your own notification
    }

> [!TIP]
> `CreateNotification` 方法可以返回 null。 通知将不会显示，并且市场宣传活动会被丢弃。
> 
> 

为了简化布局实现，我们还提供了我们自己 xaml，可用作代码基础。 这些文件位于 Engagement SDK 存档 (/src/reach/) 中。

> [!WARNING]
> 我们提供的源与我们所使用完全相同。 因此，如果想要直接对其进行修改，切记要更改命名空间和名称。
> 
> 

### <a name="notification-position"></a>通知的位置
默认情况下，应用内通知会显示在应用程序的左下角。 可以通过重写 `EngagementReachHandler` 对象的 `GetNotificationPosition` 方法更改此行为。

    // In your subclass of EngagementReachHandler

    public override EngagementReachHandler.NotificationPosition GetNotificationPosition(EngagementNotificationViewModel viewModel)
    {
       // return a value of the EngagementReachHandler.NotificationPosition enum (TOP or BOTTOM)
    }

目前，可以选择 `BOTTOM`（默认值）和 `TOP` 之间的位置。

### <a name="launch-message"></a>启动消息
用户单击系统通知（toast 通知）时，Engagement 会启动应用，加载推送消息的内容，并显示相应市场活动的页面。

应用程序启动和页面显示之间会有延迟（具体取决于网络速度）。

若要告诉用户某个内容正在加载，则应提供可视化信息，如进度栏或进度指示器。 Engagement 无法自行处理这种情况，但提供了几个句柄。

若要实现回调，请执行以下操作：

    /* The application has launched and the content is loading.
     * You should display an indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };

    /* The application has finished loading the content and the page
     * is about to be displayed.
     * You should hide the indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };

    /* The content has been loaded, but an error has occurred.
     * You can provide an information to the user.
     * You should hide the indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

可以在 `App.xaml.cs` 文件的 `Application_Launching` 方法中设置回调，最好在 `EngagementReach.Instance.Init()` 调用之前进行。

> [!TIP]
> 每个句柄由 UI 线程调用。 在使用 MessageBox 或 UI 相关的内容时不需要担心这一点。
> 
> 

[应用程序策略]:http://msdn.microsoft.com/library/windows/apps/hh184841(v=vs.105).aspx
[Content Policies]:http://msdn.microsoft.com/library/windows/apps/hh184842(v=vs.105).aspx
[特定应用程序类型的其他要求]:http://msdn.microsoft.com/library/windows/apps/hh184838(v=vs.105).aspx

