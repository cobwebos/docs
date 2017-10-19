---
title: "适用于函数的 Azure Cosmos DB 绑定 | Microsoft Docs"
description: "了解如何在 Azure Functions 中使用 Azure Cosmos DB 触发器和绑定。"
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions，函数，事件处理，动态计算，无服务体系结构"
ms.assetid: 3d8497f0-21f3-437d-ba24-5ece8c90ac85
ms.service: functions; cosmos-db
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/19/2017
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ad058929eb888920823fddf549ada4ce2c6d9eee
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="azure-cosmos-db-bindings-for-functions"></a>适用于函数的 Azure Cosmos DB 绑定
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

本文介绍如何在 Azure Functions 中为 Azure Cosmos DB 绑定进行配置和编写代码。 函数支持 Azure Cosmos DB 的触发器、输入和输出绑定。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

有关无服务器计算及 Azure Cosmos DB 的详细信息，请参阅 [Azure Cosmos DB：使用 Azure Functions 的无服务器数据库计算](..\cosmos-db\serverless-computing-database.md)。

<a id="trigger"></a>
<a id="cosmosdbtrigger"></a>

## <a name="azure-cosmos-db-trigger"></a>Azure Cosmos DB 触发器

Azure Cosmos DB 触发器使用 [Azure Cosmos DB 更改源](../cosmos-db/change-feed.md)来侦听跨分区的更改。 触发器需要第二个集合，该集合用于存储各分区的_租用_。

必须提供受监视的集合和包含租用的集合，触发器才能正常工作。

Azure Cosmos DB 触发器支持以下属性：

|属性  |说明  |
|---------|---------|
|**类型** | 必须设置为 `cosmosDBTrigger`。 |
|**name** | 函数代码中使用的变量名称，表示发生更改的文档列表。 | 
|**direction** | 必须设置为 `in`。 在 Azure 门户中创建触发器时，会自动设置该参数。 |
|**connectionStringSetting** | 应用设置的名称，该应用设置包含用于连接到受监视的 Azure Cosmos DB 帐户的连接字符串。 |
|**databaseName** | 带有受监视的集合的 Azure Cosmos DB 数据库的名称。 |
|**collectionName** | 受监视的集合的名称。 |
| **leaseConnectionStringSetting** | （可选）应用设置的名称，该应用设置包含指向保留租用集合的服务的连接字符串。 未设置时，使用 `connectionStringSetting` 值。 在门户中创建绑定时，将自动设置该参数。 |
| **leaseDatabaseName** | （可选）数据库的名称，该数据库包含用于存储租用的集合。 未设置时，使用 `databaseName` 设置的值。 在门户中创建绑定时，将自动设置该参数。 |
| **leaseCollectionName** | （可选）用于存储租用的集合的名称。 未设置时，使用值 `leases`。 |
| **createLeaseCollectionIfNotExists** | （可选）设置为 `true` 时，如果租用集合并不存在，将自动创建该集合。 默认值为 `false`。 |
| **leaseCollectionThroughput** | （可选）在创建租用集合时，定义要分配的请求单位的数量。 仅当 `createLeaseCollectionIfNotExists` 设置为 `true` 时，才会使用该设置。 使用门户创建绑定时，将自动设置该参数。

>[!NOTE] 
>用于连接到租用集合的连接字符串必须具有写入权限。

可以在 Azure 门户中的函数“集成”选项卡中设置这些属性，或者通过编辑 `function.json` 项目文件进行设置。

## <a name="using-an-azure-cosmos-db-trigger"></a>使用 Azure Cosmos DB 触发器

本部分包含如何使用 Azure Cosmos DB 触发器的示例。 这些示例假定触发器元数据如下所示：

```json
{
  "type": "cosmosDBTrigger",
  "name": "documents",
  "direction": "in",
  "leaseCollectionName": "leases",
  "connectionStringSetting": "<connection-app-setting>",
  "databaseName": "Tasks",
  "collectionName": "Items",
  "createLeaseCollectionIfNotExists": true
}
```
 
有关如何在门户中从函数应用创建 Azure Cosmos DB 触发器的示例，请参阅[创建由 Azure Cosmos DB 触发的函数](functions-create-cosmos-db-triggered-function.md)。 

### <a name="trigger-sample-in-c"></a>C# 中的触发器示例 #
```cs 
    #r "Microsoft.Azure.Documents.Client"
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using System;
    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
        log.Verbose("Documents modified " + documents.Count);
        log.Verbose("First document Id " + documents[0].Id);
    }
```


### <a name="trigger-sample-in-javascript"></a>JavaScript 中的触发器示例
```javascript
    module.exports = function (context, documents) {
        context.log('First document Id modified : ', documents[0].id);

        context.done();
    }
```

<a id="docdbinput"></a>

## <a name="documentdb-api-input-binding"></a>DocumentDB API 输入绑定
DocumentDB API 输入绑定会检索 Azure Cosmos DB 文档，并将其传递给函数的命名输入参数。 可根据调用函数的触发器确定文档 ID。 

在 *function.json* 中，DocumentDB API 输入绑定具有以下属性：

|属性  |说明  |
|---------|---------|
|**name**     | 表示函数中的文档的绑定参数的名称。  |
|**类型**     | 必须设置为 `documentdb`。        |
|**databaseName** | 包含文档的数据库。        |
|**collectionName**  | 包含文档的集合的名称。 |
|**id**     | 要检索的文档的 ID。 此属性支持绑定参数。 有关详细信息，请参阅[绑定到绑定表达式中的自定义输入属性](functions-triggers-bindings.md#bind-to-custom-input-properties-in-a-binding-expression)。 |
|**sqlQuery**     | 用于检索多个文档的 Azure Cosmos DB SQL 查询。 该查询支持运行时绑定，如以下示例所示：`SELECT * FROM c where c.departmentId = {departmentId}`。        |
|**连接**     |内含 Azure Cosmos DB 连接字符串的应用设置的名称。        |
|**direction**     | 必须设置为 `in`。         |

不能同时设置 ID 和 sqlQuery 属性。 如果两者均未设置，则检索整个集合。

## <a name="using-a-documentdb-api-input-binding"></a>使用 DocumentDB API 输入绑定

* 在 C# 函数和 F# 函数中，函数成功退出后，通过命名输入参数对输入文档所做的任何更改都会自动保存。 
* 在 JavaScript 函数中，函数退出时不会自动进行更新。 请改用 `context.bindings.<documentName>In` 和 `context.bindings.<documentName>Out` 进行更新。 请参阅 [JavaScript 示例](#injavascript)。

<a name="inputsample"></a>

## <a name="input-sample-for-single-document"></a>单个文档的的输入示例
假设在 function.json 的 `bindings` 数组中有以下 DocumentDB API 输入绑定：

```json
{
  "name": "inputDocument",
  "type": "documentDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "id" : "{queueTrigger}",
  "connection": "MyAccount_COSMOSDB",     
  "direction": "in"
}
```

请参阅特定于语言的示例，该示例使用此输入绑定更新文档的文本值。

* [C#](#incsharp)
* [F#](#infsharp)
* [JavaScript](#injavascript)

<a name="incsharp"></a>
### <a name="input-sample-in-c"></a>C# 中的输入示例 #

```cs
// Change input document contents using DocumentDB API input binding 
public static void Run(string myQueueItem, dynamic inputDocument)
{   
  inputDocument.text = "This has changed.";
}
```
<a name="infsharp"></a>

### <a name="input-sample-in-f"></a>F# 中的输入示例 #

```fsharp
(* Change input document contents using DocumentDB API input binding *)
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
// Change input document contents using DocumentDB API input binding, using context.bindings.inputDocumentOut
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
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

### <a name="input-sample-with-multiple-documents-in-c"></a>C# 中多个文档的输入示例

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

## <a id="docdboutput"></a>DocumentDB API 输出绑定
DocumentDB API 输出绑定允许将新文档写入 Azure Cosmos DB 数据库。 在 function.json 中，该输出绑定具有以下属性：

|属性  |说明  |
|---------|---------|
|**name**     | 表示函数中的文档的绑定参数的名称。  |
|**类型**     | 必须设置为 `documentdb`。        |
|**databaseName** | 包含在其中创建文档的集合的数据库。     |
|**collectionName**  | 包含在其中创建文档的集合的名称。 |
|**createIfNotExists**     | 一个用于指示是否创建集合（如果不存在）的布尔值。 默认值为 *false*。 这是因为新集合是使用保留的吞吐量创建的，具有成本方面的隐含意义。 有关详细信息，请访问[定价页](https://azure.microsoft.com/pricing/details/documentdb/)。  |
|**连接**     |内含 Azure Cosmos DB 连接字符串的应用设置的名称。        |
|**direction**     | 必须设置为 `out`。         |

## <a name="using-a-documentdb-api-output-binding"></a>使用 DocumentDB API 输出绑定
本部分演示如何在函数代码中使用 DocumentDB API 输出绑定。

默认情况下，当写入函数中的输出参数时，将在数据库中创建一个文档。 本文档将自动生成的 GUID 作为文档 ID。 可以通过在传递给输出参数的 JSON 对象中指定 `id` 属性来指定输出文档的文档 ID。 

>[!Note]  
>如果指定现有文档的 ID，它会被新的输出文档覆盖。 

若要输出多个文档，还可以绑定到 `ICollector<T>` 或 `IAsyncCollector<T>`，其中 `T` 是受支持的类型之一。

<a name="outputsample"></a>

## <a name="documentdb-api-output-binding-sample"></a>DocumentDB API 输出绑定示例
假设在 function.json 的 `bindings` 数组中有以下 DocumentDB API 输出绑定：

```json
{
  "name": "employeeDocument",
  "type": "documentDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "createIfNotExists": true,
  "connection": "MyAccount_COSMOSDB",     
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

并且需要按下列格式为每个记录创建 Azure Cosmos DB 文档：

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

### <a name="output-sample-in-c"></a>C# 中的输出示例 #

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

### <a name="output-sample-in-f"></a>F# 中的输出示例 #

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

