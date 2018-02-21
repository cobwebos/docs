---
title: "适用于 Azure Functions 的 通知中心绑定"
description: "了解如何在 Azure Functions 中使用 Azure 通知中心绑定。"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions，函数，事件处理，动态计算，无服务体系结构"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: 6be75035247f05995949734cd4f4f0d934e30685
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="notification-hubs-output-binding-for-azure-functions"></a>适用于 Azure Functions 的 通知中心输出绑定

本文介绍如何在 Azure Functions 中使用 [Azure 通知中心](../notification-hubs/notification-hubs-push-notification-overview.md)绑定发送推送通知。 Azure Functions 支持通知中心的输出绑定。

必须为想要使用的平台通知服务 (PNS) 配置 Azure 通知中心。 若要了解如何从通知中心获取客户端应用程序中的推送通知，请参阅[通知中心入门](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)，并从页面顶部附近的下拉列表中选择目标客户端平台。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="example---template"></a>示例 - 模板

发送的通知可为本机通知，或者[模板通知](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)。 本机通知面向在输出绑定的 `platform` 属性中配置的特定客户端平台。 模板通知可用于面向多个平台。   

参阅语言特定的示例：

* [C# 脚本 - out 参数](#c-script-template-example---out-parameter)
* [C# 脚本 - 异步](#c-script-template-example---asynchronous)
* [C# 脚本 - JSON](#c-script-template-example---json)
* [C# 脚本 - 库类型](#c-script-template-example---library-types)
* [F#](#f-template-example)
* [JavaScript](#javascript-template-example)

### <a name="c-script-template-example---out-parameter"></a>C# 脚本模板示例 - out 参数

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

### <a name="c-script-template-example---asynchronous"></a>C# 脚本模板示例 - 异步

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

### <a name="c-script-template-example---json"></a>C# 脚本模板示例 - JSON

此示例使用有效的 JSON 字符串，发送一条在模板中包含 `message` 占位符的[模板注册](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)通知。

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

### <a name="c-script-template-example---library-types"></a>C# 脚本模板示例 - 库类型

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

### <a name="f-template-example"></a>F # 模板示例

此示例发送一条包含 `location` 和 `message` 的[模板注册](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)的通知。

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

### <a name="javascript-template-example"></a>JavaScript 模板示例

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

## <a name="example---apns-native"></a>示例 - APNS 本机通知

此 C# 脚本示例演示如何发送 APNS 本机通知。 

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

## <a name="example---gcm-native"></a>示例 - GCM 本机通知

此 C# 脚本示例演示如何发送 GCM 本机通知。 

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

## <a name="example---wns-native"></a>示例 - WNS 本机通知

此 C# 脚本示例演示如何使用 [Microsoft Azure 通知中心库](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)中定义的类型发送本机 WNS toast 通知。 

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

## <a name="attributes"></a>属性

在 [C# 类库](functions-dotnet-class-library.md)中，请使用 NuGet 包 [Microsoft.Azure.WebJobs.Extensions.NotificationHubs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs) 中定义的 [NotificationHub](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) 属性。

[配置](#configuration)部分描述了该特性的构造函数参数和属性。

## <a name="configuration"></a>配置

下表解释了在 *function.json* 文件和 `NotificationHub` 特性中设置的绑定配置属性：

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**类型** |不适用| 必须设置为“notificationHub”。 |
|**direction** |不适用| 必须设置为“out”。 | 
|**name** |不适用| 在通知中心消息的函数代码中使用的变量名。 |
|**tagExpression** |**TagExpression** | 标记表达式允许指定将通知传递到一组已注册接收通知的与标记表达式匹配的设备。  有关详细信息，请参阅[路由和标记表达式](../notification-hubs/notification-hubs-tags-segment-push-message.md)。 |
|**hubName** | **HubName** | 在 Azure 门户中通知中心资源的名称。 |
|**连接** | **ConnectionStringSetting** | 包含通知中心连接字符串的应用设置的名称。  连接字符串必须设置为通知中心的 *DefaultFullSharedAccessSignature* 值。 请参阅本文稍后的[连接字符串设置](#connection-string-setup)部分。|
|**platform** | 平台 | 平台属性指示通知面向的客户端平台。 默认情况下，如果从输出绑定中省略平台属性，则模板通知可用于面向 Azure 通知中心上配置的任何平台。 有关一般情况下使用模板通过 Azure 通知中心发送跨平台通知的详细信息，请参阅[模板](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)。 进行设置时，platform 必须是以下值之一： <ul><li><code>apns</code>&mdash;Apple Push Notification 服务。 有关配置 APNS 的通知中心和在客户端应用中接收通知的详细信息，请参阅[通过 Azure 通知中心向 iOS 发送推送通知](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md)。</li><li><code>adm</code>&mdash;[Amazon Device Messaging](https://developer.amazon.com/device-messaging)。 有关配置 ADM 的通知中心和在 Kindle 应用中接收通知的详细信息，请参阅[通知中心入门（Kindle 应用）](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md)。</li><li><code>gcm</code>&mdash;[Google Cloud Messaging](https://developers.google.com/cloud-messaging/)。 同样支持 Firebase Cloud Messaging 这一新版本的 GCM。 有关详细信息，请参阅[通过 Azure 通知中心向 Android 发送推送通知](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md)。</li><li><code>wns</code>&mdash;面向 Windows 平台的 [Windows 推送通知服务](https://msdn.microsoft.com/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview)。 WNS 也支持 Windows Phone 8.1 及更高版本。 有关详细信息，请参阅[适用于 Windows 通用平台应用的通知中心入门](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)。</li><li><code>mpns</code>&mdash;[Microsoft 推送通知服务](https://msdn.microsoft.com/library/windows/apps/ff402558.aspx)。 此平台支持 Windows Phone 8 和早期版本的 Windows Phone 平台。 有关详细信息，请参阅[在 Windows Phone 上借助 Azure 通知中心发送推送通知](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md)。</li></ul> |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="functionjson-file-example"></a>function.json 文件示例

下面是 *function.json* 文件中某个通知中心绑定的示例。

```json
{
  "bindings": [
    {
      "type": "notificationHub",
      "direction": "out",
      "name": "notification",
      "tagExpression": "",
      "hubName": "my-notification-hub",
      "connection": "MyHubConnectionString",
      "platform": "gcm"
    }
  ],
  "disabled": false
}
```

### <a name="connection-string-setup"></a>连接字符串设置

若要使用通知中心输出绑定，必须配置中心的连接字符串。 可以选择现有通知中心，也可以在 Azure 门户中通过“集成”选项卡创建一个新的通知中心。 还可以手动配置连接字符串。 

配置现有通知中心的连接字符串：

1. 导航到 [Azure 门户](https://portal.azure.com)中的通知中心，选择“访问策略”，然后选择 DefaultFullSharedAccessSignature 策略旁边的复制按钮。 这会将 DefaultFullSharedAccessSignature 策略的连接字符串复制到通知中心。 此连接字符串可让函数将通知消息发送到中心。
    ![复制通知中心连接字符串](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. 导航到 Azure 门户中的函数应用，选择“应用程序设置”，添加一个键（例如 **MyHubConnectionString**），粘贴复制的 *DefaultFullSharedAccessSignature* 作为通知中心的值，然后单击“保存”。

此应用程序设置的名称将传入 *function.json* 中的输出绑定连接设置或 .NET 特性。 请参阅本文前面的[配置部分](#configuration)。

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="exceptions-and-return-codes"></a>异常和返回代码

| 绑定 | 引用 |
|---|---|
| 通知中心 | [操作指南](https://docs.microsoft.com/rest/api/notificationhubs/) |

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 Azure Functions 触发器和绑定](functions-triggers-bindings.md)

