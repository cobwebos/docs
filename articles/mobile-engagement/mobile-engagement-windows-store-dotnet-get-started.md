---
title: "适用于 Windows Universal 应用的 Azure Mobile Engagement 入门"
description: "了解如何使用适用于 Windows Universal 应用且带分析和推送通知功能的 Azure Mobile Engagement。"
services: mobile-engagement
documentationcenter: windows
author: piyushjo
manager: dwrede
editor: 
ms.assetid: 48103867-7f64-4646-b019-42bd797d38e2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
translationtype: Human Translation
ms.sourcegitcommit: 830eb6627cae71f358b9790791b1d86f7c82c566
ms.openlocfilehash: 17057b78cbba962022afc92cdff4e506bcc0e46e


---
# <a name="get-started-with-azure-mobile-engagement-for-windows-universal-apps"></a>适用于 Windows Universal 应用的 Azure Mobile Engagement 入门
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

本主题介绍如何借助 Azure Mobile Engagement 了解应用的使用，以及如何向 Windows Universal 应用程序的分段用户发送推送通知。
本教程演示使用 Mobile Engagement 的简单广播方案。 创建一个空白 Windows Universal 应用，该应用使用 Windows 通知服务 (WNS) 收集基本应用使用数据以及接收推送通知。

## <a name="prerequisites"></a>先决条件
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

## <a name="set-up-mobile-engagement-for-your-windows-universal-app"></a>为 Windows Universal 应用设置 Mobile Engagement
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="a-idconnecting-appaconnect-your-app-to-the-mobile-engagement-backend"></a><a id="connecting-app"></a>将应用连接到 Mobile Engagement 后端
本教程提供的“基本集成”是收集数据和发送推送通知的最低要求。 在 [Mobile Engagement Windows Universal SDK 集成](mobile-engagement-windows-store-sdk-overview.md)中可找到完整的集成文档。

通过 Visual Studio 创建基本应用，演示该集成。

### <a name="create-a-windows-universal-app-project"></a>创建 Windows Universal 应用项目
以下步骤假定使用 Visual Studio 2015，步骤与 Visual Studio 早期版本中的类似。

1. 启动 Visual Studio，然后在“主页”屏幕上，选择“新建项目”。
2. 在弹出的窗口中，选择“Windows” -> “通用” -> “空白应用(通用 Windows)”。 输入应用“名称”和“解决方案名称”，然后单击“确定”。

    ![][1]

创建 Windows Universal 应用项目后，下一步将在其中集成 Azure Mobile Engagement SDK。

### <a name="connect-your-app-to-mobile-engagement-backend"></a>将应用连接到 Mobile Engagement 后端
1. 在项目中安装 [MicrosoftAzure.MobileEngagement] NuGet 包。 如果要同时面向 Windows 和 Windows Phone 平台，则需要为这两个项目执行此操作。 对于 Windows 8.x 和 Windows Phone 8.1，同一个 NuGet 包会在每个项目中放置特定于平台的正确二进制文件。
2. 打开“Package.appxmanifest”  ，确保存在此添加了以下功能：

        Internet (Client)

    ![][2]
3. 复制之前为 Mobile Engagement 应用复制的连接字符串，将其粘贴到 `Resources\EngagementConfiguration.xml` 文件的 `<connectionString>` 与 `</connectionString>` 标记之间：

    ![][3]

    > [!TIP]
    > 如果应用同时面向 Windows 和 Windows Phone 平台，仍应创建两个 Mobile Engagement 应用程序（每个受支持的平台一个）。 两个应用可以确保创建正确的受众分段，并且可以适当地发送每个平台的定向通知。

    > [!IMPORTANT]
    > NuGet 不会自动在 Windows 10 UWP 应用程序中复制 SDK 资源。 必须按照在安装 Nuget 包时显示的步骤手动进行操作。  

1. 在 `App.xaml.cs` 文件中：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 添加 `using` 语句：

            using Microsoft.Azure.Engagement;

    b.保留“数据库类型”设置，即设置为“共享”。 添加初始化 Engagement 的方法：

           private void InitEngagement(IActivatedEventArgs e)
           {
             EngagementAgent.Instance.Init(e);

             //... rest of the code
           }

    c. 使用 **OnLaunched** 方法初始化 SDK：

            protected override void OnLaunched(LaunchActivatedEventArgs e)
            {
              InitEngagement(e);

              //... rest of the code
            }

    c. 在 **OnActivated** 方法中插入以下内容，添加此方法（如果不存在）：

            protected override void OnActivated(IActivatedEventArgs e)
            {
              InitEngagement(e);

              //... rest of the code
            }

## <a name="a-idmonitoraenable-real-time-monitoring"></a><a id="monitor"></a>启用实时监视
要开始发送数据并确保用户处于活动状态，必须将至少一个屏幕（活动）发送到 Mobile Engagement 后端。

1. 在 **MainPage.xaml.cs** 中，添加以下 `using` 语句：

    使用 Microsoft.Azure.Engagement.Overlay;
2. 将“MainPage”的基类从“Page”更改为“EngagementPageOverlay”：

        class MainPage : EngagementPageOverlay
