---
title: "Windows Phone Silverlight Engagement SDK 集成"
description: "如何将 Azure Mobile Engagement 与 Windows Phone Silverlight 应用集成"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 447fea8d-f4e3-4ad4-8ec0-8e3cf1ad3ab0
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 67f69a5a92c922bc7357c1e4bcc88f55e16d1255
ms.lasthandoff: 11/17/2016


---
# <a name="windows-phone-silverlight-engagement-sdk-integration"></a>Windows Phone Silverlight Engagement SDK 集成
> [!div class="op_single_selector"]
> * [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md) 
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
> * [iOS](mobile-engagement-ios-integrate-engagement.md) 
> * [Android](mobile-engagement-android-integrate-engagement.md) 
> 
> 

此过程描述在 Windows Phone Silverlight 应用程序中激活 Azure Mobile Engagement 分析和监视功能的最简单方法。

以遵循以下步骤，即可激活用于计算所有用户、会话、活动、崩溃和技术相关统计数据所需的日志报告。 由于事件、错误和任务等其他统计数据与应用程序相关，因此必须使用 Engagement API 手动生成用于计算这些统计数据的日志报告（请参阅下面的[如何在 Windows Phone Silverlight 应用中使用高级 Mobile Engagement 标记 API](mobile-engagement-windows-phone-use-engagement-api.md)）。

## <a name="supported-versions"></a>支持的版本
Mobile Engagement SDK for Windows Silverlight 只能集成到以下列版本为目标的应用程序：

* Windows Phone 8.0
* Windows Phone 8.1 Silverlight

> [!NOTE]
> 如果以 Windows Phone 8.1（非 Silverlight）为目标，请参阅 [Windows Universal 集成程序](mobile-engagement-windows-store-integrate-engagement.md)。
> 
> 

## <a name="install-the-mobile-engagement-silverlight-sdk"></a>安装 Mobile Engagement Silverlight SDK
Mobile Engagement SDK for Windows Silverlight 作为一个名为 *MicrosoftAzure.MobileEngagement* 的 Nuget 程序包提供。 可以从 Visual Studio Nuget Package Manager 安装该程序包。 

## <a name="add-the-capabilities"></a>新增功能
为了能正常运行，Engagement SDK 需要 Windows Phone Silverlight SDK 的一些功能。

打开 `WMAppManifest.xml` 文件，确保“`Capabilities`”面板中明确列出了以下功能：

* `ID_CAP_NETWORKING`
* `ID_CAP_IDENTITY_DEVICE`

## <a name="initialize-the-engagement-sdk"></a>初始化 Engagement SDK
### <a name="engagement-configuration"></a>Engagement 配置
Engagement 配置在项目的 `Resources\EngagementConfiguration.xml` 文件中集中管理。

编辑此文件以指定：

* 标记 `<connectionString>` 和 `<\connectionString>` 间的应用程序连接字符串。

如果想在运行时指定连接字符串，则可以在 Engagement 代理初始化之前调用以下方法：

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

应用程序的连接字符串将显示在 Azure 经典门户中。

### <a name="engagement-initialization"></a>Engagement 初始化
创建新项目时，将生成一个 `App.xaml.cs` 文件。 此类继承自 `Application`，包含许多重要的方法。 它还用于初始化 Engagement SDK。

修改 `App.xaml.cs`：

* 添加到 `using` 语句：
  
      using Microsoft.Azure.Engagement;
* 在 `Application_Launching` 方法中插入 `EngagementAgent.Instance.Init`：
  
      private void Application_Launching(object sender, LaunchingEventArgs e)
      {
        EngagementAgent.Instance.Init();
      }
* 在 `Application_Activated` 方法中插入 `EngagementAgent.Instance.OnActivated`：
  
      private void Application_Activated(object sender, ActivatedEventArgs e)
      {
         EngagementAgent.Instance.OnActivated(e);
      }

> [!WARNING]
> 强烈建议你不要在应用程序的另一个位置添加 Engagement 初始化。 但是，请注意，`EngagementAgent.Instance.Init` 方法运行于专用线程，而非 UI 线程。
> 
> 

## <a name="basic-reporting"></a>基本报告
### <a name="recommended-method--overload-your-phoneapplicationpage-classes"></a>推荐的方法：重载 `PhoneApplicationPage` 类
若要激活 Engagement 计算用户、会话、活动、崩溃和技术统计数据所需的所有日志报告，只需从 `EngagementPage` 类继承所有 `PhoneApplicationPage` 子类。

下面举例说明了如何为应用程序的一个页面执行此操作。 可以为应用程序的所有页面执行相同的操作。

#### <a name="c-source-file"></a>C# 源文件
修改页面 `.xaml.cs` 文件：

* 添加到 `using` 语句：
  
      using Microsoft.Azure.Engagement;
* 将 `PhoneApplicationPage` 替换为 `EngagementPage`：

**没有 Engagement：**

        namespace Example
        {
          public partial class ExamplePage : PhoneApplicationPage
          {
            [...]
          }
        }

