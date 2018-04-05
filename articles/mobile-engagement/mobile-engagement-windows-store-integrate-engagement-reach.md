---
title: Windows Universal Apps Reach SDK 集成
description: 如何将 Azure Mobile Engagement Reach 与 Windows Universal 应用集成
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a31ca1d6-856f-4aec-898a-07969ae5f7ec
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 8472af2ac72a1976871970963851be775b19859b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2018
---
# <a name="windows-universal-apps-reach-sdk-integration"></a>Windows Universal Apps Reach SDK 集成
> [!IMPORTANT]
> Azure Mobile Engagement 已在 2018 年 3 月 31 日停用。 此页将在不久之后删除。
> 

在按本指南操作之前，必须完成 [Windows Universal Engagement SDK 集成](mobile-engagement-windows-store-integrate-engagement.md)中所述的集成程序。

## <a name="embed-the-engagement-reach-sdk-into-your-windows-universal-project"></a>在 Windows Universal 项目中嵌入 Engagement Reach SDK
无需添加任何内容。 `EngagementReach` 参考和资源已在项目中。

> [!TIP]
> 可以自定义项目文件夹 `Resources` 中的图像，尤其是品牌图标（默认为 Engagement 图标）。 在 Universal 应用中，还可以移动共享项目中的 `Resources` 文件夹以便在应用之间共享其内容，但由于文件依赖于平台，因此必须将 `Resources\EngagementConfiguration.xml` 文件保存在其默认位置。
> 
> 

## <a name="enable-the-windows-notification-service"></a>启用 Windows 通知服务
### <a name="windows-8x-and-windows-phone-81-only"></a>仅限于 Windows 8.x 和 Windows Phone 8.1
若要使用位于 `Application UI` 的 `Package.appxmanifest` 文件中的 **Windows 通知服务**（也称为 WNS），请单击左侧 bot 框中的 `All Image Assets`。 在 `Notifications` 中的框右侧，将 `toast capable` 从 `(not set)` 更改为 `(Yes)`。

### <a name="all-platforms"></a>所有平台
需要将应用同步到 Microsoft 帐户和 Engagement 平台。 为此，需要创建一个帐户或登录 [Windows 开发人员中心](https://dev.windows.com)。 在此之后，创建一个新的应用程序并找到 SID 及密钥。 在 Engagement 前端，转至 `native push` 中的应用设置并粘贴凭据。 随后右键单击项目，依次选择 `store` 和 `Associate App with the Store...`。 只需选择之前创建的应用程序以便将其同步。

## <a name="initialize-the-engagement-reach-sdk"></a>初始化 Engagement Reach SDK
修改 `App.xaml.cs`：

* 在 `InitEngagement` 方法中 `EngagementAgent.Instance.Init` 之后插入`EngagementReach.Instance.Init`：
  
      private void InitEngagement(IActivatedEventArgs e)
      {
        EngagementAgent.Instance.Init(e);
        EngagementReach.Instance.Init(e);
      }
  
  `EngagementReach.Instance.Init` 会在专用的线程中运行。 无需亲自执行此操作。

> [!NOTE]
> 如果要在应用程序中的其他地方使用推送通知，则必须与 Engagement Reach [共享推送通道](#push-channel-sharing)。
> 
> 

## <a name="integration"></a>集成
Engagement 提供了两种方法为应用程序中的公告和轮询添加 Reach 应用内横幅和插播式视图：覆盖集成和 Web 视图手动集成。 不应该在同一页上组合使用这两种方法。

两种集成之间的选择可归纳为以下方式：

* 如果页面已从代理 `EngagementPage` 继承了集成，可以选择覆盖集成，也就是将页面中的 `EngagementPage` 替换为 `EngagementPageOverlay`，将 `xmlns:engagement="using:Microsoft.Azure.Engagement"` 替换为 `xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"`。
* 如果想要精确地将 Reach UI 放置在页面内，或者不想向页面中添加另一个继承级别，可以选择 Web 视图手动集成。 

### <a name="overlay-integration"></a>覆盖集成
Engagement 覆盖会动态添加用于在页面中显示 Reach 营销活动的 UI 元素。 如果覆盖不适合布局，则应考虑 Web 视图手动集成。

在 .xaml 文件中，将 `EngagementPage` 引用更改为 `EngagementPageOverlay`

* 添加到命名空间声明：
  
      xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"
* 将 `engagement:EngagementPage` 替换为 `engagement:EngagementPageOverlay`：

**有 EngagementPage：**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">

            <!-- Your layout -->
        </engagement:EngagementPage>

**有 EngagementPageOverlay：**

        <engagement:EngagementPageOverlay 
            xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">

            <!-- Your layout -->
        </engagement:EngagementPageOverlay>

然后在.cs 文件中，在 `EngagementPageOverlay` 而不是 `EngagementPage` 中标记页面，然后导入 `Microsoft.Azure.Engagement.Overlay`。

            using Microsoft.Azure.Engagement.Overlay;

* 将 `EngagementPage` 替换为 `EngagementPageOverlay`：

**有 EngagementPage：**

            using Microsoft.Azure.Engagement;

            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPage
              {
                [...]
              }
            }

**有 EngagementPageOverlay：**

            using Microsoft.Azure.Engagement.Overlay;

            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPageOverlay 
              {
                [...]
              }
            }


