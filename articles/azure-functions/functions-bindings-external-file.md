---
title: "Azure Functions 外部文件绑定（预览版）| Microsoft Docs"
description: "在 Azure Functions 中使用外部文件绑定"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: erikre
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: alkarche
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 1ab9b7e306fda89235f4e9388fdbae4ea54307df
ms.lasthandoff: 04/15/2017


---
# <a name="azure-functions-external-file-bindings-preview"></a>Azure Functions 外部文件绑定（预览版）
本文介绍如何在 Azure Functions 中配置和使用外部文件绑定。 Azure Functions 支持外部文件的触发器、输入和输出绑定。

外部文件绑定使 Functions 可以访问 Azure 之外承载的文件。 此绑定会创建新的 API 连接，或使用 Function App 的资源组中的现有 API 连接。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="supported-file-connections"></a>支持的文件连接

|连接器|触发器|输入|输出|
|:-----|:---:|:---:|:---:|
|[Box](https://www.box.com)|x|x|x
|[Dropbox](https://www.dropbox.com)|x|x|x
|[文件系统](https://docs.microsoft.com/azure/logic-apps/logic-apps-using-file-connector)|x|x|x
|[FTP](https://docs.microsoft.com/azure/app-service-web/app-service-deploy-ftp)|x|x|x
|[OneDrive](https://onedrive.live.com)|x|x|x
|[OneDrive for Business](https://onedrive.live.com/about/business/)|x|x|x
|[SFTP](https://docs.microsoft.com/azure/connectors/connectors-create-api-sftp)|x|x|x
|[Azure Blob 存储](https://azure.microsoft.com/services/storage/blobs/)||x|x|
|[Google Drive](https://www.google.com/drive/)||x|x|

> [!NOTE]
> 还可以在 [Azure 逻辑应用](https://docs.microsoft.com/azure/connectors/apis-list)中使用外部文件连接

## <a name="external-file-trigger-binding"></a>外部文件触发器绑定

借助 Azure 外部文件触发器，可以监视远程文件夹，并在检测到更改时运行函数代码。

外部文件触发器在 function.json 的 `bindings` 数组中使用以下 JSON 对象

```json
{
  "type": "apiHubFileTrigger",
  "name": "<Name of input parameter in function signature>",
  "direction": "in",
  "path": "<folder to monitor, and optionally a name pattern - see below>",
  "connection": "<name of external file connection - see above>"
}
```
<!---
See one of the following subheadings for more information:

* [Name patterns](#pattern)
* [File receipts](#receipts)
* [Handling poison files](#poison)
--->

<a name="pattern"></a>

### <a name="name-patterns"></a>名称模式
可以在 `path` 属性中指定文件名模式。 例如：

```json
"path": "input/original-{name}",
```

此路径将在“input”文件夹中查找名为“original-File1.txt”的文件，函数代码中的 `name` 变量值将为 `File1`。

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

此路径将在“images”文件夹中查找名为“{20140101}-soundfile.mp3”的文件，函数代码中的 `name` 变量值将为“soundfile.mp3”。

<a name="receipts"></a>

<!--- ### File receipts
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
<a name="poison"></a>

### <a name="handling-poison-files"></a>处理有害文件
当外部文件触发器函数失败时，默认情况下，Azure Functions 会针对给定文件重试该函数最多 5 次（包括第一次尝试）。
如果 5 次尝试全部失败，Functions 会将消息添加到名为“webjobs-apihubtrigger-poison”的存储队列。 有害文件的队列消息是包含以下属性的 JSON 对象：

* FunctionId（格式为 &lt;function app name>.Functions.&lt;function name>）
* FileType
* FolderName
* FileName
* ETag（文件版本标识符，例如：“0x8D1DC6E70A277EF”）


<a name="triggerusage"></a>

## <a name="trigger-usage"></a>触发器使用情况
在 C# 函数中，通过在函数签名中使用命名参数来绑定到输入文件数据，如 `<T> <name>`。
其中 `T` 是数据要进行反序列化的目标数据类型，`paramName` 是在[触发器 JSON ](#trigger) 中指定的名称。 在 Node.js 函数中，使用 `context.bindings.<name>` 访问输入文件数据。

该文件可以反序列化为以下任何类型：

* 任何[对象](https://msdn.microsoft.com/library/system.object.aspx) - 适用于 JSON 序列化的文件数据。
  如果声明自定义输入类型（如 `FooType`），Azure Functions 会尝试将 JSON 数据反序列化为指定类型。
* 字符串 - 适用于文本文件数据。

在 C# 函数中，还可以绑定到以下任何类型，Functions 运行时会尝试使用该类型反序列化文件数据：

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
假设具有以下 function.json，定义了一个外部文件触发器：

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

请参阅特定于语言的示例，该示例记录添加到受监视文件夹中每个文件的内容。

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-usage-in-c"></a>C 中触发器的使用情况# #

```cs
public static void Run(string myFile, TraceWriter log)
{
    log.Info($"C# File trigger function processed: {myFile}");
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
    context.log('Node.js File trigger function processed', context.bindings.myFile);
    context.done();
};
```

<a name="input"></a>

## <a name="external-file-input-binding"></a>外部文件输入绑定
Azure 外部文件输入绑定使你能够在函数中使用外部文件夹中的文件。

函数的外部文件输入在 function.json 的 `bindings` 数组中使用以下 JSON 对象：

```json
{
  "name": "<Name of input parameter in function signature>",
  "type": "apiHubFile",
  "direction": "in",
  "path": "<Path of input file - see below>",
  "connection": "<name of external file connection>"
},
```

注意以下事项：

* `path` 必须包含文件夹名称和文件名。 例如，如果函数中有[队列触发器](functions-bindings-storage-queue.md)，可以使用 `"path": "samples-workitems/{queueTrigger}"` 指向 `samples-workitems` 文件夹中的一个文件，其名称与触发器消息中指定的文件名相匹配。   

<a name="inputusage"></a>

## <a name="input-usage"></a>输入使用情况
在 C# 函数中，通过在函数签名中使用命名参数来绑定到输入文件数据，如 `<T> <name>`。
其中 `T` 是数据要进行反序列化的目标数据类型，`paramName` 是在[输入绑定](#input)中指定的名称。 在 Node.js 函数中，使用 `context.bindings.<name>` 访问输入文件数据。

该文件可以反序列化为以下任何类型：

* 任何[对象](https://msdn.microsoft.com/library/system.object.aspx) - 适用于 JSON 序列化的文件数据。
  如果声明自定义输入类型（如 `InputType`），Azure Functions 会尝试将 JSON 数据反序列化为指定类型。
* 字符串 - 适用于文本文件数据。

在 C# 函数中，还可以绑定到以下任何类型，Functions 运行时会尝试使用该类型反序列化文件数据：

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`

<a name="inputsample"></a>

## <a name="input-sample"></a>输入示例
假设具有以下 function.json，定义了一个[存储队列触发器](functions-bindings-storage-queue.md)、一个外部文件输入和一个外部文件输出：

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

请参阅特定于语言的示例，该示例将输入文件复制到输出文件。

* [C#](#incsharp)
* [Node.js](#innodejs)

<a name="incsharp"></a>

### <a name="input-usage-in-c"></a>C 中输入使用情况# #

```cs
public static void Run(string myQueueItem, string myInputFile, out string myOutputFile, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputFile = myInputFile;
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
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```

<a name="output"></a>

## <a name="external-file-output-binding"></a>外部文件输出绑定
Azure 外部文件输出绑定使你能够在函数中将文件写入外部文件夹。

函数的外部文件输出在 function.json 的 `bindings` 数组中使用以下 JSON 对象：

```json
{
  "name": "<Name of output parameter in function signature>",
  "type": "apiHubFile",
  "direction": "out",
  "path": "<Path of input file - see below>",
  "connection": "<name of external file connection>"
}
```

注意以下事项：

* `path` 必须包含要写入的文件夹名称和文件名。 例如，如果函数中有[队列触发器](functions-bindings-storage-queue.md)，可以使用 `"path": "samples-workitems/{queueTrigger}"` 指向 `samples-workitems` 文件夹中的一个文件，其名称与触发器消息中指定的文件名相匹配。   

<a name="outputusage"></a>

## <a name="output-usage"></a>输出使用情况
在 C# 函数中，通过在函数签名中使用名为 `out` 的参数（如 `out <T> <name>`）绑定到输出文件，其中 `T` 是要序列化数据的目标数据类型，`paramName` 是[输出绑定](#output)中指定的名称。 在 Node.js 函数中，使用 `context.bindings.<name>` 访问输出文件。

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

<a name="outputsample"></a>

## <a name="output-sample"></a>输出示例
请参阅[输入示例](#inputsample)。

## <a name="next-steps"></a>后续步骤
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

