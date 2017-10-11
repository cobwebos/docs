---
title: "Windows Universal 应用的 SDK 升级过程"
description: "Azure Mobile Engagement 的 Windows Universal 应用 SDK 升级过程"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 4c898175-2cd6-43db-b350-bb408332f24d
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: fe85a99a92fb39082cafe7422b356de1f20f14bd
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="windows-universal-apps-sdk-upgrade-procedures"></a>Windows Universal 应用的 SDK 升级过程
如果已将较旧版本的 Engagement 集成到应用程序中，则在升级 SDK 时必须考虑以下几点。

如果错过了几个版本的 SDK，则可能需要执行多个过程。 例如，如果你将从迁移 0.10.1 到你必须首先按照"发件人 0.10.1 到 0.9.0"步骤的 0.11.0 然后"发件人到 0.11.0 0.10.1"过程。

## <a name="from-330-to-340"></a>从 3.3.0 至 3.4.0
### <a name="test-logs"></a>测试日志
现在可以启用/禁用/筛选由 SDK 生成的控制台日志。 要对其自定义，请将属性 `EngagementAgent.Instance.TestLogEnabled` 更新为 `EngagementTestLogLevel` 枚举中提供的值之一，例如：

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="resources"></a>资源
市场宣传覆盖已得到改进。 它是 SDK NuGet 程序包资源的一部分。

升级到新版本的 SDK 时，可以选择是否要保留资源的覆盖文件夹中的现有文件：

* 如果上一个覆盖正在运行，或者正在手动集成 `WebView` 元素，然后可决定保留现有文件，则其仍将正常工作。 
* 如果想要更新到新的覆盖，只需将资源中的整个 `overlay` 文件夹替换为 SDK 包中的新文件夹（UWP 应用：升级完成后，可以从 %USERPROFILE%\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources 中获得新的覆盖文件夹）。

> [!WARNING]
> 使用新的覆盖将改写在之前版本上进行的任何自定义。
> 
> 

## <a name="from-320-to-330"></a>从 3.2.0 至 3.3.0
### <a name="resources"></a>资源
此步骤仅涉及自定义的资源。 如果已对 SDK 提供的资源（html、图像、覆盖）进行了自定义，则在已升级的资源上升级并重新应用自定义前，必须对其进行备份。

## <a name="from-310-to-320"></a>从 3.1.0 到 3.2.0
### <a name="resources"></a>资源
此步骤仅涉及自定义的资源。 如果已对 SDK 提供的资源（html、图像、覆盖）进行了自定义，则在已升级的资源上升级并重新应用自定义前，必须对其进行备份。

### <a name="webview-integration"></a>Web 视图集成
在此版本中引入了匹配不同设备外观设置的一些改进。 请确保 Web 视图的集成匹配以下设置：

在 XAML 页面 ()：

            <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
            <WebView x:Name="engagement_announcement_content" Visibility="Collapsed" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

