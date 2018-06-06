---
title: include 文件
description: include 文件
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/30/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 19352df7abff23ed44521a11e7907c84c8c0327f
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "33835811"
---
本部分说明如何从 .NET 控制台应用以标记模板通知的形式发送突发新闻。 

1. 在 Visual Studio 中创建新的 Visual C# 控制台应用程序：
   
      ![“控制台应用程序”链接][13]

2. 在 Visual Studio 主菜单中，选择“工具” > “库程序包管理器” > “程序包管理器控制台”，并在控制台窗口中输入以下字符串：
   
        Install-Package Microsoft.Azure.NotificationHubs
   
3. 按 **Enter**。  
    此操作会使用 [Microsoft.Azure.Notification Hubs NuGet 包]添加对 Azure 通知中心 SDK 的引用。

4. 打开文件 Program.cs 并添加以下 `using` 语句：
   
    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

5. 在 `Program` 类中，添加以下方法，或替换此方法（如果已存在）：
   
    ```csharp
    private static async void SendTemplateNotificationAsync()
    {
        // Define the notification hub.
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");

        // Create an array of breaking news categories.
        var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};

        // Send the notification as a template notification. All template registrations that contain
        // "messageParam" and the proper tags will receive the notifications.
        // This includes APNS, GCM, WNS, and MPNS template registrations.

        Dictionary<string, string> templateParams = new Dictionary<string, string>();

        foreach (var category in categories)
        {
            templateParams["messageParam"] = "Breaking " + category + " News!";
            await hub.SendTemplateNotificationAsync(templateParams, category);
        }
    }
    ```   
   
    此代码将针对字符串数组中的所有 6 个标记发送模板通知。 使用标记是为了确保设备仅接收已注册类别的通知。

5. 在前面的代码中，将 `<hub name>` 和 `<connection string with full access>` 占位符替换为通知中心名称和从通知中心仪表板获取的 *DefaultFullSharedAccessSignature* 的连接字符串。

6. 在 **Main** 方法中添加以下行：
   
    ```csharp
    SendTemplateNotificationAsync();
    Console.ReadLine();
    ```

7. 生成控制台应用。

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Get started with Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs REST interface]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Add push notifications for Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Microsoft.Azure.Notification Hubs NuGet 包]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
