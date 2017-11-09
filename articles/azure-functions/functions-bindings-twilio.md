---
title: "Azure Functions Twilio 绑定 | Microsoft Docs"
description: "了解如何将 Azure Functions 与 Twilio 绑定配合使用。"
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, Functions, 事件处理, 动态计算, 无服务体系结构"
ms.assetid: a60263aa-3de9-4e1b-a2bb-0b52e70d559b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/20/2016
ms.author: wesmc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e8c5e8f2dfedae26486e1c8afbe0cec3f3228e86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="send-sms-messages-from-azure-functions-using-the-twilio-output-binding"></a>使用 Twilio 输出绑定从 Azure Functions 发送短信
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

本文介绍如何配置 Twilio 绑定并与 Azure Functions 配合使用。 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Azure Functions 支持 Twilio 输出绑定，使函数能够通过几行代码和 [Twilio](https://www.twilio.com/) 帐户发送短信。 

## <a name="functionjson-for-the-twilio-output-binding"></a>用于 Twilio 输出绑定的 function.json
function.json 文件提供以下属性：

|属性  |说明  |
|---------|---------|
|**name**| 在 Twilio 短信的函数代码中使用的变量名。 |
|**类型**| 必须设置为 `twilioSms`。|
|**accountSid**| 此值必须设置为保留 Twilio 帐户 Sid 的应用设置的名称。|
|**authToken**| 此值必须设置为保留 Twilio 身份验证令牌的应用设置的名称。|
|**to**| 此值设置为短信将发送到的电话号码。|
|**from**| 此值设置为发送短信的电话号码。|
|**direction**| 必须设置为 `out`。|
|**body**| 如果不需要在函数的代码中动态设置短信，则可以使用此值对其进行硬编码。 |

示例 function.json：

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```


## <a name="example-c-queue-trigger-with-twilio-output-binding"></a>使用 Twilio 输出绑定的示例 C# 队列触发器
#### <a name="synchronous"></a>同步
此 Azure 存储队列触发器的同步示例代码使用输出参数向下订单的客户发送短信。

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static void Run(string myQueueItem, out SMSMessage message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    message = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    message.Body = msg;
    message.To = order.mobileNumber;
}
```

#### <a name="asynchronous"></a>异步
此 Azure 存储队列触发器的异步示例代码向下订单的客户发送短信。

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static async Task Run(string myQueueItem, IAsyncCollector<SMSMessage> message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    SMSMessage smsText = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    smsText.Body = msg;
    smsText.To = order.mobileNumber;

    await message.AddAsync(smsText);
}
```

## <a name="example-nodejs-queue-trigger-with-twilio-output-binding"></a>使用 Twilio 输出绑定的示例 Node.js 队列触发器
此 Node.js 示例向下订单的客户发送短信。

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

## <a name="next-steps"></a>后续步骤
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

