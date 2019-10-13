---
title: 使用 Azure 通知中心向用户发送跨平台通知 (ASP.NET)
description: 了解如何使用通知中心模板在单个请求中发送针对所有平台的平台未知通知。
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 11d2131b-f683-47fd-a691-4cdfc696f62b
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 09/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: 8f4de88ed79ee802866579448681cfe6cee3e654
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2019
ms.locfileid: "72293427"
---
# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>使用通知中心向用户发送跨平台通知

本教程基于上一教程，[使用 Azure 通知中心向特定用户发送通知]。 本教程介绍如何将通知推送到已注册到特定身份验证用户的所有设备。 这种方法需要多个请求将通知发送到每个支持的客户端平台。 Azure 通知中心支持模板，可以通过模板指定特定设备要如何接收通知。 此方法简化了发送跨平台通知的方式。

本文演示如何利用模板发送面向所有平台的通知。 本文使用单个请求发送平台中立通知。 有关模板的更多详细信息，请参阅[通知中心概述][Templates]。

> [!IMPORTANT]
> Visual Studio 2019 不支持 Windows Phone 项目8.1 及更早版本。 有关详细信息，请参阅[Visual Studio 2019 平台目标和兼容性](/visualstudio/releases/2019/compatibility)。

> [!NOTE]
> 使用通知中心时，设备可以通过使用同一标记注册多个模板。 在这种情况下，以标记为目标的传入消息会导致向设备发送多个通知，每个通知对应一个模板。 此过程可以在多个可视通知中显示同一消息，如显示为 Windows 应用商店应用中的徽章和 toast 通知。

## <a name="send-cross-platform-notifications-using-templates"></a>使用模板发送跨平台通知

本部分使用[使用 Azure 通知中心向特定用户发送通知]教程中生成的示例代码。 可以从 [GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers) 下载该示例。

若要使用模板发送跨平台通知，请执行以下步骤：

1. 在**解决方案资源管理器**中的 Visual Studio 中，展开 "**控制器**" 文件夹，然后打开*RegisterController.cs*文件。

1. 在 `Put` 方法中找到用于创建新注册的代码块，然后将 `switch` 内容替换为以下代码：

    ```csharp
    switch (deviceUpdate.Platform)
    {
        case "mpns":
            var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                    "<wp:Toast>" +
                        "<wp:Text1>$(message)</wp:Text1>" +
                    "</wp:Toast> " +
                "</wp:Notification>";
            registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "wns":
            toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
            registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "apns":
            var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
            registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
            break;
        case "fcm":
            var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
            registration = new FcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }
    ```

    此代码调用平台特定的方法来创建模板注册而非本机注册。 由于模板注册派生自本机注册，因此无需修改现有注册。

1. 在**解决方案资源管理器**的 "**控制器**" 文件夹中，打开*NotificationsController.cs*文件。 将 `Post` 方法的代码替换为以下代码：

    ```csharp
    public async Task<HttpResponseMessage> Post()
    {
        var user = HttpContext.Current.User.Identity.Name;
        var userTag = "username:" + user;

        var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
        await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

    此代码将通知同时发送到所有平台。 不指定本机负载。 通知中心会构建正确的有效负载，并将其提供给每个设备，其中包含提供的标记值（在注册的模板中指定）。

1. 重新发布 Web API 项目。

1. 再次运行客户端应用，以验证注册是否已成功。

1. （可选）将客户端应用部署到第二个设备，然后运行应用。 通知会显示在每个设备上。

## <a name="next-steps"></a>后续步骤

完成本教程后，请在以下文章中了解有关通知中心和模板的详细信息：

* 有关使用模板的其他方案，请参阅将[通知推送到运行通用 Windows 平台应用程序的特定 Windows 设备][Use Notification Hubs to send breaking news]教程。
* 有关模板的更多详细信息，请参阅[通知中心概述][Templates]。

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: https://visualstudio.microsoft.com/downloads/

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: https://go.microsoft.com/fwlink/p/?LinkId=314257
[使用 Azure 通知中心向特定用户发送通知]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: https://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: https://msdn.microsoft.com/library/windowsazure/jj927172.aspx
