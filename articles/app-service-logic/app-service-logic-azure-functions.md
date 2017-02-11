---
title: "在逻辑应用中使用 Azure Functions | Microsoft Docs"
description: "请参阅如何在逻辑应用中使用 Azure Functions"
services: logic-apps,functions
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: dwrede
editor: 
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 37e246ddb362c3b19db988224a5e6df4e41bd1e7


---
# <a name="using-azure-functions-with-logic-apps"></a>在逻辑应用中使用 Azure Functions
通过在逻辑应用中使用 Azure Functions 可以运行自定义的 C# 或 node.js 代码段。  在 Microsoft Azure 中 [Azure Functions](../azure-functions/functions-overview.md) 提供与服务器无关的计算。 这对于执行以下任务很有用：

* 逻辑应用内的高级格式设置或字段计算
* 在工作流中执行计算
* 使用 C# 或 node.js 中支持的函数扩展逻辑应用的功能

## <a name="create-a-function-for-logic-apps"></a>为逻辑应用创建一个函数
我们建议在 Azure Functions 门户中使用**泛型 Webhook — 节点**或**泛型 Webhook — C#**模板创建新的函数。 这可以自动填充接受逻辑应用的 `application/json` 的模板。  如果在 Azure Functions 中选择“集成”选项卡，应将其“模式”设为“Webhook”，并且“Webhook 类型”为“泛型 JSON”。  自动发现使用这些模板的函数，并在逻辑应用设计器的“我的区域中的 Azure Functions”下面列出。

Webhook 函数接受请求，并通过 `data` 变量将其传递给方法。 可以使用点表示法如 `data.foo` 来访问有效负载的属性。  如以下示例所示，此简单的 JavaScript 函数将 DateTime 值转换为日期字符串：

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## <a name="call-azure-functions-from-a-logic-app"></a>从逻辑应用中调用 Azure Functions
在设计器中，如果单击“操作”菜单，可以选择“我的区域中的 Azure Functions”。  此操作列出订阅中的容器，你可以选择想要调用的函数。  

选择函数后，会提示你指定输入有效负载对象。 这是逻辑应用发送给此函数的消息，此对象必须是一个 JSON 对象。 例如，如果想要从 Salesforce 触发器传递**上次修改**日期，那么此函数有效负载可能如下所示：

![上次修改日期][1]

## <a name="trigger-logic-apps-from-a-function"></a>从函数中触发逻辑应用
也可以在函数中触发逻辑应用。  若要执行此操作，只需使用手动触发器创建逻辑应用。 有关详细信息，请参阅[作为可调用终结点的逻辑应用](app-service-logic-http-endpoint.md)。  然后，在函数中生成指向手动触发 URL 的 HTTP POST 请求，此请求带有要发送到逻辑应用的有效负载。

### <a name="create-a-function-from-the-designer"></a>通过设计器创建函数
你还可以在设计器中创建 node.js webhook 函数。 首先，选择“我的区域中的 Azure Functions”，然后选择函数的容器。  如果还没有容器，则需要在 [Azure Functions 门户](https://functions.azure.com/signin)中创建一个。 选择“新建”。  

若要基于要计算的数据生成模板，请指定打算传递到函数的上下文对象。 此对象必须是一个 JSON 对象。 例如，如果从 FTP 操作中传递文件内容，则上下文有效负载如下所示：

![上下文有效负载][2]

> [!NOTE]
> 因为此对象不转换为字符串，所以此内容将直接添加到 JSON 有效负载。 但是，如果不是 JSON 令牌（即，一个字符串或 JSON 对象/数组）则会出错。 若要将其转换为字符串，只需添加引号，如本文第一个图例中所示。
> 
> 

之后设计器生成可用于创建内联函数的函数模板。 基于要传递到函数的上下文对象预先创建了变量。

<!--Image references-->
[1]: ./media/app-service-logic-azure-functions/callFunction.png
[2]: ./media/app-service-logic-azure-functions/createFunction.png



<!--HONumber=Nov16_HO3-->


