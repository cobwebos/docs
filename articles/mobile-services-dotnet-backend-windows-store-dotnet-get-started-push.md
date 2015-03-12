<properties pageTitle="推送通知中心与 .NET 运行时移动服务用法入门" metaKeywords="" description="了解如何使用 Azure 移动服务和通知中心将推送通知发送到 Windows 应用商店应用程序。" metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc,ricksal" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc,ricksal" />


# 向移动服务应用程序添加推送通知

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

本主题说明如何结合使用 Azure 移动服务和 .NET 后端向通用 Windows 应用程序发送推送通知。在本教程中，你将要使用 Azure 通知中心为通用 Windows 快速入门项目启用推送通知。完成本教程后，每次插入一条记录时，你的移动服务就会使用通知中心从 .NET 后端发送一条推送通知。创建的通知中心可在移动服务中任意使用，可独立于移动服务进行管理，并可供其他应用程序和服务使用。

>[WACOM.NOTE]本主题说明如何使用适用于 Windows 应用商店应用程序的 Windows 通知服务 (WNS) 手动配置推送通知。可以使用 Visual Studio 2013 工具在 Windows 应用程序项目中自动配置相同的推送通知。有关详细信息，请参阅本教程的[通用 Windows 应用程序版本](/zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-push)。

本教程将指导你完成启用推送通知的以下基本步骤：

1. [将应用程序注册到 WNS 并配置移动服务](#register)
2. [更新应用程序以注册通知](#update-app)
3. [更新服务器以发送推送通知](#update-server)
4. [启用推送通知以进行本地测试](#local-testing)
5. [插入数据以接收推送通知](#test)

本教程基于移动服务快速入门。在开始学习本教程之前，必须先完成[移动服务入门]或[数据处理入门]，以将项目连接到移动服务。 

>[WACOM.NOTE]本主题支持 Windows Phone 应用商店 8.1 应用程序。若要将推送通知添加到 Windows Phone 8 或 Windows Phone Silverlight 8.1 应用程序，请参阅此版本的[移动服务中的推送通知入门](/zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push)。

## <a id="register"></a>将应用程序注册到 WNS 并配置移动服务

[WACOM.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

现在，你的移动服务和应用程序都已配置为使用 WNS 和通知中心。接下来，你要更新通用 Windows 应用程序，以注册通知。

## <a id="update-app"></a>更新应用程序以注册通知

只有在你注册通知通道后，你的应用程序才能接收推送通知。

1. 在 Visual Studio 中，打开文件 App.xaml.cs 并添加以下 `using` 语句：

        using Windows.Networking.PushNotifications;
		using Windows.UI.Popups;

2. 将以下 `InitNotificationAsync` 方法添加到 **App** 类，以创建推送通知通道并注册推送通知： 
	
        private async void InitNotificationsAsync()
        {
            // Request a push notification channel.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register for notifications using the new channel
            System.Exception exception = null;
            try
            {
                await MobileService.GetPush().RegisterNativeAsync(channel.Uri);
            }
            catch (System.Exception ex)
            {
                exception = ex;
            }
            if (exception != null)
            {
                var dialog = new MessageDialog(exception.Message, "Registering Channel URI");
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }


    此代码从 WNS 检索应用程序的 ChannelURI，然后注册该 ChannelURI，以便将其用于推送通知。
    
3. 在 App.xaml.cs 中 **OnLaunched** 事件处理程序的上方，添加对新 **InitNotificationsAsync** 方法的以下调用：

        InitNotificationsAsync();

	这可以确保每次加载页时都会请求注册。在应用程序中，你可能只需要定期执行此注册以确保注册是最新的。 

4. 在 Visual Studio 中，打开 Package.appxmanifest 文件，并确保"应用程序 UI"选项卡上的"支持 Toast 通知"已设置为"是"。保存文件。

   	![][1]

   	这可以确保你的应用程序能够引发 toast 通知。 

## <a id="update-server"></a>更新服务器以发送推送通知

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

## <a id="local-testing"></a>启用推送通知以进行本地测试

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

## <a id="test"></a>在应用程序中测试推送通知

[WACOM.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>后续步骤

本教程演示了有关如何使 Windows 应用商店应用使用移动服务和通知中心发送推送通知的基础知识。建议你接下来完成下一篇教程[向经过身份验证的用户发送推送通知]，其中说明了如何使用标记来做到只将推送通知从移动服务发送到经过身份验证的用户。

<!--+ [Send push notifications to authenticated users]
	<br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send broadcast notifications to subscribers]
	<br/>Learn how users can register and receive push notifications for categories they're interested in.

+ [Send template-based notifications to subscribers]
	<br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end.
-->
通过以下主题了解有关移动服务和通知中心的详细信息：

* [数据处理入门][Get started with data]
  <br/>了解有关使用移动服务存储和查询数据的详细信息。

* [身份验证入门][Get started with authentication]
  <br/>了解如何通过移动服务对使用不同帐户类型的应用程序用户进行身份验证。

* [什么是通知中心？][What are Notification Hubs?]
  <br/>详细了解通知中心如何将通知传送到各个主要客户端平台中的应用程序。

* [调试通知中心应用程序](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>获取有关对通知中心解决方案进行故障排除和调试的指导。 

* [移动服务 .NET 操作方法概念性参考]
  <br/>了解有关如何将移动服务与 .NET 一起使用的详细信息。

<!-- Anchors. -->

<!-- Images. -->


[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/enable-toast.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/mobile-quickstart-push1.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
["提交应用"页]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[我的应用程序]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[移动服务入门]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-store-get-started
[Get started with data]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
[Get started with authentication]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users



[What are Notification Hubs?]: /zh-cn/documentation/articles/notification-hubs-overview/
[向订户发送通知]: /zh-cn/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[向用户发送通知]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/


[移动服务 .NET 操作方法概念性参考]: /zh-cn/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
