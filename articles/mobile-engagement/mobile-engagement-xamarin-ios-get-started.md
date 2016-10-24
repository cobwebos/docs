<properties
	pageTitle="适用于 Xamarin.iOS 的 Azure Mobile Engagement 入门"
	description="了解如何使用适用于 Xamarin.iOS 应用的具有分析和推送通知功能的 Azure Mobile Engagement。"
	services="mobile-engagement"
	documentationCenter="xamarin"
	authors="piyushjo"
	manager=""
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="08/19/2016"
	ms.author="piyushjo" />

# 适用于 Xamarin.iOS 应用的 Azure Mobile Engagement 入门

[AZURE.INCLUDE [Hero 教程切换器](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

本主题介绍如何借助 Azure Mobile Engagement 了解应用的使用，并向 Xamarin.iOS 应用程序的细分用户发送推送通知。在本教程中，你将创建一个空白 Xamarin.iOS 应用，用于通过 Apple Push Notification 系统 (APNS) 接收推送通知。

本教程需要的内容如下：

+ [Xamarin Studio](http://xamarin.com/studio)。你也可以将 Xamarin 与 Visual Studio 配合使用，但是本教程使用的是 Xamarin Studio。如需安装说明，请参阅[设置和安装 Visual Studio 和 Xamarin](https://msdn.microsoft.com/library/mt613162.aspx)。
+ [Mobile Engagement Xamarin SDK](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [AZURE.NOTE] 若要完成本教程，你必须有一个有效的 Azure 帐户。如果你没有帐户，只需花费几分钟就能创建一个免费试用帐户。有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-CN%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-ios-get-started)。

##<a id="setup-azme"></a>为 iOS 应用设置 Mobile Engagement

[AZURE.INCLUDE [在门户中创建 Mobile Engagement 应用](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>将应用连接到 Mobile Engagement 后端

本教程介绍的“基本集成”是收集数据和发送推送通知的最低要求。

通过 Xamarin 创建一个基本应用即可演示该集成：

###创建新的 Xamarin iOS 项目

1. 启动 Xamarin Studio。转到“**文件**”->“**新建**”->“**解决方案**”

    ![][1]

2. 选择“**单一视图应用**”，确保所选语言为“**C#**”，然后单击“**下一步**”。

    ![][2]

3. 填写“**应用名称**”和“**组织标识符**”，然后单击“**下一步**”。

    ![][3]

	> [AZURE.IMPORTANT] 请确保最终用于部署 iOS 应用的发布配置文件所用的“应用 ID”与此处使用的“捆绑标识符”完全匹配。

4. 如果需要，更新“**项目名称**”、“**解决方案名称**”和“**位置**”，然后单击“**创建**”。

    ![][4]
 
Xamarin Studio 将创建在其中集成 Mobile Engagement 的演示应用。

###将应用连接到 Mobile Engagement 后端

1. 右键单击“解决方案”窗口中的“**程序包**”文件夹并选择“**添加程序包...**”

    ![][5]

2. 搜索 **Microsoft Azure Mobile Engagement Xamarin SDK** 并将其添加到你的解决方案。

    ![][6]
   
3. 打开 **AppDelegate.cs**，添加以下 using 语句：

		using Microsoft.Azure.Engagement.Xamarin;

4. 在 **FinishedLaunching** 方法中，添加以下代码来初始化与 Mobile Engagement 后端的连接。请确保添加 **ConnectionString**。此代码还使用一个由 Mobile Engagement SDK 添加的虚拟 **NotificationIcon**，你可能需要将它替换掉。

		EngagementConfiguration config = new EngagementConfiguration {
		                ConnectionString = "YourConnectionStringFromAzurePortal",
		                NotificationIcon = UIImage.FromBundle("close")
		            };
	    EngagementAgent.Init (config);

##<a id="monitor"></a>启用实时监视

为了开始发送数据并确保用户处于活动状态，必须将至少一个屏幕发送到 Mobile Engagement 后端。

1. 打开 **ViewController.cs**，添加以下 using 语句：

		using Microsoft.Azure.Engagement.Xamarin;

2. 将 `ViewController` 继承自的类从 `UIViewController` 替换为 `EngagementViewController`。

##<a id="monitor"></a>将应用与实时监视相连接

[AZURE.INCLUDE [将应用与实时监视相连接](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>启用推送通知和应用内消息传送

在市场推广活动中，可以使用 Mobile Engagement 通过推送通知和应用内消息传送与 REACH 模块和用户进行交互。在 Mobile Engagement 门户中，此模块称为 REACH。以下各部分将介绍如何设置应用以接收推送通知。

### 修改你的应用程序代理

1. 打开 **AppDelegate.cs**，添加以下 using 语句：

		using System; 

2. 接着，在 `FinishedLaunching` 方法中，在 `EngagementAgent.init(...)` 后面添加以下代码来注册推送消息

		if (UIDevice.CurrentDevice.CheckSystemVersion(8,0))
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                (UIUserNotificationType.Badge |
                    UIUserNotificationType.Sound |
                    UIUserNotificationType.Alert),
                null);
            UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        }
        else
        {
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (
                UIRemoteNotificationType.Badge |
                UIRemoteNotificationType.Sound |
                UIRemoteNotificationType.Alert);
        }

3. 最后，更新或添加以下方法︰

		public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, 
            Action<UIBackgroundFetchResult> completionHandler)
        {
            EngagementAgent.ApplicationDidReceiveRemoteNotification(userInfo, completionHandler);
        }

        public override void RegisteredForRemoteNotifications (UIApplication application, NSData deviceToken)
        {
            // Register device token on Engagement
            EngagementAgent.RegisterDeviceToken(deviceToken);
        }

        public override void FailedToRegisterForRemoteNotifications(UIApplication application, NSError error)
        {
            Console.WriteLine("Failed to register for remote notifications: Error '{0}'", error);
        }

4. 在解决方案的 **Info.plist** 文件中，请确认“**捆绑标识符**”是否与 Apple 开发人员中心中的预配配置文件中的“**应用 ID**”完全匹配。

	![][7]

5. 在同一个 **Info.plist** 文件中，确保你已复选“**启用后台模式**”和“**远程通知**”。

 	![][8]

6. 在与此发布配置文件关联的设备上运行该应用。

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[1]: ./media/mobile-engagement-xamarin-ios-get-started/new-solution.png
[2]: ./media/mobile-engagement-xamarin-ios-get-started/app-type.png
[3]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-name.png
[4]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-confirm.png
[5]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget.png
[6]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget-azme.png
[7]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-confirm-bundle.png
[8]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-configure-push.png

<!---HONumber=AcomDC_0921_2016-->