Engagement 覆盖会在由布局和两个 `WebView` 元素组成的页面顶部添加一个 `Grid` 元素，这两个元素一个用于横幅，另一个用于插播式视图。

可以直接在 `EngagementPageOverlay.cs` 文件中自定义覆盖元素。

### <a name="web-views-manual-integration"></a>Web 视图手动集成
Reach 会搜索页面，查找负责显示横幅和插播式视图的两个 `WebView` 元素。 唯一需要做的就是将这两个 `WebView` 元素添加到页面中的某处，如下例所示：

    <Grid x:Name="engagementGrid">

      <!-- Your layout -->

      <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Stretch" VerticalAlignment="Top"/>
      <WebView x:Name="engagement_announcement_content" Visibility="Collapsed"  HorizontalAlignment="Stretch"  VerticalAlignment="Stretch"/>
    </Grid>


在此例中，`WebView` 元素被拉伸以适合其容器，如果屏幕旋转或窗口大小发生变化，容器会自动调整其大小。

> [!WARNING]
> 务必为 `WebView` 元素保留相同命名的 `engagement_notification_content` 和 `engagement_announcement_content`。 Reach 通过元素名称来识别元素。 
> 
> 

## <a name="handle-datapush-optional"></a>句柄 datapush（可选）
如果希望应用程序能够接收 Reach 数据推送，则必须实现 EngagementReach 类的两个事件：

在 App.xaml.cs 的 App() 构造函数中添加：

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
我们允许用户自定义 Reach UI。

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

然后，使用 `App()` 方法中 `App.xaml.cs` 类的自定义对象，设置 `EngagementReach.Instance.Handler` 字段的内容。

**示例代码：**

            protected override void OnLaunched(LaunchActivatedEventArgs args)
            {
              // your app initialization 
              EngagementReach.Instance.Handler = new ExampleReachHandler();
              // Engagement Agent and Reach initialization
            }

> [!NOTE]
> 默认情况下，Engagement 会利用其自己的 `EngagementReachHandler` 的实现。
> 不必创建自己的方法，即使创建了，也无需覆盖每个方法。 默认行为是选择 Engagement 基对象。
> 
> 

### <a name="web-view"></a>Web 视图
默认情况下，Reach 会使用 DLL 的嵌入资源来显示通知和页面。

若要提供完全自定义的可能性，我们只需使用 Web 视图。 如果想要自定义布局，直接覆盖资源文件 `EngagementAnnouncement.html` 和 `EngagementNotification.html` 即可。 Engagement 需要 `<body></body>` 中的所有代码才能正确运行。 但是可以在 `engagement_webview_area` 外面添加标记。

不过可以决定使用自己的资源。

可以覆盖子类中的 `EngagementReachHandler` 方法来告诉 Engagement 使用布局，但请注意嵌入 Engagement 机制：

**示例代码：**

            // In your subclass of EngagementReachHandler

            public override string GetAnnouncementHTML()
            {
              return base.GetAnnouncementHTML();
            }
            public override string GetAnnouncementName()
            {
              return base.GetAnnouncementName();
            }
            public override string GetNotfificationHTML()
            {
              return base.GetNotfificationHTML();
            }
            public override string GetNotfificationName()
            {
              return base.GetNotfificationName();
            }


默认情况下，AnnouncementHTML 为 `ms-appx-web:///Resources/EngagementAnnouncement.html`。 它代表用于设计推送消息内容（文本公告、Web 公告和轮询公告）的 html 文件 。 AnnouncementName 为 `engagement_announcement_content`。 它是 xaml 页面中 webview 设计的名称。

