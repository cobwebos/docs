---
title: "Azure Functions SendGrid 绑定 | Microsoft Docs"
description: "Azure Functions SendGrid 绑定参考"
services: functions
documentationcenter: na
author: rachelappel
manager: cfowler
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/26/2017
ms.author: rachelap
ms.openlocfilehash: 4cdafbe05e29d8b483c6b0e1daf41a36583d7b5e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-sendgrid-bindings"></a>Azure Functions SendGrid 绑定

本文介绍如何在 Azure Functions 中配置和使用 SendGrid 绑定。 通过 SendGrid，可以使用 Azure Functions 以编程方式发送自定义电子邮件。

此文提供适用于 Azure Functions 开发人员的参考信息。 Azure Functions 的新手请从以下资源入手：

[创建第一个 Azure 函数](functions-create-first-azure-function.md)。 
[C#](functions-reference-csharp.md)、[F#](functions-reference-fsharp.md) 或 [Node](functions-reference-node.md) 开发人员参考。

## <a name="functionjson-for-sendgrid-bindings"></a>适用于 SendGrid 绑定的 function.json

Azure Functions 为 SendGrid 提供输出绑定。 SendGrid 输出绑定允许以编程方式创建和发送电子邮件。 

SendGrid 绑定支持以下属性：

|属性  |说明  |
|---------|---------|
|**name**| 必需 - 在请求或请求正文的函数代码中使用的变量名称。 只有一个返回值时，此值为 ```$return```。 |
|**类型**| 必需 - 必须设置为 `sendGrid`。|
|**direction**| 必需 - 必须设置为 `out`。|
|**apiKey**| 必需 - 必须设置为存储在 Function App 应用设置中的 API 密钥名称。 |
|**to**| 收件人的电子邮件地址。 |
|**from**| 发件人的电子邮件地址。 |
|**subject**| 电子邮件主题。 |
|**text**| 电子邮件内容。 |

**function.json** 示例：

```json 
{
    "bindings": [
        {
            "name": "$return",
            "type": "sendGrid",
            "direction": "out",
            "apiKey" : "MySendGridKey",
            "to": "{ToEmail}",
            "from": "{FromEmail}",
            "subject": "SendGrid output bindings"
        }
    ]
}
```

> [!NOTE]
> Azure Functions 会将连接信息和 API 密钥存储为应用设置，这样它们就不会在源代码管理储存库中进行检查。 此操作可保护敏感信息。
>
>

## <a name="c-example-of-the-sendgrid-output-binding"></a>SendGrid 输出绑定的 C# 示例

```csharp
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;

public static Mail Run(TraceWriter log, string input, out Mail message)
{
     message = new Mail
    {        
        Subject = "Azure news"          
    };

    var personalization = new Personalization();
    personalization.AddTo(new Email("recipient@contoso.com"));   

    Content content = new Content
    {
        Type = "text/plain",
        Value = input
    };
    message.AddContent(content);
    message.AddPersonalization(personalization);
}
```

## <a name="node-example-of-the-sendgrid-output-binding"></a>SendGrid 输出绑定的 Node 示例

```javascript
module.exports = function (context, input) {    
    var message = {
         "personalizations": [ { "to": [ { "email": "sample@sample.com" } ] } ],
        from: { email: "sender@contoso.com" },        
        subject: "Azure news",
        content: [{
            type: 'text/plain',
            value: input
        }]
    };

    context.done(null, message);
};

```

## <a name="next-steps"></a>后续步骤
若要了解 Azure Functions 的其他绑定和触发器，请参阅 
- [Azure Functions 触发器和绑定开发人员参考](functions-triggers-bindings.md)

- [Azure Functions 最佳做法](functions-best-practices.md)，列出了创建 Azure 函数时要使用的最佳做法。

- [Azure Functions 开发人员参考](functions-reference.md)，这是可供程序员参考的有关为函数编写代码以及定义触发器和绑定的参考资料。
