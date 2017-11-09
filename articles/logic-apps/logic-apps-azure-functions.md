---
title: "使用 Azure Functions 的 Azure 逻辑应用的自定义代码 | Microsoft Docs"
description: "使用 Azure Functions 为 Azure 逻辑应用创建和运行自定义代码"
services: logic-apps,functions
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 18442c87b049200fac5ed41cc7034ba7a848b8d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="add-and-run-custom-code-for-logic-apps-through-azure-functions"></a>通过 Azure Functions 为逻辑应用添加和运行自定义代码

若要在逻辑应用中运行 C# 或 node.js 的自定义代码段，可以通过 Azure Functions 创建自定义函数。 
在 Microsoft Azure 中 [Azure Functions](../azure-functions/functions-overview.md) 提供与服务器无关的计算，可用于执行以下这些任务：

* 逻辑应用中字段的高级格式设置或计算
* 在工作流中执行计算。
* 使用 C# 或 node.js 中支持的函数扩展逻辑应用功能

## <a name="create-custom-functions-for-your-logic-apps"></a>为逻辑应用创建自定义函数

我们建议在 Azure Functions 门户中通过**泛型 Webhook — 节点**或**泛型 Webhook — C#** 模板创建函数。 结果会创建自动填充的模板，它从逻辑应用接受 `application/json`。 系统会自动发现通过这些模板创建的函数，并在逻辑应用设计器的“我的区域中的 Azure Functions”下面显示。

在 Azure 门户中的函数“集成”窗格上，模板应显示“模式”设置为“Webhook”，而“Webhook 类型”设置为“泛型 JSON”。 

Webhook 函数接受请求，并通过 `data` 变量将其传递给方法。 可以使用点表示法如 `data.function-name` 来访问有效负载的属性。 如以下示例所示，此简单的 JavaScript 函数将 DateTime 值转换为日期字符串：

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## <a name="call-azure-functions-from-logic-apps"></a>从逻辑应用调用 Azure Functions

要列出订阅中的容器并选择要调用的函数，请在逻辑应用设计器中单击“操作”菜单，并从“我的区域中的 Azure Functions”中进行选择。

选择函数之后，会要求指定输入有效负载对象。 此对象是逻辑应用发送给函数的消息，必须是 JSON 对象。 例如，如果要从 Salesforce 触发器传递“上次修改”日期，则函数有效负载可能如此示例所示：

![上次修改日期][1]

## <a name="trigger-logic-apps-from-a-function"></a>从函数中触发逻辑应用

可以从函数中触发逻辑应用。 请参阅[作为可调用终结点的逻辑应用](logic-apps-http-endpoint.md)。 创建具有手动触发器的逻辑应用，并在函数中生成指向手动触发器 URL 的 HTTP POST 请求，此请求带有要发送到逻辑应用的有效负载。

### <a name="create-a-function-from-logic-app-designer"></a>通过逻辑应用设计器创建函数

还可以从设计器中创建 node.js webhook 函数。 首先，选择“我的区域中的 Azure Functions”，并选择函数的容器。 如果还没有容器，则需要在 [Azure Functions 门户](https://functions.azure.com/signin)中创建一个。 选择“新建”。  

若要基于要计算的数据生成模板，请指定打算传递到函数的上下文对象。 此对象必须是 JSON 对象。 例如，如果从 FTP 操作传入文件内容，则上下文有效负载如此示例所示：

![上下文有效负载][2]

> [!NOTE]
> 因为此对象不转换为字符串，所以此内容会直接添加到 JSON 有效负载。 但是，如果此对象不是 JSON 令牌（即，一个字符串或 JSON 对象/数组），则会发生错误。 要将对象转换为字符串，请添加引号，如本文第一个图例中所示。
> 

之后设计器生成可用于创建内联函数的函数模板。 基于要传递到函数的上下文对象预先创建了变量。

<!--Image references-->
[1]: ./media/logic-apps-azure-functions/callfunction.png
[2]: ./media/logic-apps-azure-functions/createfunction.png
