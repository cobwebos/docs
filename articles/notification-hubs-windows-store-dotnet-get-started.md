<properties linkid="develop-notificationhubs-tutorials-get-started-windowsdotnet" urlDisplayName="通知中心入门" pageTitle="Azure 通知中心入门" metaKeywords="" description="了解如何使用 Azure 通知中心推送通知。" metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="通知中心入门" authors="elioda" solutions="" manager="" editor="" />

# 通知中心入门

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-cn/manage/services/notification-hubs/getting-started-windows-dotnet" title="Windows 应用商店 C#" class="current">Windows 应用商店 C#</a><a href="/zh-cn/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/zh-cn/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/zh-cn/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/zh-cn/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/zh-cn/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/zh-cn/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

本主题演示如何使用 Azure 通知中心将推送通知发送到 Windows 应用商店应用程序。
在本教程中，您将创建一个空白 Windows 应用商店应用程序，它使用 Windows 推送通知服务 (WNS) 接收推送通知。完成后，您将能使用通知中心将推送通知广播到运行您的应用程序的所有设备。

本教程将指导您完成启用推送通知的以下基本步骤：

1. [为推送通知注册应用程序]
2. [配置通知中心]
3. [将您的应用程序连接到通知中心]
4. [从后端发送通知]

本教程演示使用通知中心的简单广播方案。请确保随后学习下一教程以了解如何使用通知中心来发送到特定用户和设备组。本教程需要的内容如下：

+ Microsoft Visual Studio 2012 Express for Windows 8
+ 有效的 Windows 应用商店帐户

完成本教程是学习有关 Windows 应用商店应用程序的所有其他通知中心教程的先决条件。

<div class="dev-callout"><strong>注意</strong> <p>要完成本教程，您必须有一个有效的 Windows Azure 帐户。如果您没有帐户，则可以创建一个免费的试用帐户，只需几分钟即可完成。有关详细信息，请参见 <a href="http://www.windowsazure.com/zh-cn/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 免费试用</a>。</p></div>

<h2><a name="register"></a><span class="short-header">注册应用程序</span>向 Windows 应用商店注册应用程序</h2>

要从移动服务将推送通知发送到 Windows 应用商店应用程序，您必须将您的应用程序提交到 Windows 应用商店。然后必须将通知中心配置为与 WNS 集成。

1. 如果尚未注册应用程序，则导航到 Windows 应用商店应用程序的开发人员中心内的[“提交应用程序”页]，用 Microsoft 帐户登录，然后单击“应用程序名称”。

   	![][0]

2. 在“应用程序名称”中为应用程序键入一个名称，单击“保留应用程序名称”，然后单击“保存”。

   	![][1]

   	此操作为应用程序创建一个新的 Windows 应用商店注册。

3. 在 Visual Studio 2012 Express for Windows 8 中，使用“空白应用程序”模板创建新的 Visual C# Windows 应用商店项目。

   	![][2]

4. 在解决方案资源管理器中，右键单击项目，单击“应用商店”，然后单击“将应用程序与应用商店关联...”。

   	![][3]

   	将显示“将应用程序与 Windows 应用商店关联”向导。

5. 在该向导中，单击“登录”，然后用您的 Microsoft 帐户登录。

6. 单击在第 2 步中注册的应用程序，单击“下一步”，然后单击“关联”。

   	![][4]

   	这会将所需的 Windows 应用商店注册信息添加到应用程序清单中。

7. 回到新应用程序的“Windows 开发人员中心”页，单击“服务”。

   	![][5] 

8. 在“服务” 页中，单击“Azure 移动服务”下的“Live 服务站点”。

   	![][17]

9. 单击“验证服务”，然后记下“客户端密钥”和“程序包安全标识符(SID)”的值。

   	![][6]

 	<div class="dev-callout"><b>安全说明</b>
	<p>客户端密钥和程序包 SID 是重要的安全凭据。请勿将这些值告知任何人或随您的应用程序分发它们。</p>
    </div>

<h2><a name="configure-hub"></a><span class="short-header">配置通知中心</span>配置通知中心</h2>

1. 登录到 [Azure 管理门户]，然后单击屏幕底部的“新建”。

