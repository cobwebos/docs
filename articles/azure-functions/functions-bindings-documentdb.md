---
title: "Azure Functions DocumentDB 绑定 | Microsoft Docs"
description: "了解如何在 Azure Functions 中使用 Azure DocumentDB 绑定。"
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "Azure Functions，函数，事件处理，动态计算，无服务体系结构"
ms.assetid: 3d8497f0-21f3-437d-ba24-5ece8c90ac85
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/10/2016
ms.author: chrande; glenga
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: e476a80a3846b8c80c35d6803d5518727f008824
ms.lasthandoff: 03/06/2017


---
# <a name="azure-functions-documentdb-bindings"></a>Azure Functions DocumentDB 绑定
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

本文介绍如何在 Azure Functions 中为 Azure DocumentDB 绑定进行配置和编写代码。 Azure Functions 支持 DocumentDB 的输入和输出绑定。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

有关 DocumentDB 的详细信息，请参阅 [DocumentDB 简介](../documentdb/documentdb-introduction.md)和[生成 DocumentDB 控制台应用程序](../documentdb/documentdb-get-started.md)。

<a id="docdbinput"></a>

## <a name="documentdb-input-binding"></a>DocumentDB 输入绑定
DocumentDB 输入绑定检索 DocumentDB 文档，并将其传递给函数的命名输入参数。 可根据调用函数的触发器确定文档 ID。 

函数的 DocumentDB 输入使用 function.json 的 `bindings` 数组中的下列 JSON 对象：

```json
{
  "name": "<Name of input parameter in function signature>",
  "type": "documentDB",
  "databaseName": "<Name of the DocumentDB database>",
  "collectionName": "<Name of the DocumentDB collection>",
  "id": "<Id of the DocumentDB document - see below>",
  "connection": "<Name of app setting with connection string - see below>",
  "direction": "in"
},
```

注意以下事项：

* `id` 支持类似 `{queueTrigger}` 的绑定，它使用队列消息的字符串值作为文档 ID。
* `connection` 必须是某个应用设置的名称，该设置指向 DocumentDB 帐户的终结点（包含值 `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>`）。 如果通过函数门户 UI 创建 DocumentDB 帐户，则帐户创建过程会创建一个应用设置。 若要使用现有 DocumentDB 帐户，需要[手动配置此应用设置](functions-how-to-use-azure-function-app-settings.md)。 
* 若未找到指定文档，则函数的命名输入参数设置为 `null`。 

## <a name="input-usage"></a>输入使用情况
本部分演示如何在函数代码中使用 DocumentDB 输入绑定。

在 C# 函数和 F# 函数中，函数成功退出后，对输入文档（命名输入参数）所做的任何更改都将自动发送回集合。 在 Node.js 函数中，输入绑定中的文档更新不会发送回集合。 但是，可以使用 `context.bindings.<documentName>In` 和 `context.bindings.<documentName>Out` 更新输入文档。 在 [Node.js 示例](#innodejs)中查看如何实现。

<a name="inputsample"></a>

## <a name="input-sample"></a>输入示例
假设在 function.json 的 `bindings` 数组中有以下 DocumentDB 输入绑定：

```json
{
  "name": "inputDocument",
  "type": "documentDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "id" : "{queueTrigger}",
  "connection": "MyAccount_DOCUMENTDB",     
  "direction": "in"
}
```

请参阅特定于语言的示例，该示例使用此输入绑定更新文档的文本值。

* [C#](#incsharp)
* [F#](#infsharp)
* [Node.js](#innodejs)

<a name="incsharp"></a>
### <a name="input-sample-in-c"></a>C 中的输入示例# #

```cs
public static void Run(string myQueueItem, dynamic inputDocument)
{   
  inputDocument.text = "This has changed.";
}
```
<a name="infsharp"></a>

### <a name="input-sample-in-f"></a>F 中的输入示例# #

```fsharp
open FSharp.Interop.Dynamic
let Run(myQueueItem: string, inputDocument: obj) =
  inputDocument?text <- "This has changed."
```

需要添加指定 `FSharp.Interop.Dynamic` 和 `Dynamitey` NuGet 依赖关系的 `project.json` 文件：

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

若要添加 `project.json` 文件，请参阅 [F# 包管理](functions-reference-fsharp.md#package)。

<a name="innodejs"></a>

### <a name="input-sample-in-nodejs"></a>Node.js 中的输入示例

```javascript
module.exports = function (context) {   
  context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
  context.bindings.inputDocumentOut.text = "This was updated!";
  context.done();
};
```

## <a id="docdboutput"></a>DocumentDB 输出绑定
DocumentDB 输出绑定允许将新文档写入 Azure DocumentDB 数据库。 

输出绑定在 function.json 的 `bindings` 数组中使用以下 JSON 对象： 

```json
{
  "name": "<Name of output parameter in function signature>",
  "type": "documentDB",
  "databaseName": "<Name of the DocumentDB database>",
  "collectionName": "<Name of the DocumentDB collection>",
  "createIfNotExists": <true or false - see below>,
  "connection": "<Value of AccountEndpoint in Application Setting - see below>",
  "direction": "out"
}
```

注意以下事项：

* 将 `createIfNotExists` 设置为 `true` 以创建数据库和集合（如果不存在）。 默认值为 `false`。 新集合使用保留的吞吐量进行创建，具有定价方面的隐含意义。 有关详细信息，请参阅 [DocumentDB 定价](https://azure.microsoft.com/pricing/details/documentdb/)。
* `connection` 必须是某个应用设置的名称，该设置指向 DocumentDB 帐户的终结点（包含值 `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>`）。 如果通过函数门户 UI 创建 DocumentDB 帐户，则帐户创建过程会创建一个新应用设置。 若要使用现有 DocumentDB 帐户，需要[手动配置此应用设置]()。 

## <a name="output-usage"></a>输出使用情况
本部分演示如何在函数代码中使用 DocumentDB 输出绑定。

当写入函数中的输出参数时，默认情况下数据库中将生成一个新文档，并以自动生成的 GUID 作为文档 ID。 可以通过在输出参数中指定 `id` JSON 属性，来指定输出文档的文档 ID。 

>[!Note]  
>如果指定现有文档的 ID，它会被新的输出文档覆盖。 

可使用以下任意类型写入到输出：

* 任何[对象](https://msdn.microsoft.com/library/system.object.aspx) - 有助于 JSON 序列化。
  如果声明自定义输出类型（例如 `out FooType paramName`），Azure Functions 将尝试将对象序列化为 JSON。 如果函数退出时输出参数为 null，则 Functions 运行时将创建一个 blob 作为 null 对象。
* 字符串 - (`out string paramName`) 适用于文本 blob 数据。 Functions 运行时仅在函数退出时字符串参数为非 null 才创建 blob。

在 C# 函数中，还可输出到以下任意类型：

* `TextWriter`
* `Stream`
* `CloudBlobStream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

若要输出多个文档，还可以绑定到 `ICollector<T>` 或 `IAsyncCollector<T>`，其中 `T` 是受支持的类型之一。


<a name="outputsample"></a>

## <a name="output-sample"></a>输出示例
假设在 function.json 的 `bindings` 数组中有以下 DocumentDB 输出绑定：

```json
{
  "name": "employeeDocument",
  "type": "documentDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "createIfNotExists": true,
  "connection": "MyAccount_DOCUMENTDB",     
  "direction": "out"
}
```

并且拥有一个用于某个队列的队列输入绑定，该队列以下列格式接收 JSON：

```json
{
  "name": "John Henry",
  "employeeId": "123456",
  "address": "A town nearby"
}
```

且需要按下列格式为每个记录创建 DocumentDB 文档：

```json
{
  "id": "John Henry-123456",
  "name": "John Henry",
  "employeeId": "123456",
  "address": "A town nearby"
}
```

请参阅特定于语言的示例，该示例使用此输出绑定将文档添加到数据库。

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>C 中的输出示例# #

```cs
#r "Newtonsoft.Json"

using System;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
{
  log.Info($"C# Queue trigger function processed: {myQueueItem}");

  dynamic employee = JObject.Parse(myQueueItem);

  employeeDocument = new {
    id = employee.name + "-" + employee.employeeId,
    name = employee.name,
    employeeId = employee.employeeId,
    address = employee.address
  };
}
```

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>F 中的输出示例# #

```fsharp
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Employee = {
  id: string
  name: string
  employeeId: string
  address: string
}

let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
  log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
  let employee = JObject.Parse(myQueueItem)
  employeeDocument <-
    { id = sprintf "%s-%s" employee?name employee?employeeId
      name = employee?name
      employeeId = employee?employeeId
      address = employee?address }
```

需要添加指定 `FSharp.Interop.Dynamic` 和 `Dynamitey` NuGet 依赖关系的 `project.json` 文件：

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

若要添加 `project.json` 文件，请参阅 [F# 包管理](functions-reference-fsharp.md#package)。

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Node.js 中的输出示例

```javascript
module.exports = function (context) {

  context.bindings.employeeDocument = JSON.stringify({ 
    id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
    name: context.bindings.myQueueItem.name,
    employeeId: context.bindings.myQueueItem.employeeId,
    address: context.bindings.myQueueItem.address
  });

  context.done();
};
```

