---
title: 适用于 Azure Functions 的 Azure Blob 存储触发器
description: 了解如何在 Azure Blob 存储数据更改时运行 Azure 功能。
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 0cdff3ac6eb2faed0c0b6b8796fdb3b6b0411018
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79277357"
---
# <a name="azure-blob-storage-trigger-for-azure-functions"></a>适用于 Azure Functions 的 Azure Blob 存储触发器

检测到新的或更新的 Blob 时，Blob 存储触发器会启动某个函数。 Blob 内容作为[函数的输入](./functions-bindings-storage-blob-input.md)提供。

Azure Blob 存储触发器需要常规用途的存储帐户。 若要使用仅限 blob 的帐户，或者如果你的应用程序具有特殊需求，请查看使用此触发器的替代方法。

有关设置和配置的详细信息，请参阅[概述](./functions-bindings-storage-blob.md)。

## <a name="alternatives"></a>备选项

### <a name="event-grid-trigger"></a>事件网格触发器

[事件网格触发器](functions-bindings-event-grid.md)还为[blob 事件](../storage/blobs/storage-blob-event-overview.md)提供内置支持。 以下方案请使用事件网格而不是 Blob 存储触发器：

- **仅限 blob 的存储帐户**： blob 输入和输出绑定支持[仅限 blob 的存储帐户](../storage/common/storage-account-overview.md#types-of-storage-accounts)，但不支持 blob 触发器。

- **大规模**：高缩放可以松散定义为具有超过100000个 blob 的容器，或每秒具有超过100个 blob 更新的存储帐户。

- **最小化延迟**：如果函数应用在消耗计划中，则在处理新 blob 时，如果 function app 处于空闲状态，则可能最多需要10分钟的延迟。 若要避免此延迟，可以切换到启用了 Always On 的应用服务计划。 还可以为 Blob 存储帐户使用[事件网格触发器](functions-bindings-event-grid.md)。 有关示例，请参阅[事件网格教程](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json)。

请参阅事件网格示例的 "[使用事件网格调整图像大小](../event-grid/resize-images-on-storage-blob-upload-event.md)" 教程。

### <a name="queue-storage-trigger"></a>队列存储触发器

处理 blob 的另一种方法是编写对应于正在创建或修改的 blob 的队列消息，然后使用[队列存储触发器](./functions-bindings-storage-queue.md)开始处理。

## <a name="example"></a>示例

# <a name="c"></a>[C#](#tab/csharp)

以下示例演示在 [ 容器中添加或更新 blob 时写入日志的 ](functions-dotnet-class-library.md)C# 函数`samples-workitems`。

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

blob 触发器路径 `{name}` 中的字符串 `samples-workitems/{name}` 会创建一个[绑定表达式](./functions-bindings-expressions-patterns.md)，可以在函数代码中使用它来访问触发 blob 的文件名。 有关详细信息，请参阅本文下文中的 [Blob 名称模式](#blob-name-patterns)。

有关 `BlobTrigger` 属性的详细信息，请参阅[属性和注释](#attributes-and-annotations)。

# <a name="c-script"></a>[C#脚本](#tab/csharp-script)

下面的示例演示*函数 json*文件中的 blob 触发器绑定，以及使用绑定的代码。 在 `samples-workitems`[容器](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)中添加或更新 blob 时，该函数会写入日志。

下面是 function.json 文件中的绑定数据：

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

blob 触发器路径 `{name}` 中的字符串 `samples-workitems/{name}` 会创建一个[绑定表达式](./functions-bindings-expressions-patterns.md)，可以在函数代码中使用它来访问触发 blob 的文件名。 有关详细信息，请参阅本文下文中的 [Blob 名称模式](#blob-name-patterns)。

有关 *function.json* 文件属性的详细信息，请参阅解释了这些属性的[配置](#configuration)部分。

下面是绑定到 `Stream` 的 C# 脚本代码：

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

下面是绑定到 `CloudBlockBlob` 的 C# 脚本代码：

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

以下示例显示了 *function.json* 文件中的一个 Blob 触发器绑定以及使用该绑定的 [JavaScript 代码](functions-reference-node.md)。 在 `samples-workitems` 容器中添加或更新 Blob 时，该函数会写入日志。

function.json 文件如下所示：

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

blob 触发器路径 `{name}` 中的字符串 `samples-workitems/{name}` 会创建一个[绑定表达式](./functions-bindings-expressions-patterns.md)，可以在函数代码中使用它来访问触发 blob 的文件名。 有关详细信息，请参阅本文下文中的 [Blob 名称模式](#blob-name-patterns)。

有关 *function.json* 文件属性的详细信息，请参阅解释了这些属性的[配置](#configuration)部分。

JavaScript 代码如下所示：

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

以下示例显示了 *function.json* 文件中的一个 Blob 触发器绑定以及使用该绑定的 [Python 代码](functions-reference-python.md)。 在 `samples-workitems`[容器](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)中添加或更新 blob 时，该函数会写入日志。

function.json 文件如下所示：

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,
    "bindings": [
        {
            "name": "myblob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

blob 触发器路径 `{name}` 中的字符串 `samples-workitems/{name}` 会创建一个[绑定表达式](./functions-bindings-expressions-patterns.md)，可以在函数代码中使用它来访问触发 blob 的文件名。 有关详细信息，请参阅本文下文中的 [Blob 名称模式](#blob-name-patterns)。

有关 *function.json* 文件属性的详细信息，请参阅解释了这些属性的[配置](#configuration)部分。

下面是 Python 代码：

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

# <a name="java"></a>[Java](#tab/java)

在 `myblob` 容器中添加或更新 blob 时，此函数会写入日志。

```java
@FunctionName("blobprocessor")
public void run(
  @BlobTrigger(name = "file",
               dataType = "binary",
               path = "myblob/{name}",
               connection = "MyStorageAccountAppSetting") byte[] content,
  @BindingName("name") String filename,
  final ExecutionContext context
) {
  context.getLogger().info("Name: " + filename + " Size: " + content.length + " bytes");
}
```

---

## <a name="attributes-and-annotations"></a>特性和批注

# <a name="c"></a>[C#](#tab/csharp)

对于 [C# 类库](functions-dotnet-class-library.md)，请使用以下属性来配置 blob 触发器：

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  该特性的构造函数采用一个表示要监视的容器的路径字符串，并选择性地采用某种 [Blob 名称模式](#blob-name-patterns)。 下面是一个示例：

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  可以设置 `Connection` 属性来指定要使用的存储帐户，如以下示例中所示：

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  有关完整示例，请参阅[触发器示例](#example)。

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  提供另一种方式来指定要使用的存储帐户。 构造函数采用包含存储连接字符串的应用设置的名称。 可以在参数、方法或类级别应用该特性。 以下示例演示类级别和方法级别：

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("BlobTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ....
  }
  ```

要使用的存储帐户按以下顺序确定：

* `BlobTrigger` 特性的 `Connection` 属性。
* 作为 `StorageAccount` 特性应用到同一参数的 `BlobTrigger` 特性。
* 应用到函数的 `StorageAccount` 特性。
* 应用到类的 `StorageAccount` 特性。
* 函数应用的默认存储帐户（“AzureWebJobsStorage”应用设置）。

# <a name="c-script"></a>[C#脚本](#tab/csharp-script)

C#脚本不支持特性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支持特性。

# <a name="python"></a>[Python](#tab/python)

Python 不支持特性。

# <a name="java"></a>[Java](#tab/java)

`@BlobTrigger` 特性用于授予您对触发函数的 blob 的访问权限。 有关详细信息，请参阅[触发器示例](#example)。

---

## <a name="configuration"></a>配置

下表解释了在 function.json 文件和 `BlobTrigger` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|type | 不适用 | 必须设置为 `blobTrigger`。 在 Azure 门户中创建触发器时，会自动设置此属性。|
|direction | 不适用 | 必须设置为 `in`。 在 Azure 门户中创建触发器时，会自动设置此属性。 [用法](#usage)部分中已阐述异常。 |
|name | 不适用 | 表示函数代码中的 Blob 的变量的名称。 |
|**路径** | **BlobPath** |要监视的[容器](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)。  可以是某种 [Blob 名称模式](#blob-name-patterns)。 |
|连接 | **Connection** | 包含要用于此绑定的存储连接字符串的应用设置的名称。 如果应用设置名称以“AzureWebJobs”开始，则只能在此处指定该名称的余下部分。 例如，如果将 `connection` 设置为“MyStorage”，函数运行时将会查找名为“AzureWebJobsMyStorage”的应用设置。 如果将 `connection` 留空，函数运行时将使用名为 `AzureWebJobsStorage` 的应用设置中的默认存储连接字符串。<br><br>连接字符串必须属于某个常规用途存储帐户，而不能属于[Blob 存储帐户](../storage/common/storage-account-overview.md#types-of-storage-accounts)。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用情况

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-script"></a>[C#脚本](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

使用 `context.bindings.<NAME>` 访问 blob 数据，其中 `<NAME>` 与*函数 json*中定义的值匹配。

# <a name="python"></a>[Python](#tab/python)

通过类型为[InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python)的参数访问 blob 数据。 有关详细信息，请参阅[触发器示例](#example)。

# <a name="java"></a>[Java](#tab/java)

`@BlobTrigger` 特性用于授予您对触发函数的 blob 的访问权限。 有关详细信息，请参阅[触发器示例](#example)。

---

## <a name="blob-name-patterns"></a>Blob 名称模式

可以在 `path`function.json*的* 属性中或者在 `BlobTrigger` 特性构造函数中指定 Blob 名称模式。 名称模式可以是[筛选器或绑定表达式](./functions-bindings-expressions-patterns.md)。 以下部分提供了有关示例。

### <a name="get-file-name-and-extension"></a>获取文件名和扩展名

以下示例演示如何分别绑定到 Blob 文件名和扩展名：

```json
"path": "input/{blobname}.{blobextension}",
```

如果 Blob 名为 *original-Blob1.txt*，则函数代码中 `blobname` 和 `blobextension` 变量的值为 *original-Blob1* 和 *txt*。

### <a name="filter-on-blob-name"></a>按 Blob 名称筛选

以下示例仅根据 `input` 容器中以字符串“original-”开头的 Blob 触发：

```json
"path": "input/original-{name}",
```

如果 Blob 名称为 *original-Blob1.txt*，则函数代码中 `name` 变量的值为 `Blob1`。

### <a name="filter-on-file-type"></a>按文件类型筛选

以下示例仅根据 *.png* 文件触发：

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>按文件名中的大括号筛选

若要查找文件名中的大括号，请使用两个大括号将大括号转义。 以下示例筛选名称中包含大括号的 Blob：

```json
"path": "images/{{20140101}}-{name}",
```

如果 Blob 名为 *{20140101}-soundfile.mp3*，则函数代码中的 `name` 变量值为 *soundfile.mp3*。

## <a name="metadata"></a>元数据

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-script"></a>[C#脚本](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Python 中的元数据不可用。

# <a name="java"></a>[Java](#tab/java)

元数据在 Java 中不可用。

---

## <a name="blob-receipts"></a>Blob 回执

Azure Functions 运行时确保没有为相同的新 blob 或更新 blob 多次调用 blob 触发器函数。 为了确定是否已处理给定的 blob 版本，它会维护 *blob 回执*。

Azure Functions 将 Blob 回执存储在函数应用的 Azure 存储帐户中名为 azure-webjobs-hosts 的容器中（由 `AzureWebJobsStorage` 应用设置定义）。 Blob 回执包含以下信息：

* 触发的函数（"*function app name>.Functions.&lt;function name>* *&lt;* "，例如："MyFunctionApp.Functions.CopyBlob"）
* 容器名称
* Blob 类型（"BlockBlob" 或 "PageBlob"）
* Blob 名称
* ETag（blob 版本标识符，例如："0x8D1DC6E70A277EF"）

若要强制重新处理某个 blob，可从 azure-webjobs-hosts 容器中手动删除该 blob 的 blob 回执。 尽管重新处理可能不会立即发生，但仍保证在以后的某个时间点进行重新处理。

## <a name="poison-blobs"></a>有害 blob

当给定 blob 的 blob 触发函数失败时，Azure Functions 将默认重试该函数共计 5 次。

如果 5 次尝试全部失败，Azure Functions 会将消息添加到名为 webjobs-blobtrigger-poison 的存储队列。 有害 Blob 的队列消息是包含以下属性的 JSON 对象：

* FunctionId（格式为 *function app name>.Functions.&lt;function name>* *&lt;* ）
* BlobType（"BlockBlob" 或 "PageBlob"）
* ContainerName
* BlobName
* ETag（blob 版本标识符，例如："0x8D1DC6E70A277EF"）

## <a name="concurrency-and-memory-usage"></a>并发和内存使用情况

Blob 触发器可在内部使用队列，因此并发函数调用的最大数量受 [host.json 中的队列配置](functions-host-json.md#queues)控制。 默认设置会将并发限制到 24 个调用。 此限制分别应用于使用 blob 触发器的函数。

[消耗计划](functions-scale.md#how-the-consumption-and-premium-plans-work)将一台虚拟机（VM）上的函数应用限制为 1.5 GB 内存。 内存由每个并发执行函数实例和函数运行时本身使用。 如果 blob 触发的函数将整个 blob 加载到内存中，该函数使用的仅用于 blob 的最大内存为 24 * 最大 blob 大小。 例如，包含 3 个由 blob 触发的函数的函数应用和默认设置，其每 VM 最大并发为 3*24 = 72 个函数调用。

JavaScript 和 Java 函数会将整个 blob 加载到内存中，并且如果绑定到 `string`、`Byte[]` 或 POCO，则 C# 函数也会如此。

## <a name="polling"></a>轮询

轮询在检查日志和运行定期容器扫描之间起到混合作用。 Blob 在每次以10000组进行扫描，并在间隔之间使用继续标记。

> [!WARNING]
> 此外，[将“尽力”创建存储日志](/rest/api/storageservices/About-Storage-Analytics-Logging)。 不保证捕获所有事件。 在某些情况下可能会遗漏某些日志。
> 
> 如果需要更快或更可靠的 blob 处理，在创建 blob 时，请考虑创建[队列消息](../storage/queues/storage-dotnet-how-to-use-queues.md)。 然后，使用[队列触发器](functions-bindings-storage-queue.md)而不是 Blob 触发器来处理 Blob。 另一个选项是使用事件网格；请参阅教程[使用事件网格自动调整上传图像的大小](../event-grid/resize-images-on-storage-blob-upload-event.md)。
>

## <a name="next-steps"></a>后续步骤

- [函数运行时读取 blob 存储数据](./functions-bindings-storage-blob-input.md)
- [从函数中写入 blob 存储数据](./functions-bindings-storage-blob-output.md)
