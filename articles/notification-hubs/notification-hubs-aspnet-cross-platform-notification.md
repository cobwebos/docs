---
title: "使用通知中心向用户发送跨平台通知 (ASP.NET)"
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
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: ef971fcfe68978ea9ce0810c69efbe134bb15f8a
ms.contentlocale: zh-cn
ms.lasthandoff: 08/16/2017

---
# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>使用通知中心向用户发送跨平台通知
在上一教程[使用通知中心通知用户]中，了解了如何将通知推送到经过身份验证的特定用户所注册的所有设备。 在该教程中，需要使用多个请求将通知发送到每个支持的客户端平台。 通知中心支持模板，这允许指定特定设备要如何接收通知。 这简化了发送跨平台通知。 本主题演示如何在单个请求中利用模板发送针对所有平台的与平台无关的通知。 有关模板的更多详细信息，请参阅 [Azure 通知中心概述][Templates]。
> [!IMPORTANT]
> Visual Studio 2017 不支持使用 Windows Phone 项目 8.1 及更早的版本。 有关详细信息，请参阅 [Visual Studio 2017 平台目标以及兼容性](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs)。

> [!NOTE]
> 通知中心允许设备使用同一标记注册多个模板。 在这种情况下，针对该标签的传入消息将导致多个通知发送到设备（每个通知对应一个模板）。 这允许在多个可视通知中显示同一消息，如作为 Windows 应用商店应用程序中的徽章和 toast 通知。
> 
> 

完成以下步骤来使用模板发送跨平台通知：

1. 在 Visual Studio 的解决方案资源管理器中，展开“控制器”文件夹，并打开 RegisterController.cs 文件。
2. 在 **Put** 方法中查找用于创建新注册的代码块，并将 `switch` 内容替换为以下代码：
   
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
   
    此代码调用平台特定的方法来创建模板注册而非本机注册。 不需要修改现有注册，因为模板注册派生自本机注册。
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
5. 再次运行客户端应用程序并验证注册成功。
6. （可选）将客户端应用程序部署到第二个设备，然后运行该应用程序。
   
    请注意通知会显示在每个设备上。

## <a name="next-steps"></a>后续步骤
现在，已完成本教程，请从以下主题中查找有关通知中心和模板的更多信息：

* **[使用通知中心发送突发新闻]** <br/>演示使用模板的另一场景
* **[Azure 通知中心概述][Templates]**<br/>“概述”主题提供有关模板的更多详细信息。

<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Push to users ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Push to users Mobile Services]: /manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[使用通知中心发送突发新闻]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[使用通知中心通知用户]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: http://msdn.microsoft.com/library/windowsazure/jj927172.aspx

