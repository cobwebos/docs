---
title: "Windows 通用高级报告与 MobileApps Engagement"
description: "如何将 Azure Mobile Engagement 与 Windows 通用应用集成"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: ea5030bf-73ac-49b7-bc3e-c25fc10e945a
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: feac309db1ffce0945012e293bfc1df417aed876


---
# <a name="advanced-reporting-with-the-windows-universal-apps-engagement-sdk"></a>使用 Windows 通用应用 Engagement SDK 的高级报告
> [!div class="op_single_selector"]
> * [通用 Windows](mobile-engagement-windows-store-advanced-reporting.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-reporting.md)
> 
> 

本主题介绍 Windows 通用应用程序中的其他报告方案。 这些方案包括的选项可供你选择，以应用于在[入门](mobile-engagement-windows-store-dotnet-get-started.md)教程中创建的应用程序。

## <a name="prerequisites"></a>先决条件
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

开始本教程学习之前，必须先完成[入门](mobile-engagement-windows-store-dotnet-get-started.md)教程，该教程相对浅显易懂。 本教程介绍可以从中选择的其他选项。

## <a name="specifying-engagement-configuration-at-runtime"></a>指定运行时的 Engagement 配置
Engagement 配置集中在项目的 `Resources\EngagementConfiguration.xml` 文件中，这是在[入门](mobile-engagement-windows-store-dotnet-get-started.md)主题中指定的位置。

但也可以在运行时指定位置：你可以在 Engagement 代理初始化前调用以下方法：

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);



## <a name="recommended-method-overload-your-page-classes"></a>建议方法：重载 `Page` 类
要激活 Engagement 计算用户、会话、活动、崩溃和技术统计信息所需的所有日志报告，请使全部 `Page` 子类继承自 `EngagementPage` 类。

下面是应用程序的页面示例。 可以为应用程序的所有页面执行相同的操作。

### <a name="c-source-file"></a>C# 源文件
修改页面 `.xaml.cs` 文件：

* 添加到 `using` 语句：
  
      using Microsoft.Azure.Engagement;
* 将 `Page` 替换为 `EngagementPage`：

**没有 Engagement：**

        namespace Example
        {
          public sealed partial class ExamplePage : Page
          {
            [...]
          }
        }

**有 Engagement：**

        using Microsoft.Azure.Engagement;

        namespace Example
        {
          public sealed partial class ExamplePage : EngagementPage
          {
            [...]
          }
        }

> [!IMPORTANT]
> 如果页面重写了 `OnNavigatedTo` 方法，则必须调用 `base.OnNavigatedTo(e)`。 否则，不会报告该活动（`EngagementPage` 会在其 `OnNavigatedTo` 方法内调用 `StartActivity`）。
> 
> 

### <a name="xaml-file"></a>XAML 文件
修改页面 `.xaml` 文件：

* 添加到命名空间声明：
  
      xmlns:engagement="using:Microsoft.Azure.Engagement"
* 将 `Page` 替换为 `engagement:EngagementPage`：

**没有 Engagement：**

        <Page>
            <!-- layout -->
            ...
        </Page>

**有 Engagement：**

        <engagement:EngagementPage
            xmlns:engagement="using:Microsoft.Azure.Engagement">
            <!-- layout -->
            ...
        </engagement:EngagementPage >

### <a name="override-the-default-behaviour"></a>重写默认行为
默认情况下，页面的类名被报告为活动名，无其他附加内容。 如果类使用“Page”后缀，则 Engagement 会将其删除。

要重写名称的默认行为的，请添加以下代码：

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

要报告活动的附加信息，请添加以下代码：

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
          /* your code */
          return extra;
        }

这些方法将从页面的 `OnNavigatedTo` 方法中调用。

### <a name="alternate-method-call-startactivity-manually"></a>备用方法：手动调用 `StartActivity()`
如果无法或不想重载 `Page` 类，则可以通过直接调用 `EngagementAgent` 方法来启动活动。

我们建议在页面的 `OnNavigatedTo` 方法内调用 `StartActivity`。

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [!IMPORTANT]
> 确保正确结束会话。
> 
> Windows Universal SDK 将在应用程序关闭时自动调用 `EndActivity` 方法。 因此，**强烈**建议每当用户的活动更改时即调用 `StartActivity` 方法，并且**从不**调用 `EndActivity` 方法。 此方法会告知 Engagement 服务器将当前用户已离开应用程序，这将影响该应用程序的所有日志。
> 
> 

## <a name="advanced-reporting"></a>高级报告
根据需要，你可能要报告应用程序特定事件、错误和作业，为此，可使用在 `EngagementAgent` 类中找到的其他方法。 Engagement API 允许使用的所有 Engagement 高级功能。

要了解更多信息，请参阅[如何在 Windows 通用应用中使用高级的 Mobile Engagement 标记 API](mobile-engagement-windows-store-use-engagement-api.md)。




<!--HONumber=Nov16_HO3-->


