---
title: "Azure Functions 存储 blob 绑定 | Microsoft Docs"
description: "了解如何在 Azure Functions 中使用 Azure 存储触发器和绑定。"
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "Azure Functions，函数，事件处理，动态计算，无服务体系结构"
ms.assetid: aba8976c-6568-4ec7-86f5-410efd6b0fb9
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/06/2017
ms.author: chrande, glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 198a8421636945bdf60c4ed519d065617a7fc287
ms.contentlocale: zh-cn
ms.lasthandoff: 05/15/2017


---
# <a name="azure-functions-blob-storage-bindings"></a>Azure Functions Blob 存储绑定
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

本文介绍如何在 Azure Functions 中配置和编码 Azure 存储 blob 绑定。 Azure Functions 支持 Azure 存储 blob 的触发、输入以及输出绑定。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> 不支持[仅 blob 存储帐户](../storage/storage-create-storage-account.md#blob-storage-accounts)。 Azure Functions 需要一个通用存储帐户与 blob 搭配使用。 
> 
> 

<a name="trigger"></a>

## <a name="storage-blob-trigger"></a>存储 blob 触发器
使用 Azure 存储 blob 触发器可以监视新的和已更新的 blob 的存储容器，并在检测到更改时运行函数代码。 

函数的存储 blob 触发器在 function.json 的 `bindings` 数组中使用以下 JSON 对象：

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "blobTrigger",
    "direction": "in",
    "path": "<container to monitor, and optionally a blob name pattern - see below>",
    "connection":"<Name of app setting - see below>"
}
```

注意以下事项：

* 有关 `path`，请参阅[名称模式](#pattern)了解如何设置 blob 名称模式的格式。
* `connection` 必须包含具有存储连接字符串的应用设置的名称。 在 Azure 门户中，创建存储帐户或选择现有存储帐户时，“集成”选项卡中的标准编辑器会为你配置此应用设置。 若要手动创建此应用设置，请参阅[手动配置此应用设置](functions-how-to-use-azure-function-app-settings.md)。 

基于消耗计划运行时，如果 Function App 处于空闲状态，则在处理新 Blob 时，可能会出现长达 10 分钟的延迟。 Function App 运行以后，Blob 处理速度会加快。 若要避免这种初始延迟，可以使用常规的应用服务计划并启用“始终可用”，或者使用其他机制来触发 Blob 处理，例如使用包含 Blob 名称的队列消息。 

另请参阅以下副标题以了解详细信息：

* [名称模式](#pattern)
* [Blob 回执](#receipts)
* [处理有害 blob](#poison)

<a name="pattern"></a>

### <a name="name-patterns"></a>名称模式
可以在 `path` 属性中指定 blob 名称模式。 例如：

```json
"path": "input/original-{name}",
```

此路径将在输入容器中找到名为 original-Blob1.txt 的 blob，并且函数代码中 `name` 变量的值为 `Blob1`。

另一个示例：

```json
"path": "input/{blobname}.{blobextension}",
```

此路径还会找到名为 original-Blob1.txt 的 blob，并且函数代码中 `blobname` 和 `blobextension` 变量的值为 original-Blob1 和 txt 。

可通过使用文件扩展名的固定值限制 blob 的文件类型。 例如：

```json
"path": "samples/{name}.png",
```

在此情况下，仅示例容器中的 .png blob 触发函数。

大括号在名称模式中为特殊字符。 若要指定名称中包含大括号的 blob 名称，可增加大括号。 例如：

```json
"path": "images/{{20140101}}-{name}",
```

此路径将在映像容器中找到名为 {20140101}-soundfile.mp3 的 blob，函数代码中的 `name` 变量值将为 soundfile .mp3。 

<a name="receipts"></a>

### <a name="blob-receipts"></a>Blob 回执
Azure Functions 运行时确保没有为相同的新 blob 或更新 blob 多次调用 blob 触发器函数。 为此，它会维护 blob 回执，以确定是否已处理给定的 blob 版本。

Blob 回执存储在 Function App（由 `AzureWebJobsStorage` 应用设置指定）的 Azure 存储帐户中名为 azure-webjobs-hosts 的容器中。 Blob 回执包含以下信息：

* 触发的函数（“&lt;function app name>.Functions.&lt;function name>”，例如“functionsf74b96f7.Functions.CopyBlob”）
* 容器名称
* Blob 类型（"BlockBlob" 或 "PageBlob"）
* Blob 名称
* ETag（blob 版本标识符，例如："0x8D1DC6E70A277EF"）

若要强制重新处理某个 blob，可从 azure-webjobs-hosts 容器中手动删除该 blob 的 blob 回执。

<a name="poison"></a>

### <a name="handling-poison-blobs"></a>处理有害 blob
blob 触发函数失败时，Azure Functions 对于给定 blob 默认重试该函数最多 5 次（包括第一次尝试）。 如果 5 次尝试全部失败，Functions 会将消息添加到名为 webjobs-blobtrigger-poison 的存储队列。 有害 Blob 的队列消息是包含以下属性的 JSON 对象：

* FunctionId（格式为 &lt;function app name>.Functions.&lt;function name>）
* BlobType（"BlockBlob" 或 "PageBlob"）
* ContainerName
* BlobName
* ETag（blob 版本标识符，例如："0x8D1DC6E70A277EF"）

### <a name="blob-polling-for-large-containers"></a>大容器的 blob 轮询
如果绑定监视的 blob 容器包含 10,000 多个 blob，则 Functions 运行时将扫描日志文件，监视新的或更改的 blob。 此过程不是实时过程。 创建 blob 之后数分钟或更长时间内可能仍不会触发函数。 此外，[存储日志创建于“尽力而为”的基础上](https://msdn.microsoft.com/library/azure/hh343262.aspx)。 并不能保证捕获了所有事件。 在某些情况下可能会遗漏某些日志。 如果应用程序不接受大容器的 blob 触发器的速度和可靠性限制，建议在创建 blob 时创建[队列消息](../storage/storage-dotnet-how-to-use-queues.md)，并使用[队列触发器](functions-bindings-storage-queue.md)而不是 blob 触发器来处理 blob。

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>触发器使用情况
在 C# 函数中，通过在函数签名中使用命名参数来绑定到输入 blob 数据，如 `<T> <name>`。
其中 `T` 是数据要进行反序列化的目标数据类型，`paramName` 是在[触发器 JSON ](#trigger) 中指定的名称。 在 Node.js 函数中，使用 `context.bindings.<name>` 访问输入 blob 数据。

blob 可以反序列化为以下任何类型：

* 任何[对象](https://msdn.microsoft.com/library/system.object.aspx) - 适用于 JSON 序列化的 blob 数据。
  如果声明自定义输入类型（如 `FooType`），Azure Functions 会尝试将 JSON 数据反序列化为指定类型。
* 字符串 - 适用于文本 blob 数据。

在 C# 函数中，还可绑定到以下任意类型，Functions 运行时将尝试使用此类型反序列化此 blob 数据：

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* 由 [ICloudBlobStreamBinder](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md#icbsb) 反序列化的其他类型 

## <a name="trigger-sample"></a>触发器示例
假设有以下定义存储 blob 触发器的 function.json：

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":""
        }
    ]
}
```

