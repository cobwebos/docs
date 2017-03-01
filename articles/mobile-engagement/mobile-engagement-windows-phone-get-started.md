---
title: "适用于 Windows Phone Silverlight 应用的 Azure Mobile Engagement 入门"
description: "了解如何使用适用于 Windows Phone Silverlight 应用的具有分析和推送通知功能的 Azure Mobile Engagement。"
services: mobile-engagement
documentationcenter: windows
author: piyushjo
manager: erikre
editor: 
ms.assetid: aa34692f-87f7-47c6-a20c-a1972750bc25
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 06e16033435ed0a37d5688055743875827d3aec2
ms.openlocfilehash: 3f1f8d74eb2f562991b351cae87c372897644cf8
ms.lasthandoff: 03/01/2017


---
# <a name="get-started-with-azure-mobile-engagement-for-windows-phone-silverlight-apps"></a>适用于 Windows Phone Silverlight 应用的 Azure Mobile Engagement 入门
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

本主题介绍如何使用 Azure Mobile Engagement 来了解应用使用情况和向 Windows Phone Silverlight 应用程序的细分用户发送推送通知。
本教程演示使用 Mobile Engagement 的简单广播方案。 演示中，你将创建一个空白 Windows Phone&8; 应用，以使用 Microsoft 推送通知服务 (MPNS) 收集基本数据和接收推送通知。

> [!NOTE]
> 如果你要以 Windows Phone 8.1（非 Silverlight）为目标，请参阅 [Windows Universal 教程](mobile-engagement-windows-store-dotnet-get-started.md)。
> 
> 

本教程需要的内容如下：

* Visual Studio 2013
* [MicrosoftAzure.MobileEngagement] Nuget 包

> [!NOTE]
> 若要完成本教程，你必须有一个有效的 Azure 帐户。 如果你没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-windows-phone-get-started)。
> 
> 

## <a name="a-idsetup-azmeasetup-mobile-engagement-for-your-windows-phone-app"></a><a id="setup-azme"></a>为 Windows Phone 应用设置 Mobile Engagement
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="a-idconnecting-appaconnect-your-app-to-the-mobile-engagement-backend"></a><a id="connecting-app"></a>将应用连接到 Mobile Engagement 后端
本教程提供的“基本集成”是收集数据和发送推送通知的最低要求。 在 [Mobile Engagement Windows Phone SDK 集成](mobile-engagement-windows-phone-sdk-overview.md)

我们将通过 Visual Studio 创建基本应用，以演示该集成。

### <a name="create-a-new-windows-phone-silverlight-project"></a>创建新的 Windows Phone Silverlight 项目
以下步骤假定使用 Visual Studio 2015，步骤与 Visual Studio 早期版本中的类似。 

1. 启动 Visual Studio，在“主页”屏幕上，选择“新建项目”。
2. 在弹出窗口中，选择“Windows 8” -> “Windows Phone” -> “空白应用 (Windows Phone Silverlight)”。 输入应用“名称”和“解决方案名称”，然后单击“确定”。
   
    ![][1]
3. 可以选择面向 **Windows Phone 8.0** 或 **Windows Phone 8.1**。

现在已创建了新的空白 Windows Phone Silverlight 应用，我们将在其中集成 Azure Mobile Engagement SDK。

### <a name="connect-your-app-to-the-mobile-engagement-backend"></a>将应用连接到 Mobile Engagement 后端
1. 在项目中安装 [MicrosoftAzure.MobileEngagement] nuget 包。
2. 打开 `WMAppManifest.xml`（在“属性”文件夹中），并确保在 `<Capabilities />` 标记中声明以下内容（如果未声明，请添加）：
   
        <Capability Name="ID_CAP_NETWORKING" />
        <Capability Name="ID_CAP_IDENTITY_DEVICE" />
   
    ![][2]
3. 现在，将之前为 Mobile Engagement 应用复制的连接字符串粘贴在 `Resources\EngagementConfiguration.xml` 文件的 `<connectionString>` 和 `</connectionString>` 标记之间：
   
    ![][3]
4. 在 `App.xaml.cs` 文件中：
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 添加 `using` 语句：
   
            using Microsoft.Azure.Engagement;
   
    b.保留“数据库类型”设置，即设置为“共享”。 使用 `Application_Launching` 方法初始化 SDK：
   
            private void Application_Launching(object sender, LaunchingEventArgs e)
            {
              EngagementAgent.Instance.Init();
            }
   
    c. 在 `Application_Activated` 中插入以下内容：
   
            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
               EngagementAgent.Instance.OnActivated(e);
            }

## <a name="a-idmonitoraenable-real-time-monitoring"></a><a id="monitor"></a>启用实时监视
为了开始发送数据并确保用户处于活动状态，必须将至少一个屏幕（活动）发送到 Mobile Engagement 后端。

1. 在“MainPage.xaml.cs”中，添加 `using` 语句：
   
        using Microsoft.Azure.Engagement;
2. 将 **MainPage** 的基类（位于 **PhoneApplicationPage** 前）替换为 **EngagementPage**。
   
        class MainPage : EngagementPage 
3. 在 `MainPage.xml` 文件中︰
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 添加到命名空间声明：
   
            xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
   
    b.保留“数据库类型”设置，即设置为“共享”。 将 XML 标记名称中的 `phone:PhoneApplicationPage` 替换为 `engagement:EngagementPage`。

## <a name="a-idmonitoraconnect-app-with-real-time-monitoring"></a><a id="monitor"></a>将应用与实时监视相连接
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a name="a-idintegrate-pushaenable-push-notifications-and-in-app-messaging"></a><a id="integrate-push"></a>启用推送通知和应用内消息传送
在市场推广活动中，可以使用 Mobile Engagement 通过推送通知和应用内消息传送与 REACH 模块和用户进行交互。 在 Mobile Engagement 门户中，此模块称为 REACH。
以下各部分将介绍如何设置应用以接收推送通知。

### <a name="enable-your-app-to-receive-mpns-push-notifications"></a>启用应用接收 MPNS 推送通知
将新功能添加到 `WMAppManifest.xml` 文件︰

        ID_CAP_PUSH_NOTIFICATION
        ID_CAP_WEBBROWSERCOMPONENT

   ![][5]

### <a name="initialize-the-reach-sdk"></a>初始化 REACH SDK
1. 在 `App.xaml.cs` 中，在代理初始化之后立即在 **Application_Launching** 函数中调用 `EngagementReach.Instance.Init();`：
   
        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
           EngagementAgent.Instance.Init();
           EngagementReach.Instance.Init();
        }
2. 在 `App.xaml.cs` 中，在代理初始化之后立即在 **Application_Activated** 函数中调用 `EngagementReach.Instance.OnActivated(e);`：
   
        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
           EngagementReach.Instance.OnActivated(e);
        }

你已经完成全部设置。 现在我们将验证是否已正确执行了此基本集成。

## <a name="a-idsendasend-a-notification-to-your-app"></a><a id="send"></a>向应用发送通知
[!INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

你现在应在设备上看到一条通知，如果应用是打开的，将显示为应用内通知，否则将显示为 toast 通知，如下所示︰ 

![][6]

<!-- URLs. -->
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9874664
[Mobile Engagement Windows Phone SDK documentation]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[2]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[3]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[5]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[6]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png