3. 在 `MainPage.xaml` 文件中：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 添加到命名空间声明：

        xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

    b.保留“数据库类型”设置，即设置为“共享”。 将 XML 标记名称中的“Page”替换为“engagement:EngagementPageOverlay”

> [!IMPORTANT]
> 如果页面重写了 `OnNavigatedTo` 方法，则必须调用 `base.OnNavigatedTo(e)`。 否则，该活动不会报告 `EngagementPage` 在其 `OnNavigatedTo` 方法内调用 `StartActivity`）。 这一点在 Windows Phone 项目中尤为重要，在该项目中，默认模板具有 `OnNavigatedTo` 方法。
>
> 对于 **Windows 10 通用应用**，使用 [Advanced Reporting with the Windows Universal Apps Engagement SDK](mobile-engagement-windows-store-advanced-reporting.md)（使用 Windows 通用应用 Engagement SDK 的高级报告）的“推荐方法：重载页类”一节中建议的方法，而不是上面所述的方法。

## <a name="a-idmonitoraconnect-app-with-real-time-monitoring"></a><a id="monitor"></a>将应用与实时监视相连
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a name="a-idintegrate-pushaenable-push-notifications-and-in-app-messaging"></a><a id="integrate-push"></a>启用推送通知和应用内消息传送
进行市场活动时，可以使用 Mobile Engagement 通过推送通知和应用内消息传送与用户交互以及向用户进行市场宣传。 在 Mobile Engagement 门户中，此模块称为 REACH。
以下部分介绍如何将应用设置为接收此类通知和消息。

### <a name="enable-your-app-to-receive-wns-push-notifications"></a>允许应用接收 WNS 推送通知
1. 在 `Package.appxmanifest` 文件中，在“应用程序”选项卡的“通知”下，将“支持 Toast 通知:”设置为“是”

    ![][5]

### <a name="initialize-the-reach-sdk"></a>初始化 REACH SDK
代理初始化后，在 `App.xaml.cs` 中调用 **InitEngagement** 函数中的 **EngagementReach.Instance.Init(e);**：

        private void InitEngagement(IActivatedEventArgs e)
        {
           EngagementAgent.Instance.Init(e);
           EngagementReach.Instance.Init(e);
        }

准备发送 toast。 接下来，确认是否已正确执行此基本集成。

### <a name="grant-access-to-mobile-engagement-to-send-notifications"></a>授予 Mobile Engagement 访问权限，发送通知
1. 在 Web 浏览器中打开 [Windows 应用商店开发人员中心] ，登录，创建一个帐户（如有必要）。
2. 单击右上角的“仪表板”，然后在左侧面板菜单中单击“创建新应用”。

    ![][9]
3. 保留名称，创建应用。

    ![][10]
4. 创建应用后，从左侧菜单导航到“服务”->“推送通知”。

    ![][11]
5. 在“推送通知”部分中，单击“Live 服务站点”  链接。

    ![][12]
6. 导航到“推送凭据”部分。 确保位于“应用设置”部分，然后复制“包 SID”和“客户端密码”

    ![][13]
7. 导航到 Mobile Engagement 门户的“设置”，单击左侧的“原生推送”部分。 单击“编辑”按钮，按如下所示输入“包安全标识符 (SID)”和“密钥”：

    ![][6]
8. 确保在应用商店中将 Visual Studio 应用与创建的这个应用关联。 单击 Visual Studio 中的“将应用与应用商店关联”  。

    ![][7]

## <a name="a-idsendasend-a-notification-to-your-app"></a><a id="send"></a>向应用发送通知
[!INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

如果应用正在运行，会看到应用内通知。 如果应用已关闭，则会看到 toast 通知。
如果看到的是应用内通知而不是 toast 通知，并且在 Visual Studio 调试模式下运行应用，请尝试访问工具栏中的“生命周期事件”->“挂起”，确保应用已挂起。 在 Visual Studio 中调试应用程序时，如果单击“主页”按钮，那么并不会始终挂起应用，而是看到应用内通知时（不会显示为 toast 通知）。  

![][8]

<!-- URLs. -->
[Mobile Engagement Windows 通用 SDK 文档]: ../mobile-engagement-windows-store-integrate-engagement/
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9864592
[Windows 应用商店开发人员中心]: https://dev.windows.com
[Windows 通用应用 - 覆盖集成]: ../mobile-engagement-windows-store-integrate-engagement-reach/#overlay-integration

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-store-dotnet-get-started/universal-app-creation.png
[2]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-capabilities.png
[3]: ./media/mobile-engagement-windows-store-dotnet-get-started/add-connection-info.png
[5]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-toast.png
[6]: ./media/mobile-engagement-windows-store-dotnet-get-started/enter-credentials.png
[7]: ./media/mobile-engagement-windows-store-dotnet-get-started/associate-app-store.png
[8]: ./media/mobile-engagement-windows-store-dotnet-get-started/vs-suspend.png
[9]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_create_app.png
[10]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_app_name.png
[11]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push.png
[12]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_1.png
[13]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_creds.png



<!--HONumber=Nov16_HO2-->


