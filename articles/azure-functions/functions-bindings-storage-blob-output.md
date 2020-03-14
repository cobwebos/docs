---
title: 适用于 Azure Functions 的 Azure Blob 存储输出绑定
description: 了解如何向 Azure 函数提供 Azure Blob 存储数据。
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: c6e15c9a99a78f0f3637f718b35462fe49fd5ee6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79277240"
---
# <a name="azure-blob-storage-output-binding-for-azure-functions"></a>适用于 Azure Functions 的 Azure Blob 存储输出绑定

使用输出绑定，可以在 Azure 函数中修改和删除 blob 存储数据。

有关设置和配置的详细信息，请参阅[概述](./functions-bindings-storage-blob.md)。

## <a name="example"></a>示例

# <a name="c"></a>[C#](#tab/csharp)

以下示例演示使用一个 blob 触发器和两个 blob 输出绑定的 [C# 函数](functions-dotnet-class-library.md)。 在 *sample-images* 容器中创建映像 Blob 时，会触发该函数。 该函数创建该映像 Blob 的小型和中型副本。

```csharp
using System.Collections.Generic;
using System.IO;
using Microsoft.Azure.WebJobs;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Formats;
using SixLabors.ImageSharp.PixelFormats;
using SixLabors.ImageSharp.Processing;

public class ResizeImages
{
    [FunctionName("ResizeImage")]
    public static void Run([BlobTrigger("sample-images/{name}")] Stream image,
        [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
        [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
    {
        IImageFormat format;

        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageSmall, ImageSize.Small, format);
        }

        image.Position = 0;
        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageMedium, ImageSize.Medium, format);
        }
    }

    public static void ResizeImage(Image<Rgba32> input, Stream output, ImageSize size, IImageFormat format)
    {
        var dimensions = imageDimensionsTable[size];

        input.Mutate(x => x.Resize(dimensions.Item1, dimensions.Item2));
        input.Save(output, format);
    }

    public enum ImageSize { ExtraSmall, Small, Medium }

    private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
        { ImageSize.ExtraSmall, (320, 200) },
        { ImageSize.Small,      (640, 400) },
        { ImageSize.Medium,     (800, 600) }
    };

}
```

# <a name="c-script"></a>[C#脚本](#tab/csharp-script)

<!--Same example for input and output. -->

以下示例演示 function.json 文件中的 blob 输入和输出绑定，以及使用这些绑定的 [C# 脚本 (.csx)](functions-reference-csharp.md) 代码。 此函数创建文本 blob 的副本。 该函数由包含要复制的 Blob 名称的队列消息触发。 新 Blob 名为 *{originalblobname}-Copy*。

在 *function.json* 文件中，`queueTrigger` 元数据属性用于指定 `path` 属性中的 Blob 名称：

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[配置](#configuration)部分解释了这些属性。

C# 脚本代码如下所示：

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

下面的示例展示了 function.json 文件中的 blob 输入和输出绑定，以及使用这些绑定的 [JavaScript 代码](functions-reference-node.md)。 该函数创建 Blob 的副本。 该函数由包含要复制的 Blob 名称的队列消息触发。 新 Blob 名为 *{originalblobname}-Copy*。

在 *function.json* 文件中，`queueTrigger` 元数据属性用于指定 `path` 属性中的 Blob 名称：

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[配置](#configuration)部分解释了这些属性。

JavaScript 代码如下所示：

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

<!--Same example for input and output. -->

下面的示例展示了 *function.json* 文件中的 blob 输入和输出绑定，以及使用这些绑定的 [Python 代码](functions-reference-python.md)。 该函数创建 Blob 的副本。 该函数由包含要复制的 Blob 名称的队列消息触发。 新 Blob 名为 *{originalblobname}-Copy*。

在 *function.json* 文件中，`queueTrigger` 元数据属性用于指定 `path` 属性中的 Blob 名称：

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "outputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

[配置](#configuration)部分解释了这些属性。

下面是 Python 代码：

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

# <a name="java"></a>[Java](#tab/java)

本部分包含以下示例：

* [HTTP 触发器，使用 OutputBinding](#http-trigger-using-outputbinding-java)
* [队列触发器，使用函数返回值](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>HTTP 触发器，使用 OutputBinding (Java)

 下面的示例显示了一个 Java 函数，该函数使用 `HttpTrigger` 注释来接收包含 blob 存储容器中某个文件的名称的一个参数。 然后，`BlobInput` 注释读取该文件并将其作为 `byte[]` 传递给该函数。 `BlobOutput` 注释绑定到 `OutputBinding outputItem`，然后，函数使用后者来将输入 blob 的内容写入到所配置的存储容器中。

```java
  @FunctionName("copyBlobHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage copyBlobHttp(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    @BlobOutput(
      name = "target", 
      path = "myblob/{Query.file}-CopyViaHttp")
    OutputBinding<String> outputItem,
    final ExecutionContext context) {
      // Save blob to outputItem
      outputItem.setValue(new String(content, StandardCharsets.UTF_8));

      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-using-function-return-value-java"></a>队列触发器，使用函数返回值 (Java)

 下面的示例显示了一个 Java 函数，该函数使用 `QueueTrigger` 注释来接收包含 blob 存储容器中某个文件的名称的一个消息。 然后，`BlobInput` 注释读取该文件并将其作为 `byte[]` 传递给该函数。 `BlobOutput` 注释绑定到函数返回值，然后，运行时使用后者来将输入 blob 的内容写入到所配置的存储容器中。

```java
  @FunctionName("copyBlobQueueTrigger")
  @StorageAccount("Storage_Account_Connection_String")
  @BlobOutput(
    name = "target", 
    path = "myblob/{queueTrigger}-Copy")
  public String copyBlobQueue(
    @QueueTrigger(
      name = "filename", 
      dataType = "string",
      queueName = "myqueue-items") 
    String filename,
    @BlobInput(
      name = "file", 
      path = "samples-workitems/{queueTrigger}") 
    String content,
    final ExecutionContext context) {
      context.getLogger().info("The content of \"" + filename + "\" is: " + content);
      return content;
  }
```

 在 [Java 函数运行时库](/java/api/overview/azure/functions/runtime)中，对其值将写入 Blob 存储中对象的函数参数使用 `@BlobOutput` 注释。  参数类型应为 `OutputBinding<T>`，其中 T 是任何本机 Java 类型或者是一个 POJO。

---

## <a name="attributes-and-annotations"></a>特性和批注

# <a name="c"></a>[C#](#tab/csharp)

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs)。

该特性的构造函数采用 Blob 的路径，以及一个表示读取或写入的 `FileAccess` 参数，如以下示例中所示：

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

可以设置 `Connection` 属性来指定要使用的存储帐户，如以下示例中所示：

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

# <a name="c-script"></a>[C#脚本](#tab/csharp-script)

C#脚本不支持特性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支持特性。

# <a name="python"></a>[Python](#tab/python)

Python 不支持特性。

# <a name="java"></a>[Java](#tab/java)

`@BlobOutput` 特性使你能够访问触发函数的 blob。 如果将字节数组与特性一起使用，请将 `dataType` 设置为 `binary`。 有关详细信息，请参阅[输出示例](#example)。

---

有关完整示例，请参阅[输出示例](#example)。

可以使用 `StorageAccount` 特性在类、方法或参数级别指定存储帐户。 有关详细信息，请参阅[触发器 - 特性](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations)。

## <a name="configuration"></a>配置

下表解释了在 function.json 文件和 `Blob` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|type | 不适用 | 必须设置为 `blob`。 |
|direction | 不适用 | 对于输出绑定，必须设置为 `out`。 [用法](#usage)部分中已阐述异常。 |
|name | 不适用 | 表示函数代码中的 Blob 的变量的名称。  设置为 `$return` 可引用函数返回值。|
|**路径** |**BlobPath** | Blob 容器的路径。 |
|连接 |**Connection**| 包含要用于此绑定的存储连接字符串的应用设置的名称。 如果应用设置名称以“AzureWebJobs”开始，则只能在此处指定该名称的余下部分。 例如，如果将 `connection` 设置为“MyStorage”，函数运行时将会查找名为“AzureWebJobsMyStorage”的应用设置。 如果将 `connection` 留空，函数运行时将使用名为 `AzureWebJobsStorage` 的应用设置中的默认存储连接字符串。<br><br>连接字符串必须属于某个常规用途存储帐户，而不能属于[仅限 Blob 的存储帐户](../storage/common/storage-account-overview.md#types-of-storage-accounts)。|
|不适用 | **Access** | 表示是要读取还是写入。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用情况

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-script"></a>[C#脚本](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

在 JavaScript 中，可以使用 `context.bindings.<name from function.json>` 访问 Blob 数据。

# <a name="python"></a>[Python](#tab/python)

可以将函数参数声明为以下类型以写出到 blob 存储：

* 字符串作为 `func.Out(str)`
* 流 `func.Out(func.InputStream)`

有关详细信息，请参阅[输出示例](#example)。

# <a name="java"></a>[Java](#tab/java)

`@BlobOutput` 特性使你能够访问触发函数的 blob。 如果将字节数组与特性一起使用，请将 `dataType` 设置为 `binary`。 有关详细信息，请参阅[输出示例](#example)。

---

## <a name="exceptions-and-return-codes"></a>异常和返回代码

| 绑定 |  参考 |
|---|---|
| Blob | [Blob 错误代码](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Blob、表、队列 |  [存储错误代码](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob、表、队列 |  [故障排除](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>后续步骤

- [在 blob 存储数据更改时运行函数](./functions-bindings-storage-blob-trigger.md)
- [函数运行时读取 blob 存储数据](./functions-bindings-storage-blob-input.md)