请参阅特定于语言的示例，该示例记录添加到受监视容器中每个 blob 的内容。

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-usage-in-c"></a>C# 中的触发器用法 #

```cs
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

<!--
<a name="triggerfsharp"></a>
### Trigger usage in F# ##
```fsharp

``` 
-->

<a name="triggernodejs"></a>

### <a name="trigger-usage-in-nodejs"></a>Node.js 中触发器的使用情况

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

<a name="input"></a>

## <a name="storage-blob-input-binding"></a>存储 Blob 输入绑定
Azure 存储 blob 输入绑定可让用户在函数中使用存储容器中的 blob。 

函数的存储 blob 输入在 function.json 的 `bindings` 数组中使用以下 JSON 对象：

```json
{
  "name": "<Name of input parameter in function signature>",
  "type": "blob",
  "direction": "in"
  "path": "<Path of input blob - see below>",
  "connection":"<Name of app setting - see below>"
},
```

注意以下事项：

* `path` 必须包含容器名称和 blob 名称。 例如，如果函数中有[队列触发器](functions-bindings-storage-queue.md)，则可以使用 `"path": "samples-workitems/{queueTrigger}"` 指向 `samples-workitems` 容器中的一个 blob，其名称与触发器消息中指定的 blob 名称相匹配。   
* `connection` 必须包含具有存储连接字符串的应用设置的名称。 在 Azure 门户中，创建存储帐户或选择现有存储帐户时，“集成”选项卡中的标准编辑器会为你配置此应用设置。 若要手动创建此应用设置，请参阅[手动配置此应用设置](functions-how-to-use-azure-function-app-settings.md)。 

<a name="inputusage"></a>

## <a name="input-usage"></a>输入使用情况
在 C# 函数中，通过在函数签名中使用命名参数来绑定到输入 blob 数据，如 `<T> <name>`。
其中 `T` 是数据要进行反序列化的目标数据类型，`paramName` 是在[输入绑定](#input)中指定的名称。 在 Node.js 函数中，使用 `context.bindings.<name>` 访问输入 blob 数据。

blob 可以反序列化为以下任何类型：

* 任何[对象](https://msdn.microsoft.com/library/system.object.aspx) - 适用于 JSON 序列化的 blob 数据。
  如果声明自定义输入类型（如 `InputType`），Azure Functions 会尝试将 JSON 数据反序列化为指定类型。
* 字符串 - 适用于文本 blob 数据。

在 C# 函数中，还可绑定到以下任意类型，Functions 运行时将尝试使用此类型反序列化此 blob 数据：

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

<a name="inputsample"></a>

## <a name="input-sample"></a>输入示例
假设有以下定义[存储队列触发器](functions-bindings-storage-queue.md)、存储 blob 输入和存储 blob 输出的 function.json：

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

请参阅将输入 blob 复制到输出 blob 的语言特定示例。

* [C#](#incsharp)
* [Node.js](#innodejs)

<a name="incsharp"></a>

### <a name="input-usage-in-c"></a>C# 中的输入用法 #

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

<!--
<a name="infsharp"></a>
### Input usage in F# ##
```fsharp

