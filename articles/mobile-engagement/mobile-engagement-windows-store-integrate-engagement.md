---
title: "Windows Universal Apps Engagement SDK 集成"
description: "如何将 Azure Mobile Engagement 与 Windows 通用应用集成"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 71236b68-5ebd-44aa-8c82-c7ca8098ea05
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 898160814304fa8ec65622056a77ca9d4caf2c99
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="windows-universal-apps-engagement-sdk-integration"></a>Windows Universal Apps Engagement SDK 集成
> [!div class="op_single_selector"]
> * [通用 Windows](mobile-engagement-windows-store-integrate-engagement.md) 
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
> * [iOS](mobile-engagement-ios-integrate-engagement.md) 
> * [Android](mobile-engagement-android-integrate-engagement.md) 
> 
> 

本指南介绍了在 Windows 通用应用程序中激活 Engagement 的分析和监视功能的最简单方法。

遵循以下步骤，即可激活用于计算所有用户、会话、活动、崩溃和技术相关统计数据所需的日志报告。 计算“事件”、“错误”和“作业”等其他统计信息所需的日志报表必须使用 Engagement API 手动完成（请参阅[如何在 Windows 通用应用程序中使用高级 Mobile Engagement 标记 API](mobile-engagement-windows-store-use-engagement-api.md)），因为这些统计信息独立于应用程序。

## <a name="supported-versions"></a>支持的版本
仅可以将 Mobile Engagement SDK for Windows Universal Apps 集成到针对以下操作系统的 Windows 运行时和通用 Windows 平台应用程序中：

* Windows 8
* Windows 8.1
* Windows Phone 8.1
* Windows 10（桌面和移动系列）

> [!NOTE]
> 如果以 Windows Phone Silverlight 为目标，请参阅 [Windows Phone Silverlight 集成步骤](mobile-engagement-windows-phone-integrate-engagement.md)。
> 
> 

## <a name="install-the-mobile-engagement-universal-apps-sdk"></a>安装 Mobile Engagement Universal Apps SDK
### <a name="all-platforms"></a>所有平台
Mobile Engagement SDK for Windows Universal App 作为一个名为 *MicrosoftAzure.MobileEngagement* 的 Nuget 程序包提供。 可以从 Visual Studio Nuget Package Manager 安装该程序包。

### <a name="windows-8x-and-windows-phone-81"></a>Windows 8.x 和 Windows Phone 8.1
NuGet 会自动在应用程序项目根目录下的 `Resources` 文件夹中部署 SDK 资源。

### <a name="windows-10-universal-windows-platform-applications"></a>Windows 10 通用 Windows 平台应用程序
NuGet 尚不会自动在 UWP 应用程序中部署 SDK 资源。 在 NuGet 中重新引入资源部署之前，必须手动部署它︰

1. 打开文件资源管理器。
2. 导航到以下位置（**x.x.x** 是要安装的 Engagement 的版本）：*%USERPROFILE%\\.nuget\packages\MicrosoftAzure.MobileEngagement\\**x.x.x**\\content\win81*
3. 将“**Resources**”文件夹从文件资源管理器拖放到 Visual Studio 中项目的根目录下。
4. 在 Visual Studio 中，选择项目，然后激活位于“**解决方案资源管理器**”顶部的“**显示所有文件**”图标。
5. 有些文件未包含在项目中。 要立即导入它们，请右键单击“**Resources**”文件夹，选择“**从项目中排除**”，并再次右键单击“**Resources**”文件夹，选择“**包括在项目中**”以重新包含整个文件夹。 “**Resources**”文件夹中的所有文件现在都包括在项目中。

## <a name="add-the-capabilities"></a>添加功能
Engagement SDK 需要 Windows SDK 的某些功能才能正常工作。

打开 `Package.appxmanifest` 文件，确保其中明确列出了以下功能：

* `Internet (Client)`

## <a name="initialize-the-engagement-sdk"></a>初始化 Engagement SDK
### <a name="engagement-configuration"></a>Engagement 配置
Engagement 配置在项目的 `Resources\EngagementConfiguration.xml` 文件中集中管理。

编辑此文件以指定：

* 标记 `<connectionString>` 和 `<\connectionString>` 间的应用程序连接字符串。

如果想在运行时指定连接字符串，则可以在 Engagement 代理初始化之前调用以下方法：

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);

应用程序的连接字符串会显示在 Azure 经典门户中。

### <a name="engagement-initialization"></a>Engagement 初始化
创建新项目时，将生成一个 `App.xaml.cs` 文件。 此类继承自 `Application`，包含许多重要的方法。 它还用于初始化 Engagement SDK。

修改 `App.xaml.cs`：

* 添加到 `using` 语句：
  
      using Microsoft.Azure.Engagement;
