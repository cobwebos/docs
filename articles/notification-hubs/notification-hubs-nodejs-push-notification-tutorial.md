---
title: 使用 Azure 通知中心和 Node.js 发送推送通知
description: 了解如何使用通知中心从 Node.js 应用程序发送推送通知。
keywords: 推送通知,push notification,node.js 推送,ios 推送
services: notification-hubs
documentationcenter: nodejs
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ded4749c-6c39-4ff8-b2cf-1927b3e92f93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 04/29/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: cb984a944067ddb1449f58b464e596fd138dc7c7
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82592003"
---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>使用 Azure 通知中心和 Node.js 发送推送通知

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>概述

> [!IMPORTANT]
> 要完成本教程，必须有一个有效的 Azure 帐户。 如果没有帐户，请通过 [Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs)花费几分钟创建免费试用帐户。

本指南介绍如何借助 Azure 通知中心，直接从 [Node.js](https://nodejs.org) 应用程序发送推送通知。

涵盖的方案包括在下列平台将推送通知发送到应用程序：

- Android
- iOS
- 通用 Windows 平台
- Windows Phone

## <a name="notification-hubs"></a>通知中心

Azure 通知中心提供用于向移动设备发送推送通知的易于使用、多平台且可缩放的基础结构。 有关服务基础结构的详细信息，请参阅 [Azure 通知中心](https://msdn.microsoft.com/library/windowsazure/jj927170.aspx)页。

## <a name="create-a-nodejs-application"></a>创建 Node.js 应用程序

本教程的第一步是创建新的空白 Node.js 应用程序。 有关创建 Node.js 应用程序的说明，请参阅[创建 Node.js 应用程序并将其部署到 Azure 网站][nodejswebsite]、使用 Windows PowerShell 创建 [Node.js 云服务][Node.js Cloud Service]或[使用 WebMatrix 创建网站][webmatrix]。

## <a name="configure-your-application-to-use-notification-hubs"></a>将应用程序配置为使用通知中心

若要使用 Azure 通知中心，需要下载和使用 Node.js [azure 包](https://www.npmjs.com/package/azure)，其中包括一组可与推送通知 REST 服务通信的内置帮助程序库。

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>使用 Node 包管理器 (NPM) 可获取该程序包

1. 使用 **PowerShell** (Windows)、**Terminal** (Mac) 或 **Bash** (Linux) 等命令行接口导航到在其中创建了空白应用程序的文件夹。
2. 在命令窗口中执行 `npm install azure-sb`。
3. 可以手动运行 `ls` 或 `dir` 命令来验证是否创建了 `node_modules` 文件夹。
4. 在该文件夹中，找到 **azure** 包，其中包含访问通知中心所需的库。

> [!NOTE]
> 可以在官方 [NPM 博客](https://blog.npmjs.org/post/85484771375/how-to-install-npm)中了解有关安装 NPM 的详细信息。

### <a name="import-the-module"></a>导入模块
使用文本编辑器将以下内容添加到应用程序的 `server.js` 文件的顶部：

```javascript
var azure = require('azure-sb');
```

### <a name="set-up-an-azure-notification-hub-connection"></a>设置 Azure 通知中心连接

可以通过 `NotificationHubService` 对象使用通知中心。 以下代码为名为 `NotificationHubService` 的通知中心创建 `hubname` 对象。 将它添加到靠近 `server.js` 文件顶部、用于导入 Azure 模块的语句之后的位置：

```javascript
var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');
```

通过执行以下步骤从 `connectionstring`Azure 门户[Azure 门户] 值：

1. 在左侧导航窗格中，单击“浏览”。 
2. 选择“通知中心”，并找到要用于示例的中心。  如果在创建新通知中心时需要帮助，可以参阅 [Windows 应用商店入门教程](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)。
3. 选择“设置”。 
4. 单击“访问策略”。  会看到共享连接字符串和完全访问连接字符串。

![Azure 门户 — 通知中心](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> 还可以使用 **Azure PowerShell** 提供的 [Get-AzureSbNamespace](/powershell/azureps-cmdlets-docs) cmdlet 或者在 **Azure 命令行接口 (Azure CLI)** 中使用 [azure sb namespace show](../cli-install-nodejs.md) 命令检索连接字符串。

## <a name="general-architecture"></a>一般体系结构

`NotificationHubService` 对象公开以下对象实例，以便向特定设备和应用程序发送推送通知：

- **Android** - 使用可在 `notificationHubService.gcm` 获得的 `GcmService` 对象
- **iOS** - 使用可在 `notificationHubService.apns` 访问的 `ApnsService` 对象
- **Windows Phone** - 使用可在 `notificationHubService.mpns` 获得的 `MpnsService` 对象
- **通用 Windows 平台** - 使用可在 `notificationHubService.wns` 获得的 `WnsService` 对象

### <a name="how-to-send-push-notifications-to-android-applications"></a>如何：向 Android 应用程序发送推送通知

`GcmService` 对象提供 `send` 方法，该方法可用于将推送通知发送到 Android 应用程序。 该 `send` 方法接受以下参数：

- **Tags** — 标记标识符。 如果没有提供任何标记，通知会发送给所有客户端。
- **Payload** — 消息的 JSON 或原始字符串的有效负载。
- **Callback** — 回调函数。

有关负载格式的详细信息，请参阅[有效负载文档](https://distriqt.github.io/ANE-PushNotifications/m.FCM-GCM%20Payload)。

以下代码使用由 `NotificationHubService` 公开的 `GcmService` 实例将推送通知发送到所有已注册的客户端。

```javascript
var payload = {
  data: {
    message: 'Hello!'
  }
};
notificationHubService.gcm.send(null, payload, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-ios-applications"></a>如何：向 iOS 应用程序发送推送通知

与上述 Android 应用程序一样，`ApnsService` 对象提供可用于将推送通知发送到 iOS 应用程序的 `send` 方法。 该 `send` 方法接受以下参数：

- **Tags** — 标记标识符。 如果没有提供任何标记，通知会发送给所有客户端。
- **Payload** — 消息的 JSON 或字符串的有效负载。
- **Callback** — 回调函数。

有关负载格式的详细信息，请参阅[UserNotifications guide](https://developer.apple.com/documentation/usernotifications)（**通知内容**）部分。

以下代码使用由 `NotificationHubService` 公开的 `ApnsService` 实例将警报消息发送给所有客户端：

```javascript
var payload={
    alert: 'Hello!'
  };
notificationHubService.apns.send(null, payload, function(error){
  if(!error){
      // notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>如何：向 Windows Phone 应用程序发送推送通知

`MpnsService` 对象提供可用于将推送通知发送到 Windows Phone 应用程序的 `send` 方法。 该 `send` 方法接受以下参数：

- **Tags** — 标记标识符。 如果没有提供任何标记，通知会发送给所有客户端。
- **Payload** — 消息的 XML 有效负载。
- **适用于**toast 通知。 -  `toast` `token` 用于磁贴通知。
- **NotificationClass** — 通知的优先级。 有关该参数的有效值，请参阅 [从服务器推送通知](https://msdn.microsoft.com/library/hh221551.aspx)文档中的 **HTTP Header Elements**（HTTP 标头元素）部分。
- **Options** — 可选的请求标头。
- **Callback** — 回调函数。

有关有效的 `TargetName`、`NotificationClass` 和标头选项的列表，请查看[从服务器推送通知](https://msdn.microsoft.com/library/hh221551.aspx)页面。

以下示例代码使用由 `NotificationHubService` 公开的 `MpnsService` 实例发送 toast 推送通知：

```javascript
var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>如何：向通用 Windows 平台 (UWP) 应用程序发送推送通知

`WnsService` 对象提供可用于将推送通知发送到通用 Windows 平台应用程序的 `send` 方法。  该 `send` 方法接受以下参数：

- **Tags** — 标记标识符。 如果没有提供任何标记，通知会发送给所有已注册的客户端。
- **Payload** — XML 消息有效负载。
- **Type** — 通知类型。
- **Options** — 可选的请求标头。
- **Callback** — 回调函数。

有关有效的类型和请求标头的列表，请参阅[推送通知服务请求和响应标头](https://msdn.microsoft.com/library/windows/apps/hh465435.aspx)。

以下示例代码使用由 `NotificationHubService` 公开的 `WnsService` 实例将 toast 推送通知发送给 UWP 应用：

```javascript
var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
  if(!error){
      // notification sent
  }
});
```

## <a name="next-steps"></a>后续步骤

使用上述示例代码段，可以轻松地构建服务基础结构，将推送通知传递到各种设备。 了解使用通知中心和 node.js 的基础知识之后，请参考下列链接以了解有关如何进一步扩展这些功能的详细信息。

- 请参阅 MSDN 参考：[Azure 通知中心](https://msdn.microsoft.com/library/azure/jj927170.aspx)。
- 请访问 GitHub 上的 [Azure SDK for Node] 存储库以获取更多示例和实施详细信息。

[用于 Node 的 Azure SDK]: https://github.com/WindowsAzure/azure-sdk-for-node
[Next Steps]: #nextsteps
[What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
[Create a Service Namespace]: #create-a-service-namespace
[Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
[Create a Node.js Application]: #Create_a_Nodejs_Application
[Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
[How to: Create a Topic]: #How_to_Create_a_Topic
[How to: Create Subscriptions]: #How_to_Create_Subscriptions
[How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
[How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
[How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
[How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
[1]: #Next_Steps
[Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
[image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
[2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
[3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
[4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
[5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
[SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Azure Service Bus Notification Hubs]: https://msdn.microsoft.com/library/windowsazure/jj927170.aspx
[SqlFilter]: https://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
[Web Site with WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
[nodejswebsite]: https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs
[webmatrix]: https://docs.microsoft.com/aspnet/web-pages/videos/introduction/create-a-website-using-webmatrix
[Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
[Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
[Azure 门户]: https://portal.azure.com