NotfificationHTML 为 `ms-appx-web:///Resources/EngagementNotification.html`。 它代表用于设计推送消息通知的 html 文件。 NotfificationName 为 `engagement_notification_content`。 它是 xaml 页面中 webview 设计的名称。

### <a name="customization"></a>自定义
如果保留了 Engagement 对象，可以按照需要自定义通知和公告 Web 视图。 注意描述了三次 webview 对象：第一次在 xaml 中，第二次在 .cs 文件的“setwebview()”方法中，第三次在 html 文件中。

* 在 xaml 中描述当前图形布局 webview 组件。
* 在 .cs 文件中，可以定义 "setwebview()" 并在其中设置两个 webview 的维度（通知和公告）。 在应用程序调整大小时，这会非常有效。
* 在 Engagement html 文件中，我们描述了 webview 内容、设计和元素在彼此之间的位置。

### <a name="launch-message"></a>启动消息
用户单击系统通知（Toast 通知）时，Engagement 会启动应用程序，加载推送消息的内容，并显示相应营销活动的页面。

应用程序启动和页面显示之间会有延迟（具体取决于网络速度）。

若要告诉用户某个内容正在加载，则应提供可视化信息，如进度栏或进度指示器。 Engagement 无法自行处理这种情况，但提供了几个句柄。

若要实现回调，在 App.xaml.cs 的 "Public App(){}" 中添加：

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

可以在 `App.xaml.cs` 文件的 "Public App(){}" 方法中设置回调，最好在 `EngagementReach.Instance.Init()` 调用之前进行。

> [!TIP]
> 每个句柄由 UI 线程调用。 在使用 MessageBox 或 UI 相关的内容时不需要担心这一点。
> 
> 

## <a id="push-channel-sharing"></a>推送通道共享
如果因为其他目的要在应用程序中使用推送通知，则必须使用 Engagement SDK 的推送通道共享功能。 这是为了避免丢失的推送。

* 可以将自己的推送通道提供给 Engagement Reach 初始化。 SDK 将使用通道，而不会请求新的通道。

在 `InitEngagement` 文件的 `App.xaml.cs` 方法中，使用推送通道更新 Engagement Reach 初始化：

    /* Your own push channel logic... */
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    /*...Engagement initialization */
    EngagementAgent.Instance.Init(e);
    EngagementReach.Instance.Init(e,pushChannel);

* 或者，如果只是想在 Reach 初始化之后使用推送通道，可以针对 Engagement Reach 设置一个回调，以便在 SDK 创建完通道后获取该通道。

Reach 初始化**之后**，在任意位置设置回调：

    /* Set action on the SDK push channel. */
    EngagementReach.Instance.SetActionOnPushChannel((PushNotificationChannel channel) => 
    {
      /* The forwarded channel can be null if its creation fails for any reason. */
      if (channel != null)
      {
        /* Your own push channel logic... */
      });
    }

## <a name="custom-scheme-tip"></a>自定义方案提示
我们提供了自定义方案可供使用。 可以从 Engagement 前端发送将要在 Engagement 应用程序中使用的不同类型的 URI。 默认方案（如 `http, ftp, ...`）由 Windows 管理，如果设备上没有安装默认应用程序，会弹出一个窗口提示。 还可以为应用程序创建自己的自定义方案。

在应用程序中设置自定义方案的一个简单方法就是打开 `Package.appxmanifest`，进入“`Declarations`”面板。 在“可用声明”滚动框中选择“`Protocol`”并进行添加。 使用新协议所需的名称编辑“`Name`”字段。

现在如果要使用此协议，请使用 `App.xaml.cs` 方法编辑 `OnActivated`，不要忘记还应在此处初始化 Engagement：

            /// <summary>
            /// Enter point when app his called by another way than user click
            /// </summary>
            /// <param name="args">Activation args</param>
            protected override void OnActivated(IActivatedEventArgs args)
            {
              /* Init engagement like it was launch by a custom uri scheme */
              EngagementAgent.Instance.Init(args);
              EngagementReach.Instance.Init(args);

              //TODO design action to do when app is launch

              #region Custom scheme use
              if (args.Kind == ActivationKind.Protocol)
              {
                ProtocolActivatedEventArgs myProtocol = (ProtocolActivatedEventArgs)args;

                if (myProtocol.Uri.Scheme.Equals("protocolName"))
                {
                  string path = myProtocol.Uri.AbsolutePath;
                }
              }
              #endregion