**有 Engagement：**

        using Microsoft.Azure.Engagement;

        namespace Example
        {
          public partial class ExamplePage : EngagementPage 
          {
            [...]
          }
        }

> [!WARNING]
> 如果页面继承自 `OnNavigatedTo` 方法，务必让 `base.OnNavigatedTo(e)` 调用。 否则，将不会报告该活动。 实际上，`EngagementPage` 将调用 `StartActivity` 方法中的 `OnNavigatedTo`。
> 
> 

#### <a name="xaml-file"></a>XAML 文件
修改页面 `.xaml` 文件：

* 添加到命名空间声明：
  
      xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
* 将 `phone:PhoneApplicationPage` 替换为 `engagement:EngagementPage`：

**没有 Engagement：**

        <phone:PhoneApplicationPage>
            <!-- layout -->
        </phone:PhoneApplicationPage>

**有 Engagement：**

        <engagement:EngagementPage 
            xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP">

            <!-- layout -->
        </engagement:EngagementPage >

#### <a name="override-the-default-behavior"></a>重写默认行为
默认情况下，页面的类名被报告为活动名，无其他附加内容。 如果类使用“Page”后缀，Engagement 也会将其删除。

如果想要重写名称的默认行为，只需将此内容添加到你的代码中即可：

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
           /* your code */
           return "new name";
        }

如果想要报告关于活动的一些额外信息，可以将此内容添加到你的代码中：

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
           /* your code */
           return extra;
        }

这些方法将从页面的 `OnNavigatedTo` 方法中调用。

### <a name="alternate-method-call-startactivity-manually"></a>备用方法：手动调用 `StartActivity()`
如果无法或不想重载 `PhoneApplicationPage` 类，则可以通过直接调用 `EngagementAgent` 方法来启动活动。

建议从 PhoneApplicationPage 的 `OnNavigatedTo` 方法中调用 `StartActivity`。

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
           base.OnNavigatedTo(e);
           EngagementAgent.Instance.StartActivity("MyPage");
        }

> [!IMPORTANT]
> 确保正确结束会话。
> 
> 应用程序关闭时，SDK 会自动调用 `EndActivity` 方法。 因此，**强烈**建议每当用户的活动更改时即调用 `StartActivity` 方法，并且**从不**调用 `EndActivity` 方法。 此方法会向 Engagement 服务器发送一条消息，告知当前用户已离开应用程序，这将会影响所有应用程序日志。
> 
> 

## <a name="advanced-reporting"></a>高级报告
（可选）你可能希望报告应用程序特定事件、错误和任务，为此，请使用 `EngagementAgent` 类中找到的其他方法。 Engagement API 允许使用 Engagement 的所有高级功能。

有关详细信息，请参阅[如何在 Windows Phone Silverlight 应用中使用高级 Mobile Engagement 标记 API](mobile-engagement-windows-phone-use-engagement-api.md)。

## <a name="advanced-configuration"></a>高级配置
### <a name="disable-automatic-crash-reporting"></a>禁用自动崩溃报告
可以禁用 Engagement 的自动崩溃报告功能。 这样在发生未经处理的异常时，Engagement 不会执行任何操作。

> [!WARNING]
> 如果打算禁用此功能，请注意，未处理的崩溃将出现在应用程序中，Engagement 将不会发送崩溃，**并且**它不会关闭会话和任务。
> 
> 

要禁用自动崩溃报告，只需要根据你声明的方式自定义配置即可：

#### <a name="from-engagementconfigurationxml-file"></a>在 `EngagementConfiguration.xml` 文件中
将报告崩溃设置为 `<reportCrash>` 和 `</reportCrash>` 标记之间的 `false`。

#### <a name="from-engagementconfiguration-object-at-run-time"></a>在运行时的 `EngagementConfiguration` 对象中
使用 EngagementConfiguration 对象将报告崩溃设置为 false。

        /* Engagement configuration. */

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration(); engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        /\* Disable Engagement crash reporting. \*/ engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>突发模式
默认情况下，Engagement 服务实时报告日志。 如果应用程序频繁报告日志，最好对日志进行缓冲并在固定的时间一次性进行报告（称为“突发模式”）。

若要执行此操作，调用方法：

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

参数值为**毫秒**。 每当想要重新激活实时日志记录时，只需调用该方法（但不包含任何参数），或将值设置为 0。

突发模式会略微增加电池寿命，但会对 Engagement 监视器造成影响：所有会话和任务持续时间均被取整为突发阈值（这样，小于突发阈值的会话和任务可能不会显示）。 建议使用的突发阈值不长于 30000（30 秒）。 必须注意，保存的日志限制为 300 条。 如果发送的日志太长，可能会丢失某些日志。

> [!WARNING]
> 不能将突发阈值配置为小于一秒。 如果尝试这样做，SDK 将显示错误跟踪，并将阈值自动重置为默认值（即零秒）。 这将触发 SDK 实时报告日志。
> 
> 


