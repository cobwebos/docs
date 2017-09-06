---
title: "Azure Functions 移动应用绑定 | Microsoft Docs"
description: "了解如何在 Azure Functions 中使用 Azure 移动应用绑定。"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, Functions, 事件处理, 动态计算, 无服务体系结构"
ms.assetid: faad1263-0fa5-41a9-964f-aecbc0be706a
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/31/2016
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: d2c0e4e233761584bad2df05a8e702e4fc77e84f
ms.contentlocale: zh-cn
ms.lasthandoff: 08/29/2017

---
# <a name="azure-functions-mobile-apps-bindings"></a>Azure Functions 移动应用绑定
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

本文介绍如何在 Azure Functions 中为 [Azure 移动应用](../app-service-mobile/app-service-mobile-value-prop.md)绑定进行配置和编写代码。 Azure Functions 支持移动应用的输入和输出绑定。

通过移动应用输入和输出绑定，可以在移动应用中[读取和写入数据表](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations)。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="input"></a>

## <a name="mobile-apps-input-binding"></a>移动应用输入绑定
移动应用输入绑定从移动表终结点加载记录，并将其传递到函数中。 在 C# 函数和 F# 函数中，函数成功退出后，对记录所做的任何更改都会自动发送回表。

对函数的移动应用输入使用下列 JSON 对象（该对象位于函数 .json 的 `bindings` 数组中）：

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "mobileTable",
    "tableName": "<Name of your mobile app's data table>",
    "id" : "<Id of the record to retrieve - see below>",
    "connection": "<Name of app setting that has your mobile app's URL - see below>",
    "apiKey": "<Name of app setting that has your mobile app's API key - see below>",
    "direction": "in"
}
```

注意以下事项：

* `id` 可以是静态的，也可以基于调用函数的触发器。 例如，如果对函数使用[队列触发器]()，则 `"id": "{queueTrigger}"` 会使用队列消息的字符串值作为记录 ID 进行检索。
* `connection` 应包含 Function App 中应用设置的名称，反过来它又包含移动应用的 URL。 该函数使用此 URL 为移动应用构造所需的 REST 操作。 首先[在 Function App 中创建应用设置]()，它包含移动应用的 URL（类似于 `http://<appname>.azurewebsites.net`），并在输入绑定的 `connection` 属性中指定应用设置的名称。 
* 若要[在 Node.js 移动应用后端执行 API 密钥](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)，或[在 .NET 移动应用后端执行 API 密钥](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key)，则需指定 `apiKey`。 为此，请[在函数应用中创建应用设置]()（包含 API 密钥），然后在输入绑定中添加具有应用设置名称的 `apiKey` 属性。 
  
  > [!IMPORTANT]
  > 此 API 密钥不能与移动应用客户端共享。 应仅将其安全分布到服务端客户端，如 Azure Functions。 
  > 
  > [!NOTE]
  > Azure Functions 会将连接信息和 API 密钥存储为应用设置，这样它们就不会在源代码管理储存库中进行检查。 这会保护敏感信息。
  > 
  > 

<a name="inputusage"></a>

## <a name="input-usage"></a>输入使用情况
本部分介绍如何在函数代码中使用移动应用输入绑定。 

找到具有指定表和记录 ID 的记录时，会将该记录传递到名为 [JObject](http://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) 的参数（在 Node.js 中，会传递到 `context.bindings.<name>` 对象）。 未找到记录时，该参数为 `null`。 

在 C# 函数和 F# 函数中，函数成功退出后，对输入记录（输入参数）所做的任何更改都会自动发送回移动应用表。 在 Node.js 函数中，可使用 `context.bindings.<name>` 访问输入记录。 但不能修改 Node.js 中的记录。

<a name="inputsample"></a>

## <a name="input-sample"></a>输入示例
假设具有以下 function.json，用于检索具有队列触发器消息 ID 的移动应用表记录：

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id" : "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
],
"disabled": false
}
```

请参阅使用来自绑定的输入记录的特定语言示例。 C# 和 F# 示例还修改了记录的 `text` 属性。

* [C#](#inputcsharp)
* [Node.js](#inputnodejs)

<a name="inputcsharp"></a>

### <a name="input-sample-in-c"></a>C# 中的输入示例 #

```cs
#r "Newtonsoft.Json"    
using Newtonsoft.Json.Linq;

public static void Run(string myQueueItem, JObject record)
{
    if (record != null)
    {
        record["Text"] = "This has changed.";
    }    
}
```

<!--
<a name="inputfsharp"></a>
### Input sample in F# ## 

```fsharp
#r "Newtonsoft.Json"    
open Newtonsoft.Json.Linq
let Run(myQueueItem: string, record: JObject) =
  inputDocument?text <- "This has changed."
```
-->

<a name="inputnodejs"></a>

### <a name="input-sample-in-nodejs"></a>Node.js 中的输入示例

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

<a name="output"></a>

## <a name="mobile-apps-output-binding"></a>移动应用输出绑定
使用移动应用输出绑定将新记录写入移动应用表终结点。  

函数的移动应用输出使用下列 JSON 对象（该对象位于函数 .json 的 `bindings` 数组中）：

```json
{
    "name": "<Name of output parameter in function signature>",
    "type": "mobileTable",
    "tableName": "<Name of your mobile app's data table>",
    "connection": "<Name of app setting that has your mobile app's URL - see below>",
    "apiKey": "<Name of app setting that has your mobile app's API key - see below>",
    "direction": "out"
}
```

注意以下事项：

* `connection` 应包含 Function App 中应用设置的名称，反过来它又包含移动应用的 URL。 该函数使用此 URL 为移动应用构造所需的 REST 操作。 首先[在 Function App 中创建应用设置]()，它包含移动应用的 URL（类似于 `http://<appname>.azurewebsites.net`），并在输入绑定的 `connection` 属性中指定应用设置的名称。 
* 若要[在 Node.js 移动应用后端执行 API 密钥](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)，或[在 .NET 移动应用后端执行 API 密钥](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key)，则需指定 `apiKey`。 为此，请[在函数应用中创建应用设置]()（包含 API 密钥），然后在输入绑定中添加具有应用设置名称的 `apiKey` 属性。 
  
  > [!IMPORTANT]
  > 此 API 密钥不能与移动应用客户端共享。 应仅将其安全分布到服务端客户端，如 Azure Functions。 
  > 
  > [!NOTE]
  > Azure Functions 会将连接信息和 API 密钥存储为应用设置，这样它们就不会在源代码管理储存库中进行检查。 这会保护敏感信息。
  > 
  > 

<a name="outputusage"></a>

## <a name="output-usage"></a>输出使用情况
本部分介绍如何在函数代码中使用移动应用输出绑定。 

在 C# 函数中，使用类型为 `out object` 的命名输出参数来访问输出记录。 在 Node.js 函数中，使用 `context.bindings.<name>` 来访问输出记录。

<a name="outputsample"></a>

## <a name="output-sample"></a>输出示例
假设具有以下 function.json，用于定义队列触发器和移动应用输出：

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
    "name": "record",
    "type": "mobileTable",
    "tableName": "MyTable",
    "connection": "My_MobileApp_Url",
    "apiKey": "My_MobileApp_Key",
    "direction": "out"
    }
],
"disabled": false
}
```

请参阅在移动应用表终结点中创建记录的特定语言示例（它包含队列消息内容）。

* [C#](#outcsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>C# 中的输出示例 #

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

<!--
<a name="outfsharp"></a>
### Output sample in F# ## 
```fsharp

```
-->
<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Node.js 中的输出示例

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="next-steps"></a>后续步骤
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


