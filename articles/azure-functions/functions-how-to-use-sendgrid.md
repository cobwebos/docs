---
title: "如何在 Azure Functions 中使用 SendGrid | Microsoft 文档"
description: "介绍如何在 Azure Functions 中使用 SendGrid"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/31/2017
ms.author: glenga
ms.openlocfilehash: 444e06ff24ea7f909a24d482aba26f890040d980
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-use-sendgrid-in-azure-functions"></a>如何在 Azure Functions 中使用 Send Grid

## <a name="sendgrid-overview"></a>SendGrid 概述

Azure Functions 支持 SendGrid 输出绑定，使函数能够通过几行代码和 SendGrid 帐户发送电子邮件消息。

若要在 Azure Function 中使用 SendGrid API，必须具有 [SendGrid 帐户](http://SendGrid.com)。 此外，还必须具有 SendGrid API 密钥。 登录到 SendGrid 帐户，然后依次单击“设置”和“API 密钥”来生成 API 密钥。 使此密钥保持可用，因为在即将执行的步骤中将使用此密钥。

现在，已准备就绪，可以创建 Azure Function App 了。

## <a name="create-an-azure-function-app"></a>创建 Azure Function App 

Azure Function App 是一个或多个 Azure 函数的容器。 Azure 函数只是函数。 每个 Azure 函数都绑定到一个触发器，后者是导致函数运行的事件。
每个函数可以包含任意数量的输入或输出绑定。 绑定是可以在函数中使用的服务。 SendGrid 是可以用来发送电子邮件的输出绑定。 

1. 登录到 Azure 门户并[创建 Azure Function APP](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) 或打开一个现有 Function App。 
2. 创建一个 Azure 函数。 为了简单起见，请选择手动触发器和 C#。 

 ![创建 Azure 函数](./media/functions-how-to-use-sendgrid/functions-new-function-manual-trigger-page.png)

## <a name="configure-sendgrid-for-use-in-an-azure-function-app"></a>配置 SendGrid 以在 Azure Function App 中使用

必须将 SendGrid API 密钥存储为应用设置才能将其用于函数中。 ApiKey 字段不是实际 SendGrid API 密钥，而是你定义的代表实际 API 密钥的应用设置。 以此方式存储密钥是为了保证安全性，因为它独立于可能会签入到源代码控制中的任何代码或文件。

- 在 Function App 的“应用程序设置”中创建一个 **AppSettings** 密钥。

 ![创建 Azure 函数](./media/functions-how-to-use-sendgrid/functions-configure-sendgrid-api-key-app-settings.png)

## <a name="configure-sendgrid-output-bindings"></a>配置 SendGrid 输出绑定

SendGrid 可用作 Azure 函数输出绑定。 若要创建 SendGrid 输出绑定，请执行以下操作：

1. 在 Azure 门户中，转到函数的“集成”选项卡。
2. 单击“新建输出”创建 SendGrid 输出绑定。
3. 填写“API 密钥”和“消息参数名称”属性。 如果需要，可以现在输入其他属性，或者为其编写代码。 这些设置可以用作默认值。

 ![配置 SendGrid 输出绑定](./media/functions-how-to-use-sendgrid/functions-configure-sendgrid-output-bindings.png)

将绑定添加到函数会在该函数的文件夹中创建一个名为 **function.json** 的文件。 此文件包含了与在 Azure 函数的“集成”选项卡中所见完全相同的信息，但采用的是 Json 格式。 设置 **ApiKey**、**消息**和**来源**字段会在 **function.json** 文件中创建以下条目： 

```json
{
  "bindings": [    
    {
      "type": "sendGrid",
      "name": "message",
      "apiKey": "SendGridKey",
      "direction": "out",
      "from": "azure@contoso.com"
    }
  ],
  "disabled": false
}
```

如果愿意，可以自己直接修改此文件。

现在，已创建并配置了 Function App 和函数，可以编写代码来发送电子邮件了。

## <a name="write-code-that-creates-and-sends-email"></a>编写用于创建和发送电子邮件的代码

SendGrid API 包含了创建和发送电子邮件所需的所有命令。  

- 将函数中的代码替换为以下代码：

```cs
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;

public static void Run(TraceWriter log, string input, out Mail message)
{
    message = new Mail
    {        
        Subject = "Azure news"          
    };

    var personalization = new Personalization();
    // change to email of recipient
    personalization.AddTo(new Email("MoreEmailPlease@contoso.com"));   

    Content content = new Content
    {
        Type = "text/plain",
        Value = input
    };
    message.AddContent(content);
    message.AddPersonalization(personalization);
}
```

注意，第一行包含 ```#r``` 指令，它引用了 SendGrid 程序集。 然后，可以使用 ```using``` 语句更轻松地访问该命名空间中的对象。 在代码中，从 SendGrid API 中创建构成电子邮件的 ```Mail```、```Personalization``` 和 ```Content``` 对象的实例。 当返回消息时，SendGrid 会传送该消息。 

函数的签名还包含一个额外的类型为 ```Mail``` 且名为 ```message``` 的 out 参数。 输入和输出绑定在代码中都将其自己表达为函数参数。 

2. 通过以下方式测试代码：单击“测试”并在“请求正文”中输入一条消息，并单击“运行”按钮。

 ![测试代码](./media/functions-how-to-use-sendgrid/functions-develop-test-sendgrid.png)

3. 检查电子邮件以验证 SendGrid 是否发送了电子邮件。 电子邮件应当发送到第 1 步的代码中的地址，并包含“请求正文”中的消息。

## <a name="next-steps"></a>后续步骤
本文已经演示了如何使用 SendGrid 服务创建和发送电子邮件。 若要详细了解如何在应用中使用 Azure Functions，请参阅以下主题： 

- [Azure Functions 最佳做法](functions-best-practices.md)，列出了创建 Azure 函数时要使用的最佳做法。

- [Azure Functions 开发人员参考](functions-reference.md)，这是可供程序员参考的有关为函数编写代码以及定义触发器和绑定的参考资料。

- [测试 Azure Functions](functions-test-a-function.md)，介绍了可用于测试函数的各种工具和技巧。