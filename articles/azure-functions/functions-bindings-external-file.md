---
title: Azure Functions 的外部文件绑定（实验性）
description: 在 Azure Functions 中使用外部文件绑定
services: functions
documentationcenter: ''
author: alexkarcher-msft
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/27/2017
ms.author: alkarche
ms.openlocfilehash: 4e9c2c336df465d7488de84bd2a02cc5d9e42f30
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2018
ms.locfileid: "27607915"
---
# <a name="azure-functions-external-file-bindings-experimental"></a>Azure Functions 外部文件绑定（实验性）
本文演示如何在 Azure Functions 中通过不同 SaaS 提供程序（例如 Dropbox 或 Google Drive）处理文件。 Azure Functions 支持外部文件的触发器、输入和输出绑定。 这些绑定会创建与 SaaS 提供程序的 API 连接，或使用 Function App 的资源组中的现有 API 连接。

> [!IMPORTANT]
> 外部文件绑定是实验性的，可能永远不会达到正式发布 (GA) 状态。 这些文件绑定仅包括在 Azure Functions 1.x 中，没有计划将它们添加到 Azure Functions 2.x。 对于需要访问 SaaS 提供程序中数据的方案，请考虑使用[调用函数的逻辑应用](functions-twitter-email.md)。 请参阅[逻辑应用文件系统连接器](../logic-apps/logic-apps-using-file-connector.md)。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="available-file-connections"></a>可用文件连接