``` 
-->

<a name="innodejs"></a>

### <a name="input-usage-in-nodejs"></a>Node.js 中输入使用情况

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

<a name="output"></a>

## <a name="storage-blob-output-binding"></a>存储 Blob 输出绑定
借助 Azure 存储 blob 输出绑定，用户可将 blob 写入到函数中的存储容器。 

函数的存储 blob 输出在 function.json 的 `bindings` 数组中使用以下 JSON 对象：

```json
{
  "name": "<Name of output parameter in function signature>",
  "type": "blob",
  "direction": "out",
  "path": "<Path of input blob - see below>",
  "connection": "<Name of app setting - see below>"
}
```

注意以下事项：

* `path` 必须包含容器名称和要写入的 blob 名称。 例如，如果函数中有[队列触发器](functions-bindings-storage-queue.md)，则可以使用 `"path": "samples-workitems/{queueTrigger}"` 指向 `samples-workitems` 容器中的一个 blob，其名称与触发器消息中指定的 blob 名称相匹配。   
* `connection` 必须包含具有存储连接字符串的应用设置的名称。 在 Azure 门户中，创建存储帐户或选择现有存储帐户时，“集成”选项卡中的标准编辑器会为你配置此应用设置。 若要手动创建此应用设置，请参阅[手动配置此应用设置](functions-how-to-use-azure-function-app-settings.md)。 

<a name="outputusage"></a>

## <a name="output-usage"></a>输出使用情况
在 C# 函数中，通过在函数签名中使用名为 `out` 的参数（如 `out <T> <name>`）绑定到输出 blob，其中 `T` 是要序列化数据的目标数据类型，`paramName` 是在[输出绑定](#output)中指定的名称。 在 Node.js 函数中，使用 `context.bindings.<name>` 访问输出 blob。

可使用以下任意类型写入输出 blob：

* 任何[对象](https://msdn.microsoft.com/library/system.object.aspx) - 有助于 JSON 序列化。
  如果声明自定义输出类型（例如 `out OutputType paramName`），Azure Functions 将尝试将对象序列化为 JSON。 如果函数退出时输出参数为 null，则 Functions 运行时将创建一个 blob 作为 null 对象。
* 字符串 - (`out string paramName`) 适用于文本 blob 数据。 Functions 运行时仅在函数退出时字符串参数为非 null 才创建 blob。

在 C# 函数中，还可输出到以下任意类型：

* `TextWriter`
* `Stream`
* `CloudBlobStream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

<a name="outputsample"></a>

## <a name="output-sample"></a>输出示例
请参阅[输入示例](#inputsample)。

## <a name="next-steps"></a>后续步骤
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


