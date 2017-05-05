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
ms.date: 04/18/2016
ms.author: chrande; glenga
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: e38c9187be42946df1e8059ba44f10f76d32d984
ms.lasthandoff: 04/21/2017


---
# <a name="azure-functions-documentdb-bindings"></a>Azure Functions DocumentDB 绑定
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

本文介绍如何在 Azure Functions 中为 Azure DocumentDB 绑定进行配置和编写代码。 Azure Functions 支持 DocumentDB 的输入和输出绑定。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

有关 DocumentDB 的详细信息，请参阅 [DocumentDB 简介](../documentdb/documentdb-introduction.md)和[生成 DocumentDB 控制台应用程序](../documentdb/documentdb-get-started.md)。

<a id="docdbinput"></a>

## <a name="documentdb-input-binding"></a>DocumentDB 输入绑定
DocumentDB 输入绑定检索 DocumentDB 文档，并将其传递给函数的命名输入参数。 可根据调用函数的触发器确定文档 ID。 

function.json 中，DocumentDB 输入绑定具有以下属性：

- `name`：在文档的函数代码中使用的标识符名称
- `type`：必须设置为“documentdb”
- `databaseName`：包含文档的数据库
- `collectionName`：包含文档的集合
- `id`：要检索的文档的 ID。 此属性支持绑定参数；请参阅 [Azure Functions 触发器和绑定概念](functions-triggers-bindings.md)一文中的[绑定到绑定表达式中的自定义输入属性](functions-triggers-bindings.md#bind-to-custom-input-properties-in-a-binding-expression)。
- `sqlQuery`：用于检索多个文档的 DocumentDB SQL 查询。 该查询支持运行时绑定。 例如： `SELECT * FROM c where c.departmentId = {departmentId}`
- `connection`：内含 DocumentDB 连接字符串的应用设置的名称
- `direction`：必须设置为 `"in"`。

无法同时指定属性 `id` 和 `sqlQuery`。 如果 `id` 和 `sqlQuery` 均未设置，则检索整个集合。

## <a name="using-a-documentdb-input-binding"></a>使用 DocumentDB 输入绑定

* 在 C# 函数和 F# 函数中，函数成功退出后，通过命名输入参数对输入文档所做的任何更改都将自动进行。 
* 在 JavaScript 函数中，函数退出时不会自动进行更新。 请改用 `context.bindings.<documentName>In` 和 `context.bindings.<documentName>Out` 进行更新。 请参阅 [JavaScript 示例](#injavascript)。

<a name="inputsample"></a>

## <a name="input-sample-for-single-document"></a>单个文档的的输入示例
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
* [JavaScript](#injavascript)

<a name="incsharp"></a>
### <a name="input-sample-in-c"></a>C 中的输入示例# #

```cs
// Change input document contents using DocumentDB input binding 
public static void Run(string myQueueItem, dynamic inputDocument)
{   
  inputDocument.text = "This has changed.";
}
```
<a name="infsharp"></a>

### <a name="input-sample-in-f"></a>F 中的输入示例# #

```fsharp
(* Change input document contents using DocumentDB input binding *)
open FSharp.Interop.Dynamic
let Run(myQueueItem: string, inputDocument: obj) =
  inputDocument?text <- "This has changed."
```

此示例要求具有指定 `FSharp.Interop.Dynamic` 和 `Dynamitey` NuGet 依赖关系的 `project.json` 文件：

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

<a name="injavascript"></a>

### <a name="input-sample-in-javascript"></a>JavaScript 中的输入示例

```javascript
// Change input document contents using DocumentDB input binding, using context.bindings.inputDocumentOut
module.exports = function (context) {   
  context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
  context.bindings.inputDocumentOut.text = "This was updated!";
  context.done();
};
```

## <a name="input-sample-with-multiple-documents"></a>多个文档的输入示例

假设要检索 SQL 查询中指定的多个文档，请使用队列触发器自定义查询参数。 

在此示例中，队列触发器提供 `departmentId` 参数。`{ "departmentId" : "Finance" }` 队列消息将返回财务部门的所有记录。 在 function.json 中使用以下代码：

```
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}"
    "connection": "DocumentDBConnection"
}
```

### <a name="input-sample-with-multiple-documents-in-c"></a>C 中多个文档的输入示例#

```csharp
public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
{   
    foreach (var doc in documents)
    {
        // operate on each document
    }    
}

public class QueuePayload
{
    public string departmentId { get; set; }
}
```

### <a name="input-sample-with-multiple-documents-in-javascript"></a>JavaScript 中多个文档的输入示例

```javascript
module.exports = function (context, input) {    
    var documents = context.bindings.documents;
    for (var i = 0; i < documents.length; i++) {
        var document = documents[i];
        // operate on each document
    }        
    context.done();
};
```

## <a id="docdboutput"></a>DocumentDB 输出绑定
DocumentDB 输出绑定允许将新文档写入 Azure DocumentDB 数据库。 在 function.json 中，该输出绑定具有以下属性：

- `name`：在新文档的函数代码中使用的标识符
- `type`：必须设置为 `"documentdb"`
- `databaseName`：包含将在其中创建新文档的集合的数据库。
- `collectionName`：将在其中创建新文档的集合。
- `createIfNotExists`：一个用于指示是否创建集合（如果不存在）的布尔值。 默认值为 *false*。 其原因是新集合使用保留吞吐量进行创建，这会影响定价。 有关详细信息，请访问[定价页](https://azure.microsoft.com/pricing/details/documentdb/)。
- `connection`：内含 DocumentDB 连接字符串的应用设置的名称
- `direction`：必须设置为 `"out"`

## <a name="using-a-documentdb-output-binding"></a>使用 DocumentDB 输出绑定
本部分演示如何在函数代码中使用 DocumentDB 输出绑定。

当写入函数中的输出参数时，默认情况下数据库中将生成一个新文档，并以自动生成的 GUID 作为文档 ID。 可以通过在输出参数中指定 `id` JSON 属性，来指定输出文档的文档 ID。 

>[!Note]  
>如果指定现有文档的 ID，它会被新的输出文档覆盖。 

若要输出多个文档，还可以绑定到 `ICollector<T>` 或 `IAsyncCollector<T>`，其中 `T` 是受支持的类型之一。

<a name="outputsample"></a>

## <a name="documentdb-output-binding-sample"></a>DocumentDB 输出绑定示例
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
* [JavaScript](#outjavascript)

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

此示例要求具有指定 `FSharp.Interop.Dynamic` 和 `Dynamitey` NuGet 依赖关系的 `project.json` 文件：

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

<a name="outjavascript"></a>

### <a name="output-sample-in-javascript"></a>JavaScript 中的输出示例

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

