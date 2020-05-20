---
title: include 文件
description: include 文件
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 11/07/2019
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 48907713082ebb1008ad963121671b36af7f2731
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "74228158"
---
本部分说明如何从 .NET 控制台应用以标记模板通知的形式发送突发新闻。

1. 在 Visual Studio 中创建新的 Visual C# 控制台应用程序：
    1. 在菜单中，选择“文件”   > “新建”   > “项目”  。
    1. 在“创建新项目”中，选择模板列表中适用于 C# 的“控制台应用(.NET Framework)”，然后选择“下一步”。   
    1. 输入应用程序的名称。
    1. 对于“解决方案”  ，选择“添加到解决方案”  ，然后选择“创建”  以创建项目。

1. 选择“工具” > “NuGet 包管理器” > “包管理器控制台”，然后在控制台窗口中运行以下命令：

   ```powershell
   Install-Package Microsoft.Azure.NotificationHubs
   ```

   此操作会使用 [Microsoft.Azure.NotificationHubs] 包添加对 Azure 通知中心 SDK 的引用。

1. 打开文件 *Program.cs* 并添加以下 `using` 语句：

   ```csharp
   using Microsoft.Azure.NotificationHubs;
   ```

1. 在 `Program` 类中，添加以下方法，或替换此方法（如果已存在）：

    ```csharp
    private static async void SendTemplateNotificationAsync()
    {
        // Define the notification hub.
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");

        // Apple requires the apns-push-type header for all requests
        var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};

        // Create an array of breaking news categories.
        var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};

        // Send the notification as a template notification. All template registrations that contain
        // "messageParam" and the proper tags will receive the notifications.
        // This includes APNS, GCM/FCM, WNS, and MPNS template registrations.

        Dictionary<string, string> templateParams = new Dictionary<string, string>();

        foreach (var category in categories)
        {
            templateParams["messageParam"] = "Breaking " + category + " News!";
            await hub.SendTemplateNotificationAsync(templateParams, category);
        }
    }
    ```

   此代码将针对字符串数组中的所有 6 个标记发送模板通知。 使用标记是为了确保设备仅接收已注册类别的通知。

1. 在前面的代码中，将 `<hub name>` 和 `<connection string with full access>` 占位符替换为通知中心名称和从通知中心仪表板获取的 *DefaultFullSharedAccessSignature* 的连接字符串。

1. 在 `Main()` 方法中添加以下行：

   ```csharp
    SendTemplateNotificationAsync();
    Console.ReadLine();
    ```

1. 生成控制台应用。

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Get started with Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs REST interface]: https://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Add push notifications for Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Microsoft.Azure.NotificationHubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
