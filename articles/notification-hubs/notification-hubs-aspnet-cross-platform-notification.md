---
title: "使用 Azure 通知中心向用户发送跨平台通知 (ASP.NET)"
description: "了解如何使用通知中心模板在单个请求中发送针对所有平台的平台未知通知。"
services: notification-hubs
documentationcenter: 
author: ysxu
manager: erikre
editor: 
ms.assetid: 11d2131b-f683-47fd-a691-4cdfc696f62b
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: 3c6dde338cb154f0cbe02642e4ff0f81d070aa25
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>使用通知中心向用户发送跨平台通知
在上一教程[使用通知中心通知用户]中，我们了解了如何向注册给经过身份验证的特定用户的所有设备推送通知。 在该教程中，需要使用多个请求将通知发送到每个支持的客户端平台。 Azure 通知中心支持模板，可以通过模板指定特定设备要如何接收通知。 此方法简化了发送跨平台通知的方式。 

本文演示了如何利用模板在单个请求中向所有平台发送不区分平台的通知。 有关模板的更多详细信息，请参阅 [Azure 通知中心概述][Templates]。

> [!IMPORTANT]
> Visual Studio 2017 不支持使用 Windows Phone 项目 8.1 及更早的版本。 有关详细信息，请参阅 [Visual Studio 2017 平台目标以及兼容性](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs)。

> [!NOTE]
> 通过通知中心，设备可使用同一标记注册多个模板。 在这种情况下，针对该标签的传入的邮件将导致系统向设备发送多个通知（每个通知对应一个模板）。 此过程可以在多个可视通知中显示同一消息，如显示为 Windows 应用商店应用中的徽章和 toast 通知。
> 
> 

若要使用模板发送跨平台通知，请执行以下步骤：

1. 在 Visual Studio 的解决方案资源管理器中，展开“控制器”文件夹，然后打开 RegisterController.cs 文件。

2. 在 Put 方法中查找用于创建新注册的代码块，然后将 `switch` 内容替换为以下代码：
   
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
            case "gcm":
                var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
                registration = new GcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }
   
    此代码调用平台特定的方法来创建模板注册而非本机注册。 由于模板注册派生自本机注册，因此无需修改现有注册。

3. 在 **Notifications** 控制器中，将 **sendNotification** 方法替换为以下代码：
   
        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:" + user;
   
            var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
            await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);
   
            return Request.CreateResponse(HttpStatusCode.OK);
        }
   
    此代码将通知同时发送到所有平台，而不必指定本机负载。 通知中心使用提供的*标记*值（在注册的模板中指定）生成正确的有效负载并将它传递到每个设备。

4. 重新发布 WebApi 后端项目。

5. 再次运行客户端应用并验证注册是否已成功。

6. （可选）将客户端应用部署到第二个设备，然后运行该应用。
    请注意通知会显示在每个设备上。

## <a name="next-steps"></a>后续步骤
现在，已完成本教程，请从以下主题中查找有关通知中心和模板的更多信息：

* [Use Notification Hubs to send breaking news]: Demonstrates another scenario for using templates.
* [Azure 通知中心概述][Templates]：包含有关模板的更详细信息。

<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Push to users ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Push to users Mobile Services]: /manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[使用通知中心通知用户]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: http://msdn.microsoft.com/library/windowsazure/jj927172.aspx
