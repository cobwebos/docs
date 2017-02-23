---
title: "Azure Functions 通知中心绑定 | Microsoft Docs"
description: "了解如何在 Azure Functions 中使用 Azure 通知中心绑定。"
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, Functions, 事件处理, 动态计算, 无服务体系结构"
ms.assetid: 0ff0c949-20bf-430b-8dd5-d72b7b6ee6f7
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/27/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: a64af1a9f969c5ce5a7e2f18ca23451ac96f1a13
ms.openlocfilehash: 7abd7b0921c029ff159935d89905d3c502aba643


---
# <a name="azure-functions-notification-hub-output-binding"></a>Azure Functions 通知中心输出绑定
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

本文介绍如何在 Azure Functions 中为 Azure Functions 通知中心绑定进行配置和编写代码。 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

函数可使用已配置的 Azure 通知中心发送推送通知，只需几行代码即可。 但是，必须为想要使用的平台通知服务 (PNS) 配置 Azure 通知中心。 有关配置 Azure 通知中心和开发注册接收通知的的客户端应用程序的详细信息，请参阅[通知中心入门](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)，然后单击顶部的目标客户端平台。

发送的通知可为本机通知，或者模板通知。 本机通知面向在输出绑定的 `platform` 属性中配置的特定通知平台。 模板通知可用于面向多个平台。   

## <a name="notification-hub-output-binding-properties"></a>通知中心输出绑定属性
function.json 文件提供以下属性：

* `name`：在通知中心消息的函数代码中使用的变量名。
* `type`必须设置为 "notificationHub"。
* `tagExpression`：标记表达式允许指定将通知传递到一组已注册接收通知的与标记表达式匹配的设备。  有关详细信息，请参阅[路由和标记表达式](../notification-hubs/notification-hubs-tags-segment-push-message.md)。
* `hubName`：在 Azure 门户中通知中心资源的名称。
* `connection`：此连接字符串必须为**应用程序设置**连接字符串，且设置为用于通知中心的 DefaultFullSharedAccessSignature 值。
* `direction`：必须设置为“out”。 
* `platform`：平台属性表明通知面向的通知平台。 必须是以下值之一：
  * `template`：如果从输出绑定省略平台属性，则此为默认平台。 模板通知可用于面向 Azure 通知中心上配置的任何平台。 有关一般情况下使用模板通过 Azure 通知中心发送跨平台通知的详细信息，请参阅[模板](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)。
  * `apns`：Apple Push Notification 服务。 有关配置 APNS 的通知中心和在客户端应用中接收通知的详细信息，请参阅[通过 Azure 通知中心将推送通知发送到 iOS](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md) 
  * `adm`[Amazon Device Messaging](https://developer.amazon.com/device-messaging)。 有关配置 ADM 的通知中心和在 Kindle 应用中接收通知的详细信息，请参阅 [Kindle 应用通知中心入门](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md) 
  * `gcm`：[Google Cloud Messaging](https://developers.google.com/cloud-messaging/)。 同样支持 Firebase Cloud Messaging 这一新版本的 GCM。 有关配置 GCM/FCM 的通知中心和在 Android 客户端应用中接收通知的详细信息，请参阅[通过 Azure 通知中心将推送通知发送到 Android](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md)
  * `wns`：面向 Windows 平台的 [Windows 推送通知服务](https://msdn.microsoft.com/en-us/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview)。 WNS 也支持 Windows Phone 8.1 及更高版本。 有关配置 WNS 的通知中心和在通用 Windows 平台 (UWP) 应用中接收通知的详细信息，请参阅 [Windows 通用平台应用通知中心入门](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
  * `mpns`：[Microsoft 推送通知服务](https://msdn.microsoft.com/en-us/library/windows/apps/ff402558.aspx)。 此平台支持 Windows Phone 8 和早期版本的 Windows Phone 平台。 有关配置 MPNS 的通知中心和在 Windows Phone 应用中接收通知的详细信息，请参阅[在 Windows Phone 上通过 Azure 通知中心发送推送通知](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md)

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
要使用通知中心输出绑定，必须配置中心的连接字符串。 通过选择通知中心或创建新通知中心，即可在“集成”选项卡上完成此操作。 

也可通过将 DefaultFullSharedAccessSignature 的连接字符串添加到你的通知中心，手动为现有中心添加连接字符串。 此连接字符串提供了对你的函数的访问权限，来发送通知消息。 可从 Azure 门户中通知中心资源的主边栏选项卡的“密钥”按钮访问 DefaultFullSharedAccessSignature 连接字符串的值。 若要手动为你的中心添加连接字符串，请按照以下步骤操作： 

1. 在 Azure 门户的“Function App”边栏选项卡中，单击“Function App 设置”，转到“应用服务设置”。
2. 在“设置”边栏选项卡中，单击“应用程序设置”。
3. 向下滚动到“应用设置”部分，为通知中心添加已命名的 *DefaultFullSharedAccessSignature* 值项。
4. 引用输出绑定中应用设置字符串的名称。 类似于上面示例中使用的 MyHubConnectionString。

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




<!--HONumber=Feb17_HO2-->


