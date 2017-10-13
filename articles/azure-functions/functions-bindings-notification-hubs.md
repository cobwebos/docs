---
title: "Azure Functions 通知中心绑定 | Microsoft Docs"
description: "了解如何在 Azure Functions 中使用 Azure 通知中心绑定。"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, Functions, 事件处理, 动态计算, 无服务体系结构"
ms.assetid: 0ff0c949-20bf-430b-8dd5-d72b7b6ee6f7
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/26/2017
ms.author: glenga
ms.openlocfilehash: 02d01d0f6e945ed54dbe766aec2a0fd7c17c510f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-notification-hub-output-binding"></a>Azure Functions 通知中心输出绑定
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

本文介绍如何在 Azure Functions 中为 Azure Functions 通知中心绑定进行配置和编写代码。 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

函数可使用已配置的 Azure 通知中心发送推送通知，只需几行代码即可。 但是，必须为想要使用的平台通知服务 (PNS) 配置 Azure 通知中心。 有关配置 Azure 通知中心和开发注册接收通知的的客户端应用程序的详细信息，请参阅[通知中心入门](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)，并单击顶部的目标客户端平台。

发送的通知可为本机通知，或者模板通知。 本机通知面向在输出绑定的 `platform` 属性中配置的特定通知平台。 模板通知可用于面向多个平台。   

## <a name="notification-hub-output-binding-properties"></a>通知中心输出绑定属性
function.json 文件提供以下属性：


|属性  |说明  |
|---------|---------|
|**name** | 在通知中心消息的函数代码中使用的变量名。 |
|**类型** | 必须设置为 `notificationHub`。 |
|**tagExpression** | 标记表达式允许指定将通知传递到一组已注册接收通知的与标记表达式匹配的设备。  有关详细信息，请参阅[路由和标记表达式](../notification-hubs/notification-hubs-tags-segment-push-message.md)。 |
|**hubName** | 在 Azure 门户中通知中心资源的名称。 |
|**连接** | 此连接字符串必须为应用程序设置连接字符串，且设置为用于通知中心的 DefaultFullSharedAccessSignature 值。 |
|**direction** | 必须设置为 `out`。 | 
|**platform** | 平台属性表明通知面向的通知平台。 默认情况下，如果从输出绑定中省略平台属性，则模板通知可用于面向 Azure 通知中心上配置的任何平台。 有关一般情况下使用模板通过 Azure 通知中心发送跨平台通知的详细信息，请参阅[模板](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)。 进行设置时，platform 必须是以下值之一： <ul><li><code>apns</code>&mdash;Apple Push Notification 服务。 有关配置 APNS 的通知中心和在客户端应用中接收通知的详细信息，请参阅[通过 Azure 通知中心向 iOS 发送推送通知](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md)。</li><li><code>adm</code>&mdash;[Amazon Device Messaging](https://developer.amazon.com/device-messaging)。 有关配置 ADM 的通知中心和在 Kindle 应用中接收通知的详细信息，请参阅[通知中心入门（Kindle 应用）](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md)。</li><li><code>gcm</code>&mdash;[Google Cloud Messaging](https://developers.google.com/cloud-messaging/)。 同样支持 Firebase Cloud Messaging 这一新版本的 GCM。 有关详细信息，请参阅[通过 Azure 通知中心向 Android 发送推送通知](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md)。</li><li><code>wns</code>&mdash;面向 Windows 平台的 [Windows 推送通知服务](https://msdn.microsoft.com/en-us/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview)。 WNS 也支持 Windows Phone 8.1 及更高版本。 有关详细信息，请参阅[适用于 Windows 通用平台应用的通知中心入门](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)。</li><li><code>mpns</code>&mdash;[Microsoft 推送通知服务](https://msdn.microsoft.com/en-us/library/windows/apps/ff402558.aspx)。 此平台支持 Windows Phone 8 和早期版本的 Windows Phone 平台。 有关详细信息，请参阅[在 Windows Phone 上借助 Azure 通知中心发送推送通知](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md)。</li></ul> |

示例 function.json：

```json
{
  "bindings": [
    {
      "name": "notification",
      "type": "notificationHub",
      "tagExpression": "",
      "hubName": "my-notification-hub",
      "connection": "MyHubConnectionString",
      "platform": "gcm",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

## <a name="notification-hub-connection-string-setup"></a>通知中心连接字符串设置
要使用通知中心输出绑定，必须配置中心的连接字符串。 可以选择现有通知中心，也可通过函数的“集成”选项卡创建一个新的通知中心。 还可以手动配置连接字符串。 

配置现有通知中心的连接字符串：

1. 导航到 [Azure 门户](https://portal.azure.com)中的通知中心，选择“访问策略”，然后选择 DefaultFullSharedAccessSignature 策略旁边的复制按钮。 这会将 DefaultFullSharedAccessSignature 策略的连接字符串复制到通知中心。 此连接字符串提供了对函数的访问权限，来发送通知消息。 
    ![复制通知中心连接字符串](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. 导航到 Azure 门户中的函数应用，选择“应用程序设置”，添加 `MyHubConnectionString` 等键，粘贴复制的 DefaultFullSharedAccessSignature 作为通知中心的值，然后单击“保存”。

现在可使用此命名应用程序设置，定义输出绑定中的通知中心连接。

## <a name="apns-native-notifications-with-c-queue-triggers"></a>包含 C# 队列触发器的 APNS 本机通知
此示例演示如何使用 [Microsoft Azure 通知中心库](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)中定义的类型发送本机 APNS 通知。 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native APNS notification is ...
    // { "aps": { "alert": "notification message" }}  

    log.Info($"Sending APNS notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{apnsNotificationPayload}");
    await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
}
```

## <a name="gcm-native-notifications-with-c-queue-triggers"></a>包含 C# 队列触发器的 GCM 本机通知
此示例演示如何使用 [Microsoft Azure 通知中心库](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)中定义的类型发送本机 GCM 通知。 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native GCM notification is ...
    // { "data": { "message": "notification message" }}  

    log.Info($"Sending GCM notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string gcmNotificationPayload = "{\"data\": {\"message\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{gcmNotificationPayload}");
    await notification.AddAsync(new GcmNotification(gcmNotificationPayload));        
}
```

## <a name="wns-native-notifications-with-c-queue-triggers"></a>包含 C# 队列触发器的 WNS 本机通知
此示例演示如何使用 [Microsoft Azure 通知中心库](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)中定义的类型发送本机 WNS toast 通知。 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The XML format for a native WNS toast notification is ...
    // <?xml version="1.0" encoding="utf-8"?>
    // <toast>
    //      <visual>
    //     <binding template="ToastText01">
    //       <text id="1">notification message</text>
    //     </binding>
    //   </visual>
    // </toast>

    log.Info($"Sending WNS toast notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                    "<toast><visual><binding template=\"ToastText01\">" +
                                        "<text id=\"1\">" + 
                                            "A new user wants to be added (" + user.name + ")" + 
                                        "</text>" +
                                    "</binding></visual></toast>";

    log.Info($"{wnsNotificationPayload}");
    await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));        
}
```

## <a name="template-example-for-nodejs-timer-triggers"></a>Node.js 计时器触发器的模板示例
此示例发送一条包含 `location` 和 `message` 的[模板注册](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)的通知。

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);  
    context.bindings.notification = {
        location: "Redmond",
        message: "Hello from Node!"
    };
    context.done();
};
```

## <a name="template-example-for-f-timer-triggers"></a>F# 计时器触发器的模板示例
此示例发送一条包含 `location` 和 `message` 的[模板注册](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)的通知。

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

## <a name="template-example-using-an-out-parameter"></a>使用 out 参数的模板示例
此示例发送一条在模板中包含 `message` 占位符的[模板注册](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)通知。

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = GetTemplateProperties(myQueueItem);
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return templateProperties;
}
```

## <a name="template-example-with-asynchronous-function"></a>包含异步函数的模板示例
如果使用的是异步代码，则不允许使用 out 参数。 在此情况下，使用 `IAsyncCollector` 返回模板通知。 以下代码是上述代码的异步示例。 

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    log.Info($"Sending Template Notification to Notification Hub");
    await notification.AddAsync(GetTemplateProperties(myQueueItem));    
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["user"] = "A new user wants to be added : " + message;
    return templateProperties;
}
```

## <a name="template-example-using-json"></a>使用 JSON 的模板示例
此示例使用有效的 JSON 字符串，发送一条在模板中包含 `message` 占位符的[模板注册](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)通知。

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

## <a name="template-example-using-notification-hubs-library-types"></a>使用通知中心库类型的模板示例
此示例演示如何使用 [Microsoft Azure 通知中心库](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)中定义的类型。 

```cs
#r "Microsoft.Azure.NotificationHubs"

using System;
using System.Threading.Tasks;
using Microsoft.Azure.NotificationHubs;

public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
{
   log.Info($"C# Queue trigger function processed: {myQueueItem}");
   notification = GetTemplateNotification(myQueueItem);
}

private static TemplateNotification GetTemplateNotification(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return new TemplateNotification(templateProperties);
}
```

## <a name="next-steps"></a>后续步骤
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