在关联的 .cs 文件中：

    using Microsoft.Azure.Engagement;
    using System;
    using Windows.ApplicationModel.Core;
    using Windows.UI.ViewManagement;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Navigation;

    namespace My.Namespace.Example
    {
            /// <summary>
            /// An empty page that can be used on its own or navigated to within a Frame.
            /// </summary>
            public sealed partial class ExampleEngagementReachPage : EngagementPage
            {
              public ExampleEngagementReachPage()
              {
                this.InitializeComponent();

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }

              #region to implement
              /* Attach events when page is navigated. */
              protected override void OnNavigatedTo(NavigationEventArgs e)
              {
                /* Update the webview when the app window is resized. */
                Window.Current.SizeChanged += DisplayProperties_OrientationChanged;

                /* Update the webview when the app/status bar is resized. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged += DisplayProperties_VisibleBoundsChanged; 
    #endif
                base.OnNavigatedTo(e);
              }

              /* When page is left ensure to detach SizeChanged handler. */
              protected override void OnNavigatedFrom(NavigationEventArgs e)
              {
                Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged -= DisplayProperties_VisibleBoundsChanged;
    #endif
                base.OnNavigatedFrom(e);
              }

              /* "width" and "height" are the current size of your application display. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
              double width = ApplicationView.GetForCurrentView().VisibleBounds.Width;
              double height = ApplicationView.GetForCurrentView().VisibleBounds.Height;
    #else
              double width =  Window.Current.Bounds.Width;
              double height =  Window.Current.Bounds.Height;
    #endif

              /// <summary>
              /// Set your webview elements to the correct size.
              /// </summary>
              /// <param name="width">The width of your current display.</param>
              /// <param name="height">The height of your current display.</param>
              private void SetWebView(double width, double height)
              {
                #pragma warning disable 4014
                CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
                        () =>
                        {
                          this.engagement_notification_content.Width = width;
                          this.engagement_announcement_content.Width = width;
                          this.engagement_announcement_content.Height = height;
                        });
              }

              /// <summary>
              /// Handler that takes the Windows.Current.SizeChanged and indicates that webviews have to be resized.
              /// </summary>
              /// <param name="sender">Original event trigger.</param>
              /// <param name="e">Window Size Changed Event arguments.</param>
              private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
              {
                double width = e.Size.Width;
                double height = e.Size.Height;

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }

    #if WINDOWS_PHONE_APP || WINDOWS_UWP              
              /// <summary>
              /// Handler that takes the ApplicationView.VisibleBoundsChanged and indicates that webviews have to be resized
              /// </summary>
              /// <param name="sender">The related application view.</param>
              /// <param name="e">Related event arguments.</param>
              private void DisplayProperties_VisibleBoundsChanged(ApplicationView sender, Object e)
              {
                double width = sender.VisibleBounds.Width;
                double height = sender.VisibleBounds.Height;

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }
    #endif
              #endregion
            }
    }

## <a name="from-200-to-300"></a>从 2.0.0 至 3.0.0
### <a name="resources"></a>资源
此步骤仅涉及自定义的资源。 如果已对 SDK 提供的资源（html、图像、覆盖）进行了自定义，则在已升级的资源上升级并重新应用自定义前，必须对其进行备份。

## <a name="from-111-to-200"></a>从 1.1.1 至 2.0.0
以下部分介绍如何将 SDK 集成从由 Capptain SAS 提供的 Capptain 服务迁移到 Azure Mobile Engagement 支持的应用。 

> [!IMPORTANT]
> Capptain 和 Mobile Engagement 不是相同的服务，以下提供的过程仅重点描述如何迁移客户端应用。 迁移应用中的 SDK 不会将数据从 Capptain 服务器迁移到 Mobile Engagement 服务器
> 
> 

如果从较早版本进行迁移，请先参阅 Capptain 网站迁移到 1.1.1，再应用以下过程

### <a name="nuget-package"></a>NuGet 程序包
将 **Capptain.WindowsPhone** 替换为 **MicrosoftAzure.MobileEngagement** Nuget 程序包。

### <a name="applying-mobile-engagement"></a>应用 Mobile Engagement
SDK 使用术语 `Engagement`。 需要更新项目以匹配此更改。

需要卸载当前 Capptain NuGet 程序包。 请注意，Capptain 资源文件夹中的所有更改都会被删除。 如果想要保留这些文件，请对其进行备份。

之后，在项目上安装新的 Microsoft Azure Engagement nuget 程序包。 可直接在 [nuget 网站] 上找到它， 或在此处索引。 此操作将替换 Engagement 使用的所有资源文件，并将新的 Engagement DLL 添加到项目引用中。

需通过删除 Capptain DLL 引用来清理项目引用。 如果不执行此操作，Capptain 的版本会发生冲突并出现错误。

如果已自定义 Captain 资源，请复制旧文件内容并将其粘贴到新的 Engagement 文件中。 请注意，xaml 和 cs 文件都需要更新。

完成这些步骤后，只需将旧的 Capptain 引用替换为新的 Engagement 引用。

1. 所有 Capptain 命名空间均需进行更新。
   
    迁移前：
   
        using Capptain.Agent;
        using Capptain.Reach;
   
    迁移后：
   
        using Microsoft.Azure.Engagement;
2. 包含“Capptain”的所有 Capptain 类都应包含“Engagement”。
   
    迁移前：
   
        public sealed partial class MainPage : CapptainPage
        {
          protected override string GetCapptainPageName()
          {
            return "Capptain Demo";
          }
          ...
        }
   
    迁移后：
   
        public sealed partial class MainPage : EngagementPage
        {
          protected override string GetEngagementPageName()
          {
            return "Engagement Demo";
          }
          ...
        }
3. Xaml 文件 Capptain 命名空间和属性也随之更改。
   
    迁移前：
   
        <capptain:CapptainPage
        ...
        xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
        ...
        </capptain:CapptainPage>
   
    迁移后：
   
        <engagement:EngagementPage
        ...
        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
        ...
        </engagement:EngagementPage>
4. 覆盖页面更改
   
   > [!IMPORTANT]
   > 覆盖也随之更改。 其新命名空间为 `Microsoft.Azure.Engagement.Overlay`。 必须在 xaml 和 cs 文件中使用。 此外，`CapptainGrid` 需命名为 `EngagementGrid`，而 `capptain_notification_content` 和 `capptain_announcement_content` 需命名为 `engagement_notification_content` 和 `engagement_announcement_content`。
   > 
   > 
   
    对于覆盖：
   
        <capptain:CapptainPageOverlay
          xmlns:capptain="using:Capptain.Overlay"
          ...
        </capptain:CapptainPageOverlay>
   
    它变为：
   
        <EngagementPageOverlay
          engagement="using:Microsoft.Azure.Engagement.Overlay"
          ...
        </engagement:EngagementPageOverlay>
5. 对于其他资源（如 Capptain 图片和 HTML 文件），请注意，也需使用“Engagement”对其重命名。

### <a name="project-declaration"></a>项目声明
对 Package.appxmanifest `File Type Associations` 的更新如下：

* 从 capptain\_reach\_content 更新到 engagement\_reach\_content
* 从 capptain\_log\_file 更新到 engagement\_log\_file

### <a name="application-id--sdk-key"></a>应用程序 ID / SDK 密钥
Engagement 使用连接字符串。 无需使用 Mobile Engagement 指定应用程序 ID 和 SDK 密钥，只需指定连接字符串。 可在 EngagementConfiguration 文件中对其设置。

可在项目的 `Resources\EngagementConfiguration.xml` 文件中设置 Engagement 配置。

编辑此文件以指定：

* 标记 `<connectionString>` 和 `<\connectionString>` 间的应用程序连接字符串。

如果想在运行时指定连接字符串，则可以在 Engagement 代理初始化之前调用以下方法：

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(args, engagementConfiguration);

应用程序的连接字符串会显示在 Azure 经典门户中。

### <a name="items-name-change"></a>项名称更改
所有名为 *capptain* 的项被命名为 *engagement*。 同样，名为 *Capptain* 的项被命名为 *Engagement*。

常用 Capptain 项的示例：

* CapptainConfiguration 现在名为 EngagementConfiguration
* CapptainAgent 现在名为 EngagementAgent
* CapptainReach 现在名为 EngagementReach
* CapptainHttpConfig 现在名为 EngagementHttpConfig
* GetCapptainPageName 现在名为 GetEngagementPageName

请注意，重命名也会影响重写方法。

