---
title: "适用于 Windows Phone 应用的 Azure 通知中心入门 | Microsoft Docs"
description: "在本教程中，将了解如何使用 Azure 通知中心将通知推送到 Windows Phone 8 或 Windows Phone 8.1 Silverlight 应用程序。"
services: notification-hubs
documentationcenter: windows
keywords: "推送通知,push notification,windows phone 推送"
author: jwhitedev
manager: kpiteira
editor: 
ms.assetid: d872d8dc-4658-4d65-9e71-fa8e34fae96e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/06/2018
ms.author: jawh
ms.openlocfilehash: 38d60001293a3bae6eb9f90179abb8af7815fbe7
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="get-started-with-azure-notification-hubs-for-windows-phone-apps"></a>适用于 Windows Phone 应用的 Azure 通知中心入门
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>概述
> [!NOTE]
> 要完成本教程，必须有一个有效的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-phone-get-started%2F)。
> 
> 

本教程演示如何使用 Azure 通知中心将推送通知发送到 Windows Phone 8 或 Windows Phone 8.1 Silverlight 应用程序。 如果要以 Windows Phone 8.1（非 Silverlight）为目标，请参阅 [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) 版本。
在本教程中，将创建一个空白 Windows Phone 8 应用，它使用 Microsoft 推送通知服务 (MPNS) 接收推送通知。 完成后，能够使用通知中心将推送通知广播到运行应用的所有设备。

> [!NOTE]
> 通知中心 Windows Phone SDK 不支持将 Windows 推送通知服务 (WNS) 与 Windows Phone 8.1 Silverlight 应用配合使用。 要将 WNS（而不是 MPNS）与 Windows Phone 8.1 Silverlight 应用配合使用，请遵循使用 REST API 的 [通知中心 - Windows Phone Silverlight 教程]。
> 
> 

本教程演示使用通知中心的简单广播方案。

## <a name="prerequisites"></a>先决条件
本教程需要的内容如下：

* [Visual Studio 2012 Express for Windows Phone]或更高版本。

完成本教程是学习有关 Windows Phone 8 应用的所有其他通知中心教程的先决条件。

## <a name="create-your-notification-hub"></a>创建通知中心
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li><p>在“Notification Services”下选择“Windows Phone (MPNS)”，然后单击“启用未经身份验证的推送”复选框。<b></b><b></b><b></b></p>
</li>
</ol>

&emsp;&emsp;![Azure 门户 - 启用未经身份验证的推送通知](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

中心现已创建，并已配置为向 Windows Phone 发送未经身份验证的通知。

> [!NOTE]
> 本教程使用未经身份验证模式下的 MPNS。 MPNS 未经身份验证的模式对可以发送到每个通道的通知有一些限制。 通知中心支持 [MPNS 身份验证模式](http://msdn.microsoft.com/library/windowsphone/develop/ff941099.aspx)，它允许上传证书。
> 
> 

## <a name="connecting-your-app-to-the-notification-hub"></a>将应用程序连接到通知中心
1. 在 Visual Studio 中创建一个新的 Windows Phone 8 应用程序。
   
    ![Visual Studio - 新建项目 - Windows Phone 应用][13]
   
    在 Visual Studio 2013 Update 2 或更高版本中，必须改为创建 Windows Phone Silverlight 应用程序。
   
    ![Visual Studio - 新建项目 - 空白应用 - Windows Phone Silverlight][11]
2. 在 Visual Studio 中，右键单击该解决方案，并单击“管理 NuGet 包”。
   
    此时会显示“管理 NuGet 包”对话框。
3. 搜索 `WindowsAzure.Messaging.Managed`，单击“安装”，并接受使用条款。
   
    ![Visual Studio - NuGet 包管理器][20]
   
    此时会使用 <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">WindowsAzure.Messaging.Managed NuGet 包</a>下载、安装并添加对 Windows 的 Azure 消息传送库的引用。
4. 打开文件 App.xaml.cs 并添加以下 `using` 语句：
   
        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;
5. 在 App.xaml.cs 中 **Application_Launching** 方法的顶部添加以下代码：
   
        var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }
   
        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            var result = await hub.RegisterNativeAsync(args.ChannelUri.ToString());
   
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
        });
   
   > [!NOTE]
   > 值 **MyPushChannel** 是用于查找 [HttpNotificationChannel](https://msdn.microsoft.com/library/windows/apps/microsoft.phone.notification.httpnotificationchannel.aspx) 集合中现有通道的索引。 如果不存在，则使用该名称创建新条目。
   > 
   > 
   
    确保插入中心名称以及在前一部分中获取的名为 **DefaultListenSharedAccessSignature** 的连接字符串。
    此代码从 MPNS 检索应用的通道 URI，然后将该通道 URI 注册到通知中心。 它还保证每次启动应用程序时都在通知中心注册通道 URI。
   
   > [!NOTE]
   > 本教程会一个 toast 通知发送到设备。 而发送磁贴通知时，必须在通道上调用 **BindToShellTile** 方法。 若要同时支持 toast 通知和磁贴通知，请同时调用 **BindToShellTile** 和  **BindToShellToast**。
   > 
   > 
6. 在解决方案资源管理器中，展开“属性”，打开 `WMAppManifest.xml` 文件，单击“功能”选项卡并确保选中 **ID_CAP_PUSH_NOTIFICATION** 功能。
   
    ![Visual Studio - Windows Phone 应用功能][14]
   
    这样可确保应用程序可收到推送通知。 如果没有它，则将无法成功向应用发送推送通知。
7. 按 `F5` 键以运行应用。
   
    随后应用中会显示注册消息。
8. 关闭应用。  
   
   > [!NOTE]
   > 若要接收 toast 推送通知，则应用程序不得在前台运行。
   >

## <a name="next-steps"></a>后续步骤
在这个简单的示例中，已将推送通知广播到所有 Windows Phone 8 设备。 

要针对特定客户，请参考教程 [使用通知中心将通知推送到用户]。 

如果要按兴趣组划分用户，可以阅读 [使用通知中心发送突发新闻]。 

在 [通知中心指南]中了解有关如何使用通知中心的更多信息。

<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[通知中心指南]: http://msdn.microsoft.com/library/jj927170.aspx
[MPNS authenticated mode]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[使用通知中心将通知推送到用户]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[使用通知中心发送突发新闻]: notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md
[toast catalog]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[tile catalog]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[通知中心 - Windows Phone Silverlight 教程]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp

