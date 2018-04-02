---
title: 适用于通用 Windows 平台应用的 Azure 通知中心入门 | Microsoft Docs
description: 在本教程中，将了解如何使用 Azure 通知中心将通知推送到通用 Windows 平台应用程序。
services: notification-hubs
documentationcenter: windows
author: jwhitedev
manager: kpiteira
editor: ''
ms.assetid: cf307cf3-8c58-4628-9c63-8751e6a0ef43
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 8464a7e48a02be39fb624322fac6c26f4c6c6806
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="get-started-with-notification-hubs-for-universal-windows-platform-apps"></a>适用于通用 Windows 平台应用的通知中心入门

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>概述
本文介绍如何使用 Azure 通知中心将推送通知发送到通用 Windows 平台 (UWP) 应用。

本文介绍如何创建一个空白 Windows 应用商店应用，该应用使用 Windows 推送通知服务 (WNS) 接收推送通知。 完成后，便可以使用通知中心将推送通知广播到运行应用的所有设备。

## <a name="before-you-begin"></a>开始之前
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

可以在 [GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/GetStartedWindowsUniversal) 上找到本教程的已完成代码。

## <a name="prerequisites"></a>先决条件
本教程需要的内容如下：

* [Microsoft Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs) 或更高版本
* [已安装 UWP 应用开发工具](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up)
* 一个有效的 Azure 帐户  
    如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-store-dotnet-get-started%2F)。
* 有效的 Windows 应用商店帐户

完成本教程是学习有关 UWP 应用的所有其他通知中心教程的先决条件。

## <a name="register-your-app-for-the-windows-store"></a>为 Windows 应用商店注册应用程序
要将推送通知发送到 UWP 应用，请将应用关联到 Windows 应用商店。 然后将通知中心配置为与 WNS 集成。

1. 如果尚未注册应用，请导航到 [Windows 开发人员中心](https://dev.windows.com/overview)，使用 Microsoft 帐户登录，并选择“创建新应用”。

2. 键入应用的名称，并选择“保留应用名称”。 这将为应用创建一个新的 Windows 应用商店注册。

3. 在 Visual Studio 中，使用 UWP“空白应用”模板来创建一个新的 Visual C# 应用商店应用项目，并选择“确定”。

4. 接受目标和最低平台版本的默认值。

5. 在“解决方案资源管理器”中，右键单击 Windows 应用商店应用项目，选择“应用商店”，然后选择“将应用与应用商店关联”。  
    此时会显示“将应用与 Windows 应用商店关联”向导。

6. 在向导中，使用 Microsoft 帐户登录。

7. 选择在第 2 步中注册的应用，选择“下一步”，然后选择“关联”。 这会将所需的 Windows 应用商店注册信息添加到应用程序清单中。

8. 回到新应用的 [Windows 开发人员中心](http://dev.windows.com/overview)页，依次选择“服务”、“推送通知”、“WNS/MPNS”。

9. 选择“新建通知”。

10. 选择“空白(Toast)”模板，然后选择“确定”。

11. 输入通知**名称**和 Visual **上下文**消息，然后选择“另存为草稿”。

12. 访问[应用程序注册门户](http://apps.dev.microsoft.com)并登录。

13. 选择应用程序名称。 在“Windows 应用商店”平台设置中，记下“应用程序机密”密码和“包安全标识符(SID)”。

    >[!WARNING]
    >应用程序机密和程序包 SID 是重要的安全凭据。 请勿将这些值告知任何人或随应用程序分发它们。

## <a name="configure-your-notification-hub"></a>配置通知中心
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li><p>在“Notification Services”<b></b>下选择“Windows (WNS)”<b></b>，然后在“安全密钥”<b></b>框中输入应用程序机密密码。 在“包 SID”框中，输入在之前部分从 WNS 获取的值，并选择“保存”。<b></b><b></b></p>
</li>
</ol>

![“包 SID”框和“安全密钥”框](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

通知中心现在已配置为使用 WNS。 你有了用于注册应用和发送通知的连接字符串。

## <a name="connect-your-app-to-the-notification-hub"></a>将应用连接到通知中心
1. 在 Visual Studio 中，右键单击该解决方案，并选择“管理 NuGet 包”。  
    此时会打开“管理 NuGet 包”窗口。

2. 在搜索框中输入 **Microsoft.Azure.NotificationHubs**，选择“安装”，并接受使用条款。
   
    ![“管理 NuGet 包”窗口][20]
   
    此操作使用 [Microsoft.Azure.NotificationHubs NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs)下载、安装并添加对 Azure 通知中心库的引用。

3. 打开 App.xaml.cs 项目文件并添加以下 `using` 语句： 
   
        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.UI.Popups;

4. 在 App.xaml.cs 中，另外将以下 **InitNotificationsAsync** 方法定义添加到 **App** 类中：
   
        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
   
            var hub = new NotificationHub("<your hub name>", "<Your DefaultListenSharedAccessSignature connection string>");
            var result = await hub.RegisterNativeAsync(channel.Uri);
   
            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
   
        }
   
    此代码从 WNS 检索应用的通道 URI，并将该通道 URI 注册到通知中心。
   
    >[!NOTE]
    >* 将“hub name”占位符替换为出现在 Azure 门户中的通知中心的名称。 
    >* 另外，使用在之前部分中从通知中心的“访问策略”页获取的 **DefaultListenSharedAccessSignature** 连接字符串替换连接字符串占位符。
   > 
   > 
5. 在 App.xaml.cs 中 **OnLaunched** 事件处理程序的上方，添加对新 **InitNotificationsAsync** 方法的以下调用：
   
        InitNotificationsAsync();
   
    此操作保证每次启动应用程序时都在通知中心注册通道 URI。

6. 若要运行应用，请选择 **F5** 键。 此时会显示包含注册密钥的对话框。

应用现在已能够接收 toast 通知。

## <a name="send-notifications"></a>发送通知
可以通过在 [Azure 门户](https://portal.azure.com/)中发送通知来快速测试在应用中接收通知。 使用通知中心中的“测试发送”按钮，如下图所示：

![“测试发送”窗格](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)

通常，推送通知是在移动服务或 ASP.NET 等后端服务中使用兼容库发送的。 如果后端没有可用的库，则也可以直接使用 REST API 发送通知消息。 

本教程演示如何简单地通过在控制台应用程序（而不是后端服务）中为通知中心发送使用 .NET SDK 的通知，来测试客户端应用。 建议接下来学习[使用通知中心向用户推送通知]教程，以了解如何从 ASP.NET 后端发送通知。 但是，可以通过使用以下方法发送通知：

* **REST 接口**：可以使用 [REST 接口](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx)在任何后端平台上支持通知。

* **Microsoft Azure 通知中心 .NET SDK**：在 Visual Studio 的 NuGet 包管理器中，运行 [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)。

* **Node.js**：请参阅[如何通过 Node.js 使用通知中心](notification-hubs-nodejs-push-notification-tutorial.md)。
* **Azure 移动应用**：有关如何从通知中心集成的 Azure 移动应用发送通知的示例，请参阅[为移动应用添加推送通知](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md)。

* **Java 或 PHP**：有关如何使用 REST API 发送通知的示例，请参阅：
    * [Java](notification-hubs-java-push-notification-tutorial.md)
    * [PHP](notification-hubs-php-push-notification-tutorial.md)

## <a name="next-steps"></a>后续步骤
这个简单示例使用门户或控制台应用将广播通知发送到所有 Windows 设备。 建议下一步学习[使用通知中心向用户推送通知]教程。 它演示了如何使用标记从 ASP.NET 后端将通知发送到目标特定的用户。

如果要按兴趣组划分用户，可以阅读 [使用通知中心发送突发新闻]。 

有关通知中心的更多常规信息，请参阅[通知中心指南](notification-hubs-push-notification-overview.md)。

<!-- Images. -->
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->

[使用通知中心向用户推送通知]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[使用通知中心发送突发新闻]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md

[toast catalog]: http://msdn.microsoft.com/library/windows/apps/hh761494.aspx
[tile catalog]: http://msdn.microsoft.com/library/windows/apps/hh761491.aspx
[badge overview]: http://msdn.microsoft.com/library/windows/apps/hh779719.aspx
 