2. 依次单击“应用程序服务”、**Service Bus**、“通知中心”和“快速创建”。

   	![][7]

3. 键入通知中心的名称，选择所需的区域，然后单击“创建新的通知中心”。

   	![][8]

4. 单击刚刚创建的命名空间（通常为 ***通知中心名称*-ns**），然后单击顶部的“配置”选项卡。

   	![][9]

5. 选择顶部的“通知中心”选项卡，然后单击您刚刚创建的通知中心。

   	![][10]

6. 选择顶部的“配置”选项卡，输入您在前一节中从 WNS 获取的“客户端密钥”和“程序包 SID”值，然后单击“保存”。

   	![][11]

7. 选择顶部的“仪表板”选项卡，然后单击“连接信息”。记下两个连接字符串。

   	![][12]

您的通知中心现在已配置为使用 WNS，并且您有连接字符串用于注册您的应用程序和发送通知。

<h2><a name="connecting-app"></a><span class="short-header">连接您的应用程序</span>将您的应用程序连接到通知中心</h2>

1. 使用 <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">WindowsAzure.Messaging.Managed NuGet 包</a>添加对 Windows 应用商店的 Azure 消息传递库的引用。在 Visual Studio 主菜单中，依次单击“工具”、“库程序包管理器”和“程序包管理器控制台”。然后，在控制台窗口中键入：

        Install-Package WindowsAzure.Messaging.Managed

    并按 **Enter**。

2. 打开文件 App.xaml.cs 并添加以下 `using` 语句：

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
		using Windows.UI.Popups;

3. 在`App` 类中将以下代码添加到 App.xaml.cs。确保将“中心名称”占位符替换为在门户的“通知中心”选项卡中显示的通知中心名称（如上例中的 **mynotificationhub2**）：
	
	    private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
			
            var hub = new NotificationHub("<hub name>", "<connection string with listen access>");              
			var result = await hub.RegisterNativeAsync(channel.Uri);
            
            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }

        }

    确保插入您的中心名称以及在前一节中获取的名为 **DefaultListenSharedAccessSignature** 的连接字符串。
    此代码从 WNS 检索应用程序的 ChannelURI，然后将该 ChannelURI 注册到您的通知中心。
    
4. 在 App.xaml.cs 中 **OnLaunched** 事件处理程序的上方，添加对新 **InitNotificationsAsync** 方法的以下调用：

        InitNotificationsAsync();

    这保证每次启动应用程序时都在通知中心中注册 ChannelURI。

5. 按 **F5** 键以运行应用程序。将显示包含注册密钥的弹出式对话框。
   
   	![][19]

<h2><a name="send"></a><span class="short-header">发送通知</span>从后端发送通知</h2>

您可以使用通知中心通过 <a href="http://msdn.microsoft.com/zh-cn/library/windowsazure/dn223264.aspx">REST 接口</a>从任意后端发送通知。在本教程中，您使用 .NET 控制台应用程序和移动服务来发送通知，通过节点脚本来执行这些操作。

使用 .NET 应用程序发送通知：

1. 仍使以前的解决方案在 Visual Studio 中处于打开状态，在解决方案资源管理器中双击 **Package.appxmanifest** 以在 Visual Studio 编辑器中将其打开。

2. 向下滚动至“所有图像资产”，然后单击“徽章徽标”。在“通知”中，将“支持 Toast 通知”设置为“是”：

   	![][18]

   	从“文件”菜单中，单击“全部保存”。

3. 现在创建新的 Visual C# 控制台应用程序：

   	![][13]

4. 通过使用 <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet 包</a>添加对 Azure Service Bus SDK 的引用。在 Visual Studio 主菜单中，依次单击“工具”、“库程序包管理器”和“程序包管理器控制台”。然后，在控制台窗口中键入：

        Install-Package WindowsAzure.ServiceBus

    并按 **Enter**。

5. 打开文件 Program.cs 并添加以下 `using` 语句：

        using Microsoft.ServiceBus.Notifications;

6. 在 `Program` 类中，添加以下方法。确保将“中心名称”占位符替换为在门户的“通知中心”选项卡中显示的通知中心名称：

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
            await hub.SendWindowsNativeNotificationAsync(toast);
        }

   	确保插入您的中心名称以及在“配置通知中心”一节中获取的名为 **DefaultFullSharedAccessSignature** 的连接字符串。请注意这是具有“完全”访问权限（而非“侦听”访问权限）的连接字符串。