* 定义用于一次为所有调用共享 Engagement 初始化的方法︰
  
      private void InitEngagement(IActivatedEventArgs e)
      {
        EngagementAgent.Instance.Init(e);
  
        // or
  
        EngagementAgent.Instance.Init(e, engagementConfiguration);
      }
* 在 `OnLaunched` 方法中调用 `InitEngagement`：
  
      protected override void OnLaunched(LaunchActivatedEventArgs e)
      {
        InitEngagement(e);
      }
* 当使用某个自定义方案、另一个应用程序或命令行启动应用程序时，会调用 `OnActivated` 方法。 当应用被激活时，还需要启动 Engagement SDK。 为此，请重写 `OnActivated` 方法：
  
      protected override void OnActivated(IActivatedEventArgs args)
      {
        InitEngagement(args);
      }

> [!IMPORTANT]
> 强烈建议不要在应用程序的另一个位置添加 Engagement 初始化。
> 
> 

## <a name="basic-reporting"></a>基本报告
### <a name="recommended-method-overload-your-page-classes"></a>建议方法：重载 `Page` 类
若要激活 Engagement 计算用户、会话、活动、崩溃和技术统计数据所需的所有日志报告，只需从 `EngagementPage` 类继承所有 `Page` 子类。

下面举例说明了如何为应用程序的一个页面执行此操作。 可以为应用程序的所有页面执行相同的操作。

#### <a name="c-source-file"></a>C# 源文件
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
> 如果页面重写了 `OnNavigatedTo` 方法，则必须调用 `base.OnNavigatedTo(e)`。 否则，该活动将不会报告（`EngagementPage` 在其 `OnNavigatedTo` 方法内调用 `StartActivity`）。
> 
> 

#### <a name="xaml-file"></a>XAML 文件
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

#### <a name="override-the-default-behaviour"></a>重写默认行为
默认情况下，页面的类名被报告为活动名，无其他附加内容。 如果类使用“Page”后缀，Engagement 也会将其删除。

如果想要重写名称的默认行为，只需将此内容添加到代码中即可：

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

如果想要报告关于活动的一些额外信息，可以将此内容添加到代码中：

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
> Windows Universal SDK 会在应用程序关闭时自动调用 `EndActivity` 方法。 因此，**强烈**建议任何时候用户的活动改变时都调用 `StartActivity` 方法，而且**永远不要**调用 `EndActivity` 方法，因为此方法会向 Engagement 服务器发送当前用户已离开应用程序的信息，这会影响所有应用程序日志。
> 
> 

## <a name="advanced-reporting"></a>高级报告
根据需要，可能要报告应用程序特定事件、错误和作业，为此，可使用在 `EngagementAgent` 类中找到的其他方法。 Engagement API 允许使用 Engagement 的所有高级功能。

要了解更多信息，请参阅[如何在 Windows Universal 应用中使用高级的 Mobile Engagement 标记 API](mobile-engagement-windows-store-use-engagement-api.md)。

## <a name="advanced-configuration"></a>高级配置
### <a name="disable-automatic-crash-reporting"></a>禁用自动崩溃报告
可以禁用 Engagement 的自动崩溃报告功能。 这样在发生未经处理的异常时，Engagement 不会执行任何操作。

> [!WARNING]
> 如果打算禁用此功能，请注意，未处理的崩溃将出现在应用程序中，Engagement 将不会发送崩溃，**并且**不会关闭会话和任务。
> 
> 

若要禁用自动崩溃报告，只需根据声明它的方式自定义配置即可：

#### <a name="from-engagementconfigurationxml-file"></a>在 `EngagementConfiguration.xml` 文件中
将报告崩溃设置为 `<reportCrash>` 和 `</reportCrash>` 标记之间的 `false`。

#### <a name="from-engagementconfiguration-object-at-run-time"></a>在运行时的 `EngagementConfiguration` 对象中
使用 EngagementConfiguration 对象将报告崩溃设置为 false。

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>突发模式
默认情况下，Engagement 服务实时报告日志。 如果应用程序频繁报告日志，最好对日志进行缓冲并在固定的时间一次性进行报告（称为“突发模式”）。

若要执行此操作，调用方法：

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

参数值为**毫秒**。 每当想要重新激活实时日志记录时，只需调用该方法（但不包含任何参数），或将值设置为 0。

突发模式会略微增加电池寿命，但会对 Engagement 监视器造成影响：所有会话和任务持续时间均被取整为突发阈值（这样，小于突发阈值的会话和任务可能不会显示）。 建议使用的突发阈值不长于 30000（30 秒）。 必须注意，保存的日志限制为 300 条。 如果发送的日志太长，可能会丢失某些日志。

> [!WARNING]
> 不能将突发阈值配置为小于 1 秒。 如果尝试这样做，SDK 会显示错误跟踪，并将阈值自动重置为默认值（即 0 秒）。 这会触发 SDK 实时报告日志。
> 
> 

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview

