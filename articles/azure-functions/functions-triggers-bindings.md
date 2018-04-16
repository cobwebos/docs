---
title: Azure Functions 中的触发器和绑定
description: 了解如何使用 Azure Functions 中的触发器和绑定将代码执行连接到联机事件和基于云的服务。
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: Azure Functions, Functions, 事件处理, webhook, 动态计算, 无服务体系结构
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/07/2018
ms.author: glenga
ms.openlocfilehash: d008e94186dd3c5a18c92617ebddfffa966358d5
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions 触发器和绑定概念

本文提供有关 Azure Functions 中触发器和绑定的概念性概述。 此处介绍所有绑定和支持的语言通用的功能。

## <a name="overview"></a>概述

“触发器”定义函数的调用方式。 一个函数必须只有一个触发器。 触发器具有关联数据，该数据通常是触发函数的有效负载。

输入和输出“绑定”提供从代码内连接到数据的声明性方式。 绑定是可选项，一个函数可以有多个输入和输出绑定。 

借助触发器和绑定，可避免对正在使用的服务的详细信息进行硬编码。 函数接收函数参数中的数据（例如，队列消息内容）。 使用函数的返回值发送数据（例如，用于创建队列消息），返回值可能是 `out` 参数或[收集器对象](functions-reference-csharp.md#writing-multiple-output-values)。

使用 Azure 门户开发函数时，会在 function.json 文件中配置触发器和绑定。 门户提供此配置的 UI，但可通过更换为“高级编辑器”，直接编辑文件。

当使用 Visual Studio 开发一个用于创建类库的函数时，通过使用属性修饰方法和参数来配置触发器和绑定。

## <a name="supported-bindings"></a>支持的绑定

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

有关哪些绑定处于预览状态或已批准在生产环境中使用的信息，请参阅[支持的语言](supported-languages.md)。

## <a name="register-binding-extensions"></a>注册绑定扩展

在版本 2.x 的 Azure Functions 运行时中，必须显式注册在函数应用中使用的[绑定扩展](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/README.md)。 

扩展以 NuGet 包的形式提供，其中，包名称通常以 [microsoft.azure.webjobs.extensions](https://www.nuget.org/packages?q=microsoft.azure.webjobs.extensions) 开头。  安装和注册绑定扩展的方式取决于函数的开发方式： 

+ [在本地使用 Visual Studio 或 VS Code 进行 C# 开发](#local-c-development-using-visual-studio-or-vs-code)
+ [在本地使用 Azure Functions Core Tools](#local-development-azure-functions-core-tools)
+ [在 Azure 门户中](#azure-portal-development) 

在版本 2.x 中，有一组不是以扩展形式提供的核心绑定。 不需要注册以下触发器和绑定的扩展：HTTP、计时器和 Azure 存储。 

有关如何将函数应用设置为使用 Functions 运行时版本 2.x 的信息，请参阅[如何指定 Azure Functions 运行时的目标版本](set-runtime-version.md)。 Functions 运行时版本 2.x 目前以预览版提供。 

本部分中所示的包版本仅用作示例。 请检查 [NuGet.org 站点](https://www.nuget.org/packages?q=microsoft.azure.webjobs.extensions)，确定函数应用中的其他依赖项需要哪个版本的给定扩展。    

###  <a name="local-c-development-using-visual-studio-or-vs-code"></a>使用 Visual Studio 或 VS Code 进行本地 C# 开发 

使用 Visual Studio 或 Visual Studio Code 在本地开发 C# 函数时，只需添加扩展的 NuGet 包。 

+ **Visual Studio**：使用 NuGet 包管理器工具。 以下 [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) 命令从包管理器控制台安装 Azure Cosmos DB 扩展：

    ```
    Install-Package Microsoft.Azure.WebJobs.Extensions.CosmosDB -Version 3.0.0-beta6 
    ```
+ **Visual Studio Code**：可以在 .NET CLI 中，通过命令提示符使用 [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) 命令来安装包，如下所示：

    ```
    dotnet add package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version 3.0.0-beta6 
    ```

### <a name="local-development-azure-functions-core-tools"></a>使用 Azure Functions Core Tools 进行本地开发

[!INCLUDE [Full bindings table](../../includes/functions-core-tools-install-extension.md)]

### <a name="azure-portal-development"></a>使用 Azure 门户进行开发

创建函数或者将绑定添加到现有函数时，如果所添加的触发器或绑定的扩展需要注册，则系统会显示提示。   

在系统针对所安装的特定扩展显示警告后，请单击“安装”注册该扩展。 对于给定的函数应用，只需安装每个扩展一次。 

>[!Note] 
>在消耗计划中，门户内安装进程最多需要 10 分钟。

## <a name="example-trigger-and-binding"></a>示例触发器和绑定

假设希望在 Azure 队列存储中显示一条新消息时就将一个新行写入 Azure 表存储。 使用 Azure 队列存储触发器和 Azure 表存储输出绑定即可实现此方案。 

下面是用于这种方案的 function.json 文件。 

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "name": "$return",
      "type": "table",
      "direction": "out",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

`bindings` 数组中的第一个元素是队列存储触发器。 `type` 和 `direction` 属性标识该触发器。 `name` 属性标识接收队列消息内容的函数参数。 要监视的队列的名称位于 `queueName` 中，连接字符串位于由 `connection` 标识的应用设置中。

`bindings` 数组中的第二个元素是 Azure 表存储输出绑定。 `type` 和 `direction` 属性标识该绑定。 `name` 属性指定函数提供新表行的方式，在此例中是使用函数返回值来提供。 表格的名称位于 `tableName` 中，连接字符串位于由 `connection` 标识的应用设置中。

若要在 Azure 门户中查看和编辑 *function.json* 的内容，请单击函数“集成”选项卡上的“高级编辑器”选项。

> [!NOTE]
> `connection` 的值是包含连接字符串的应用设置的名称，而不是连接字符串本身的名称。 绑定使用应用设置中存储的连接字符串，以强制执行 function.json 不包含服务密钥这一最佳做法。

以下是适用于此触发器和绑定的 C# 脚本代码。 请注意，提供队列消息内容的参数的名称是 `order`；需使用此名称是因为 function.json 中的 `name` 属性值是 `order` 

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, TraceWriter log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

可将同一 function.json 文件用于 JavaScript 函数：

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

在类库中，由特性而不是 function.json 文件提供这些触发器和绑定信息 &mdash; 队列和表名称、存储帐户、输入和输出 &mdash; 的函数参数。 下面是一个示例：

```csharp
 public static class QueueTriggerTableOutput
 {
     [FunctionName("QueueTriggerTableOutput")]
     [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
     public static Person Run(
         [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order, 
         TraceWriter log)
     {
         return new Person() {
                 PartitionKey = "Orders",
                 RowKey = Guid.NewGuid().ToString(),
                 Name = order["Name"].ToString(),
                 MobileNumber = order["MobileNumber"].ToString() };
     }
 }

 public class Person
 {
     public string PartitionKey { get; set; }
     public string RowKey { get; set; }
     public string Name { get; set; }
     public string MobileNumber { get; set; }
 }
```

## <a name="binding-direction"></a>绑定方向

所有触发器和绑定在 *function.json* 文件中都有一个 `direction` 属性：

- 对于触发器，方向始终为 `in`
- 输入和输出绑定使用 `in` 和 `out`
- 某些绑定支持特殊方向 `inout`。 如果使用 `inout`，则“集成”选项卡中仅“高级编辑器”可用。

使用[类库中的特性](functions-dotnet-class-library.md)来配置触发器和绑定时，方向在特性构造函数中提供或推断自参数类型。

## <a name="using-the-function-return-value"></a>使用函数返回值

在提供返回值的语言中，可将输出绑定绑定到返回值：

* 在 C# 类库，请将输出绑定特性应用到方法返回值。
* 在其他语言中，请将 *function.json* 中的 `name` 属性设置为 `$return`。

如果需要编写多个项，请使用[收集器对象](functions-reference-csharp.md#writing-multiple-output-values)，而不要使用返回值。 如果有多个输出绑定，请只使用其中一个绑定的返回值。

参阅语言特定的示例：

* [C#](#c-example)
* [C# 脚本 (.csx)](#c-script-example)
* [F#](#f-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>C# 示例

以下 C# 代码使用输出绑定的返回值，后接异步示例：

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

### <a name="c-script-example"></a>C# 脚本示例

下面是 *function.json* 文件中的输出绑定：

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

下面是 C# 脚本代码，后接异步示例：

```cs
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

### <a name="f-example"></a>F# 示例

下面是 *function.json* 文件中的输出绑定：

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

F# 代码如下所示：

```fsharp
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

### <a name="javascript-example"></a>JavaScript 示例

下面是 *function.json* 文件中的输出绑定：

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

在 JavaScript 中，返回值位于 `context.done` 的第二个参数中：

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

## <a name="binding-datatype-property"></a>绑定 dataType 属性

在 .NET中，使用参数类型来定义输入数据的数据类型。 例如，使用 `string` 绑定到队列触发器的文本、一个要读取为二进制内容的字节数组，以及一个要反序列化为 POCO 对象的自定义类型。

对于动态键入的语言（如 JavaScript），请在 function.json 文件中使用 `dataType` 属性。 例如，若要以二进制格式读取 HTTP 请求的内容，将 `dataType` 设置为 `binary`：

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

`dataType` 的其他选项是 `stream` 和 `string`。

## <a name="binding-expressions-and-patterns"></a>绑定表达式和模式

触发器和绑定的最强大功能之一是*绑定表达式*。 在 *function.json* 文件、函数参数和代码中，可以使用表达式解析为各种源的值。

大多数表达式的标识方式是将其包装在大括号中。 例如，在队列触发器函数中，`{queueTrigger}` 解析为队列消息文本。 如果 Blob 输出绑定的 `path` 属性为 `container/{queueTrigger}`，并且函数由队列消息 `HelloWorld` 触发，则创建名为 `HelloWorld` 的 Blob。

绑定表达式的类型

* [应用设置](#binding-expressions---app-settings)
* [触发器文件名](#binding-expressions---trigger-file-name)
* [触发器元数据](#binding-expressions---trigger-metadata)
* [JSON 有效负载](#binding-expressions---json-payloads)
* [新 GUID](#binding-expressions---create-guids)
* [当前日期和时间](#binding-expressions---current-time)

### <a name="binding-expressions---app-settings"></a>绑定表达式 - 应用设置

作为最佳做法，应使用应用设置（而不是配置文件）来管理密钥和连接字符串。 这会限制对这些密钥的访问，并可在公共源代码管理存储库中安全存储 *function.json* 等文件。

应用设置在想要根据环境更改配置时也很有用。 例如，在测试环境中，可能想要监视不同的队列或 blob 存储容器。

应用设置绑定表达式的标识方式不同于其他绑定表达式：它们包装在百分比号而不是大括号中。 例如，如果 Blob 输出绑定路径为 `%Environment%/newblob.txt`，`Environment` 应用设置值为 `Development`，则会在 `Development` 容器中创建 Blob。

当函数在本地运行时，应用设置值来自 *local.settings.json* 文件。

请注意，触发器和绑定的 `connection` 属性是一种特殊情况，该属性会自动将值解析为应用设置（不带百分比号）。 

以下示例是一个 Azure 队列存储触发器，该触发器使用应用设置 `%input-queue-name%` 定义要触发的队列。

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input-queue-name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

可在类库中使用此相同的方法：

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input-queue-name%")]string myQueueItem, 
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
}
```

### <a name="binding-expressions---trigger-file-name"></a>绑定表达式 - 触发器文件名

Blob 触发器的 `path` 可以是一种模式，用于引用其他绑定和函数代码中的触发 Blob 的名称。 该模式还可包含筛选条件，用于指定哪些 Blob 可以触发函数调用。

例如，在以下 Blob 触发器绑定中，`path` 模式为 `sample-images/{filename}`（创建名为 `filename` 的绑定表达式）：

```json
{
  "bindings": [
    {
      "name": "image",
      "type": "blobTrigger",
      "path": "sample-images/{filename}",
      "direction": "in",
      "connection": "MyStorageConnection"
    },
    ...
```

然后，可以在输出绑定中使用表达式 `filename`，用于指定所创建的 Blob 的名称：

```json
    ...
    {
      "name": "imageSmall",
      "type": "blob",
      "path": "sample-images-sm/{filename}",
      "direction": "out",
      "connection": "MyStorageConnection"
    }
  ],
}
```

函数代码可以使用 `filename` 作为参数名称来访问相同的值：

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, TraceWriter log)  
{
    log.Info($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

类库中的特性同样能够使用绑定表达式和模式。 在以下示例中，特性构造函数参数的值与前面 *function.json* 示例中的 `path` 值相同： 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    TraceWriter log)
{
    log.Info($"Blob trigger processing: {filename}");
    // ...
}

```

还可为文件名的某些部分（例如扩展）创建表达式。 有关如何在 Blob 路径字符串中使用表达式和模式的详细信息，请参阅[存储 Blob 绑定参考](functions-bindings-storage-blob.md)。
 
### <a name="binding-expressions---trigger-metadata"></a>绑定表达式 - 触发器元数据

除了触发器提供的数据有效负载（如触发函数的队列消息内容），许多触发器还会提供其他元数据值。 这些值可用作 C# 和 F# 中的输入参数，或用作 JavaScript 中 `context.bindings` 对象的属性。 

例如，Azure 队列存储触发器支持以下属性：

* QueueTrigger - 如果字符串有效，将触发消息内容
* DequeueCount
* ExpirationTime
* ID
* InsertionTime
* NextVisibleTime
* PopReceipt

这些元数据值可在 function.json 文件属性中访问。 例如，假设使用队列触发器，且队列消息中包含要读取的 blob 的名称。 在 function.json 文件中，可在 blob `path` 属性中使用 `queueTrigger` 元数据属性，如下面的示例中所示：

```json
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "direction": "in",
      "connection": "MyStorageConnection"
    }
  ]
```

相应参考文章中会详细介绍每种触发器的元数据属性。 有关示例，请参阅[队列触发器元数据](functions-bindings-storage-queue.md#trigger---message-metadata)。 在门户“集成”选项卡的绑定配置区域下方的“文档”部分中，还提供了文档。  

### <a name="binding-expressions---json-payloads"></a>绑定表达式 - JSON 有效负载

当触发器有效负载为 JSON 时，可以在相同的函数和函数代码中，引用其他绑定的配置中的相应属性。

以下示例演示一个接收 JSON 格式 Blob 名称的 Webhook 函数的 *function.json* 文件：`{"BlobName":"HelloWorld.txt"}`。 Blob 输入绑定读取 Blob，HTTP 输出绑定在 HTTP 响应中返回 Blob 内容。 可以看到，Blob 输入绑定通过直接引用 `BlobName` 属性来获取 Blob 名称 (`"path": "strings/{BlobName}"`)

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson"
    },
    {
      "name": "blobContents",
      "type": "blob",
      "direction": "in",
      "path": "strings/{BlobName.FileName}.{BlobName.Extension}",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

要在 C# 和 F# 中运行此代码，需要使用一个类来定义要反序列化的字段，如以下示例中所示：

```csharp
using System.Net;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

在 JavaScript 中，会自动执行 JSON 反序列化。

```javascript
module.exports = function (context, info) {
    if ('BlobName' in info) {
        context.res = {
            body: { 'data': context.bindings.blobContents }
        }
    }
    else {
        context.res = {
            status: 404
        };
    }
    context.done();
}
```

#### <a name="dot-notation"></a>点表示法

如果 JSON 有效负载中的某些属性是包含属性的对象，可以使用点表示法直接引用这些对象。 例如，假设 JSON 如下所示：

```json
{"BlobName": {
  "FileName":"HelloWorld",
  "Extension":"txt"
  }
}
```

可以直接以 `BlobName.FileName` 的形式引用 `FileName`。 使用此 JSON 格式时，上述示例中的 `path` 属性如下所示：

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

在 C# 中，需要两个类：

```csharp
public class BlobInfo
{
    public BlobName BlobName { get; set; }
}
public class BlobName
{
    public string FileName { get; set; }
    public string Extension { get; set; }
}
```

### <a name="binding-expressions---create-guids"></a>绑定表达式 - 创建 GUID

`{rand-guid}` 绑定表达式用于创建 GUID。 `function.json` 文件中的以下 Blob 路径创建名称类似于 *50710cb5-84b9-4d87-9d83-a03d6976a682.txt* 的 Blob。

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="binding-expressions---current-time"></a>绑定表达式 - 当前时间

绑定表达式 `DateTime` 解析为 `DateTime.UtcNow`。 `function.json` 文件中的以下 Blob 路径创建名称类似于 *2018-02-16T17-59-55Z.txt* 的 Blob。

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="binding-at-runtime"></a>在运行时绑定

在 C# 和其他 .NET 语言中，可以使用命令性绑定模式，而不是 function.json 和特性中的声明式绑定。 当绑定参数需要在运行时（而非在设计时）计算时，命令性绑定很有用。 若要了解详细信息，请参阅 [C# 开发人员参考](functions-dotnet-class-library.md#binding-at-runtime)或 [C# 脚本开发人员参考](functions-reference-csharp.md#binding-at-runtime)。

## <a name="functionjson-file-schema"></a>function.json 文件架构

*function.json* 文件架构位于 [http://json.schemastore.org/function](http://json.schemastore.org/function)。

## <a name="handling-binding-errors"></a>处理绑定错误

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

有关 Functions 所支持各种服务的所有相关错误主题链接，请参阅 [Azure Functions 错误处理](functions-bindings-error-pages.md)概述主题的[绑定错误代码](functions-bindings-error-pages.md#binding-error-codes)部分。  

## <a name="next-steps"></a>后续步骤

有关特定绑定的详细信息，请参阅以下文章：

- [HTTP 和 webhook](functions-bindings-http-webhook.md)
- [计时器](functions-bindings-timer.md)
- [队列存储](functions-bindings-storage-queue.md)
- [Blob 存储](functions-bindings-storage-blob.md)
- [表存储](functions-bindings-storage-table.md)
- [事件中心](functions-bindings-event-hubs.md)
- [服务总线](functions-bindings-service-bus.md)
- [Azure Cosmos DB](functions-bindings-cosmosdb.md)
- [Microsoft Graph](functions-bindings-microsoft-graph.md)
- [SendGrid](functions-bindings-sendgrid.md)
- [Twilio](functions-bindings-twilio.md)
- [通知中心](functions-bindings-notification-hubs.md)
- [移动应用](functions-bindings-mobile-apps.md)
- [外部文件](functions-bindings-external-file.md)