7. 然后在 `Main` 方法中添加下列行：

         SendNotificationAsync();
		 Console.ReadLine();

8. 按 **F5** 键以运行应用程序。您应接收 toast 通知。

   	![][14]

您可以在 MSDN 上的 [toast 目录]、[磁贴目录]和[徽章概述]中查看所有可能的负载。

要使用移动服务发送通知，请按[移动服务入门]中的说明操作，然后执行以下操作：

1. 登录到 [Azure 管理门户]，并单击您的移动服务。

2. 选择顶部的“计划程序”选项卡。

   	![][15]

3. 创建新的计划作业，插入名称，然后单击“按需”。

   	![][16]

4. 创建作业时，单击该作业名称。然后单击顶部栏上的“脚本”选项卡。

5. 在您的计划程序函数中插入以下脚本。确保将占位符替换为您的通知中心名称和您以前获取的 *DefaultFullSharedAccessSignature* 的连接字符串。完成后，单击底部栏上的“保存”。

        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
        notificationHubService.wns.sendToastText01(
            null,
            {
                text1: 'Hello from Mobile Services!!!'
            },
            function (error) {
                if (!error) {
                    console.warn("Notification successful");
                }
        });

6. 单击底部栏上的“运行一次”。您应接收 toast 通知。

## <a name="next-steps"> </a>后续步骤

在这个简单的示例中，您将通知广播到所有 Windows 设备。为了针对特定客户，请参考教程[使用通知中心将通知推送到用户]。如果要按兴趣细分用户组，可以阅读[使用通知中心发送突发新闻]。要了解有关如何使用通知中心的更多信息，请参见[通知中心指南]和[针对 Windows 应用商店的通知中心操作指南]。

<!-- Anchors. -->
[为推送通知注册应用程序]: #register
[配置通知中心]: #configure-hub
[将您的应用程序连接到通知中心]: #connecting-app
[从后端发送通知]: #send
[后续步骤]:#next-steps

<!-- Images. -->
[0]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-submit-win8-app.png
[1]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-name.png
[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-win8-app.png
[3]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-associate-win8-app.png
[4]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-select-app-name.png
[5]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit-app.png
[6]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-push-auth.png
[7]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png
[12]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-connection-strings.png
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[15]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-scheduler2.png
[17]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit2-app.png
[18]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-win8-app-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png

<!-- URLs. -->
[“提交应用程序”页]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[我的应用程序]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[移动服务入门]: /zh-cn/develop/mobile/tutorials/get-started/#create-new-service
[开始使用数据]: /zh-cn/develop/mobile/tutorials/get-started-with-data-dotnet
[身份验证入门]: /zh-cn/develop/mobile/tutorials/get-started-with-users-dotnet
[推送通知入门]: /zh-cn/develop/mobile/tutorials/get-started-with-push-dotnet
[向应用程序用户推送通知]: /zh-cn/develop/mobile/tutorials/push-notifications-to-users-dotnet
[使用脚本为用户授权]: /zh-cn/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript 和 HTML]: /zh-cn/develop/mobile/tutorials/get-started-with-push-js

[Azure 管理门户]: https://manage.windowsazure.com/
[wns 对象]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[通知中心指南]: http://msdn.microsoft.com/zh-cn/library/jj927170.aspx
[针对 Windows 应用商店的通知中心操作指南]: http://msdn.microsoft.com/zh-cn/library/jj927172.aspx

[使用通知中心将通知推送到用户]: /zh-cn/manage/services/notification-hubs/notify-users-aspnet
[使用通知中心发送突发新闻]: /zh-cn/manage/services/notification-hubs/breaking-news-dotnet

[toast 目录]: http://msdn.microsoft.com/zh-cn/library/windows/apps/hh761494.aspx
[磁贴目录]: http://msdn.microsoft.com/zh-cn/library/windows/apps/hh761491.aspx
[徽章概述]: http://msdn.microsoft.com/zh-cn/library/windows/apps/hh779719.aspx