|连接器|触发器|输入|输出|
|:-----|:---:|:---:|:---:|
|[Box](https://www.box.com)|x|x|x
|[Dropbox](https://www.dropbox.com)|x|x|x
|[FTP](https://docs.microsoft.com/azure/app-service/app-service-deploy-ftp)|x|x|x
|[OneDrive](https://onedrive.live.com)|x|x|x
|[OneDrive for Business](https://onedrive.live.com/about/business/)|x|x|x
|[SFTP](https://docs.microsoft.com/azure/connectors/connectors-create-api-sftp)|x|x|x
|[Google Drive](https://www.google.com/drive/)||x|x|

> [!NOTE]
> 还可以在 [Azure 逻辑应用](https://docs.microsoft.com/azure/connectors/apis-list)中使用外部文件连接。

## <a name="trigger"></a>触发器

借助外部文件触发器，可以监视远程文件夹，并在检测到更改时运行函数代码。

## <a name="trigger---example"></a>触发器 - 示例

参阅语言特定的示例：

* [C# 脚本](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-script-example"></a>触发器 - C# 脚本示例

以下示例演示 *function.json* 文件中的一个外部文件触发器绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。 该函数记录添加到受监视文件夹中每个文件的内容。

下面是 *function.json* 文件中的绑定数据：

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myFile",
            "type": "apiHubFileTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection": "<name of external file connection>"
        }
    ]
}
```

C# 脚本代码如下所示：

```cs
public static void Run(string myFile, TraceWriter log)
{
    log.Info($"C# File trigger function processed: {myFile}");
}
```

### <a name="trigger---javascript-example"></a>触发器 - JavaScript 示例

以下示例演示 *function.json* 文件中的一个外部文件触发器绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。 该函数记录添加到受监视文件夹中每个文件的内容。

下面是 *function.json* 文件中的绑定数据：

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myFile",
            "type": "apiHubFileTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection": "<name of external file connection>"
        }
    ]
}
```

JavaScript 代码如下所示：

```javascript
module.exports = function(context) {
    context.log('Node.js File trigger function processed', context.bindings.myFile);
    context.done();
};
```

## <a name="trigger---configuration"></a>触发器 - 配置

下表解释了在 *function.json* 文件中设置的绑定配置属性。

|function.json 属性 | 说明|
|---------|---------|----------------------|
|**类型** | 必须设置为 `apiHubFileTrigger`。 在 Azure 门户中创建触发器时，会自动设置此属性。|
|**direction** | 必须设置为 `in`。 在 Azure 门户中创建触发器时，会自动设置此属性。 |
|**name** | 在函数代码中表示事件项的变量的名称。 | 
|**连接**| 标识存储连接字符串的应用设置。 在 Azure 门户的集成 UI 中添加连接时，会自动创建应用设置。|
|**路径** | 要监视的文件夹和（可选）名称模式。|

### <a name="name-patterns"></a>名称模式

可以在 `path` 属性中指定文件名模式。 所引用的文件夹必须位于 SaaS 提供程序中。

示例:

```json
"path": "input/original-{name}",
```

此路径会在“input”文件夹中查找名为“original-File1.txt”的文件，函数代码中的 `name` 变量值将为 `File1.txt`。

另一个示例：

```json
"path": "input/{filename}.{fileextension}",
```

此路径还将查找名为“original-File1.txt”的文件，函数代码中的 `filename` 和 `fileextension` 变量值将为“original-File1”和“txt”。

可以使用文件扩展名的固定值来限制文件的文件类型。 例如：

```json
"path": "samples/{name}.png",
```

在本例中，仅“samples”文件夹中的“.png”文件会触发函数。

大括号在名称模式中为特殊字符。 若要指定名称中包含大括号的文件名，可增加大括号。
例如：

```json
"path": "images/{{20140101}}-{name}",
```

此路径将在 images 文件夹中查找名为 {20140101}-soundfile.mp3 的文件，函数代码中的 `name` 变量值将为 soundfile.mp3。

## <a name="trigger---usage"></a>触发器 - 用法

在 C# 函数中，通过在函数签名中使用命名参数来绑定到输入文件数据，如 `<T> <name>`。
其中 `T` 是数据要进行反序列化的目标数据类型，`paramName` 是在[触发器 JSON ](#trigger) 中指定的名称。 在 Node.js 函数中，使用 `context.bindings.<name>` 访问输入文件数据。

该文件可以反序列化为以下任何类型：

* 任何[对象](https://msdn.microsoft.com/library/system.object.aspx) - 适用于 JSON 序列化的文件数据。
  如果声明自定义输入类型（如 `FooType`），Azure Functions 会尝试将 JSON 数据反序列化到指定类型。
* 字符串 - 适用于文本文件数据。

在 C# 函数中，还可以绑定到以下任何类型，Functions 运行时会尝试使用该类型反序列化文件数据：

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`

<!--- ## Trigger - file receipts
The Azure Functions runtime makes sure that no external file trigger function gets called more than once for the same new or updated file.
It does so by maintaining *file receipts* to determine if a given file version has been processed.

File receipts are stored in a folder named *azure-webjobs-hosts* in the Azure storage account for your function app
(specified by the `AzureWebJobsStorage` app setting). A file receipt has the following information:

* The triggered function ("*&lt;function app name>*.Functions.*&lt;function name>*", for example: "functionsf74b96f7.Functions.CopyFile")
* The folder name
* The file type ("BlockFile" or "PageFile")
* The file name
* The ETag (a file version identifier, for example: "0x8D1DC6E70A277EF")

To force reprocessing of a file, delete the file receipt for that file from the *azure-webjobs-hosts* folder manually.
--->

## <a name="trigger---poison-files"></a>触发器 - 有害文件

当外部文件触发器函数失败时，默认情况下，Azure Functions 会针对给定文件重试该函数最多 5 次（包括第一次尝试）。
如果 5 次尝试全部失败，Functions 会将消息添加到名为“webjobs-apihubtrigger-poison”的存储队列。 有害文件的队列消息是包含以下属性的 JSON 对象：

* FunctionId（格式为 &lt;function app name>.Functions.&lt;function name>）
* FileType
* FolderName
* FileName
* ETag（文件版本标识符，例如：“0x8D1DC6E70A277EF”）

## <a name="input"></a>输入

Azure 外部文件输入绑定允许在函数中使用外部文件夹中的文件。

## <a name="input---example"></a>输入 - 示例

参阅语言特定的示例：

* [C# 脚本](#input---c-script-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-script-example"></a>输入 - C# 脚本示例

以下示例演示 *function.json* 文件中的外部文件输入和输出绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。 该函数将一个输入文件复制到输出文件。

下面是 *function.json* 文件中的绑定数据：

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
      "name": "myInputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "<name of external file connection>",
      "direction": "in"
    },
    {
      "name": "myOutputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "<name of external file connection>",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

C# 脚本代码如下所示：

```cs
public static void Run(string myQueueItem, string myInputFile, out string myOutputFile, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputFile = myInputFile;
}
```

### <a name="input---javascript-example"></a>输入 - JavaScript 示例

以下示例演示 *function.json* 文件中的外部文件输入和输出绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。 该函数将一个输入文件复制到输出文件。

下面是 *function.json* 文件中的绑定数据：

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
      "name": "myInputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "<name of external file connection>",
      "direction": "in"
    },
    {
      "name": "myOutputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "<name of external file connection>",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

JavaScript 代码如下所示：

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```

## <a name="input---configuration"></a>输入 - 配置

下表解释了在 *function.json* 文件中设置的绑定配置属性。

|function.json 属性 | 说明|
|---------|---------|----------------------|
|**类型** | 必须设置为 `apiHubFile`。 在 Azure 门户中创建触发器时，会自动设置此属性。|
|**direction** | 必须设置为 `in`。 在 Azure 门户中创建触发器时，会自动设置此属性。 |
|**name** | 在函数代码中表示事件项的变量的名称。 | 
|**连接**| 标识存储连接字符串的应用设置。 在 Azure 门户的集成 UI 中添加连接时，会自动创建应用设置。|
|**路径** | 必须包含文件夹名称和文件名。 例如，如果函数中有[队列触发器](functions-bindings-storage-queue.md)，可以使用 `"path": "samples-workitems/{queueTrigger}"` 指向 `samples-workitems` 文件夹中的一个文件，其名称与触发器消息中指定的文件名相匹配。   

## <a name="input---usage"></a>输入 - 用法

在 C# 函数中，通过在函数签名中使用命名参数来绑定到输入文件数据，如 `<T> <name>`。 `T` 是数据要进行反序列化的目标数据类型，`name` 是在输入绑定中指定的名称。 在 Node.js 函数中，使用 `context.bindings.<name>` 访问输入文件数据。

该文件可以反序列化为以下任何类型：

* 任何[对象](https://msdn.microsoft.com/library/system.object.aspx) - 适用于 JSON 序列化的文件数据。
  如果声明自定义输入类型（如 `InputType`），Azure Functions 会尝试将 JSON 数据反序列化到指定类型。
* 字符串 - 适用于文本文件数据。

在 C# 函数中，还可以绑定到以下任何类型，Functions 运行时会尝试使用该类型反序列化文件数据：

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`

## <a name="output"></a>输出

Azure 外部文件输出绑定使你能够在函数中将文件写入外部文件夹。

## <a name="output---example"></a>输出 - 示例

请参阅[输入绑定示例](#input---example)。

## <a name="output---configuration"></a>输出 - 配置

下表解释了在 *function.json* 文件中设置的绑定配置属性。

|function.json 属性 | 说明|
|---------|---------|----------------------|
|**类型** | 必须设置为 `apiHubFile`。 在 Azure 门户中创建触发器时，会自动设置此属性。|
|**direction** | 必须设置为 `out`。 在 Azure 门户中创建触发器时，会自动设置此属性。 |
|**name** | 在函数代码中表示事件项的变量的名称。 | 
|**连接**| 标识存储连接字符串的应用设置。 在 Azure 门户的集成 UI 中添加连接时，会自动创建应用设置。|
|**路径** | 必须包含文件夹名称和文件名。 例如，如果函数中有[队列触发器](functions-bindings-storage-queue.md)，可以使用 `"path": "samples-workitems/{queueTrigger}"` 指向 `samples-workitems` 文件夹中的一个文件，其名称与触发器消息中指定的文件名相匹配。   

## <a name="output---usage"></a>输出 - 用法

在 C# 函数中，通过在函数签名中使用名为 `out` 的参数（如 `out <T> <name>`）绑定到输出文件，其中 `T` 是要序列化数据的目标数据类型，`name` 是输出绑定中指定的名称。 在 Node.js 函数中，使用 `context.bindings.<name>` 访问输出文件。

可以使用以下任何类型写入输出文件：

* 任何[对象](https://msdn.microsoft.com/library/system.object.aspx) - 有助于 JSON 序列化。
  如果声明自定义输出类型（例如 `out OutputType paramName`），Azure Functions 将尝试将对象序列化为 JSON。 如果函数退出时输出参数为 null，Functions 运行时会创建一个文件作为 null 对象。
* 字符串 - (`out string paramName`) 适用于文本文件数据。 只有在函数退出时字符串参数为非 null，Functions 运行时才会创建一个文件。

在 C# 函数中，还可输出到以下任意类型：

* `TextWriter`
* `Stream`
* `CloudFileStream`
* `ICloudFile`
* `CloudBlockFile`
* `CloudPageFile`

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 Azure Functions 触发器和绑定](functions-triggers-bindings.md)
