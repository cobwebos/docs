---
title: "用于 Azure 存储的 Azure Functions 触发器和绑定 | Microsoft Docs"
description: "了解如何在 Azure Functions 中使用 Azure 存储触发器和绑定。"
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "Azure Functions，函数，事件处理，动态计算，无服务体系结构"
ms.assetid: 263b9c47-88ed-4eb2-9ea0-e26613becc62
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/22/2016
ms.author: chrande
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 62516a7ab50724e095d1512239877390e9cf1951


---
# <a name="azure-functions-triggers-and-bindings-for-azure-storage"></a>适用于 Azure 存储的 Azure Functions 触发器和绑定
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

本文介绍如何在 Azure Functions 中配置和编码 Azure 存储触发器和绑定。 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="a-idstoragequeuetriggera-azure-storage-queue-trigger"></a><a id="storagequeuetrigger"></a> Azure 存储队列触发器
#### <a name="functionjson-for-storage-queue-trigger"></a>存储队列触发器的 function.json
function.json 文件指定以下属性。

* `name`：队列或队列消息的函数代码中使用的变量名称。 
* `queueName`：要轮询的队列的名称。 有关队列命名规则的信息，请参阅[命名队列和元数据](https://msdn.microsoft.com/library/dd179349.aspx)。
* `connection`：包含存储连接字符串的应用设置的名称。 如果将 `connection` 留空，则触发器将使用由 AzureWebJobsStorage 应用设置指定的 Function App 的默认存储连接字符串。
* `type`：必须设置为 queueTrigger。
* `direction`：必须设置为 in。 

存储队列触发器的示例 function.json：

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"",
            "type": "queueTrigger",
            "direction": "in"
        }
    ]
}
```

#### <a name="queue-trigger-supported-types"></a>队列触发器支持的类型
队列消息可以反序列化为以下任何类型：

* （JSON 中的）对象
* String
* Byte Array 
* `CloudQueueMessage` (C#) 

#### <a name="queue-trigger-metadata"></a>队列触发器元数据
可以使用以下变量名称在函数中获取队列元数据：

* expirationTime
* insertionTime
* nextVisibleTime
* id
* popReceipt
* dequeueCount
* queueTrigger（另一种以字符串形式检索队列消息文本的方法）

此 C# 代码示例检索和记录队列元数据：

```csharp
public static void Run(string myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

#### <a name="handling-poison-queue-messages"></a>处理有害队列消息
其内容导致函数失败的消息称为有害消息。 当函数失败时，将不删除并最终再次选择队列消息，从而导致周期重复。 在达到限制的迭代次数后，SDK 可自动中断周期，你也可以手动中断。

SDK 在处理一个队列消息时最多会调用某个函数 5 次。 如果第五次尝试失败，消息将移到有害队列。

有害队列的名称为 {originalqueuename}-poison。 你可以编写一个函数来处理有害队列中的消息，并记录这些消息，或者发送需要注意的通知。 

如果想要手动处理有害消息，可以通过检查 `dequeueCount` 获取信息被处理的次数。

## <a name="a-idstoragequeueoutputa-azure-storage-queue-output-binding"></a><a id="storagequeueoutput"></a> Azure 存储队列输出绑定
#### <a name="functionjson-for-storage-queue-output-binding"></a>存储队列输出绑定的 function.json
function.json 文件指定以下属性。

* `name`：队列或队列消息的函数代码中使用的变量名称。 
* `queueName`：队列的名称。 有关队列命名规则的信息，请参阅[命名队列和元数据](https://msdn.microsoft.com/library/dd179349.aspx)。
* `connection`：包含存储连接字符串的应用设置的名称。 如果将 `connection` 留空，则触发器将使用由 AzureWebJobsStorage 应用设置指定的 Function App 的默认存储连接字符串。
* `type`：必须设置为队列。
* `direction`：必须设置为 out。 

使用队列触发器并写入队列消息的存储队列输出绑定的示例 function.json：

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
      "name": "myQueue",
      "queueName": "samples-workitems-out",
      "connection": "MyStorageConnection",
      "type": "queue",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### <a name="queue-output-binding-supported-types"></a>队列输出绑定支持的类型
`queue` 绑定可以将以下类型序列化为队列消息：

* 对象（C# 中的 `out T`，如果函数结束时参数为 null，则创建一个带有 null 对象的消息）
* 字符串（C# 中的 `out string`，如果函数结束时参数值非 null，则创建队列消息）
* Byte Array（C# 中的 `out byte[]`，用法类似于字符串） 
* `out CloudQueueMessage`（C#，用法类似于字符串） 

在 C# 中还可以绑定到 `ICollector<T>` 或 `IAsyncCollector<T>`，其中 `T` 是受支持的类型之一。

#### <a name="queue-output-binding-code-examples"></a>队列输出绑定代码示例
此 C# 代码示例为每个输入队列消息写入单个输出队列消息。

```csharp
public static void Run(string myQueueItem, out string myOutputQueueItem, TraceWriter log)
{
    myOutputQueueItem = myQueueItem + "(next step)";
}
```

此 C# 代码示例通过使用 `ICollector<T>`（在异步函数中使用`IAsyncCollector<T>`）写入多个消息：

```csharp
public static void Run(string myQueueItem, ICollector<string> myQueue, TraceWriter log)
{
    myQueue.Add(myQueueItem + "(step 1)");
    myQueue.Add(myQueueItem + "(step 2)");
}
```

## <a name="a-idstorageblobtriggera-azure-storage-blob-trigger"></a><a id="storageblobtrigger"></a> Azure 存储 blob 触发器
#### <a name="functionjson-for-storage-blob-trigger"></a>存储 blob 触发器的 function.json
function.json 文件指定以下属性。

* `name`：在 blob 的函数代码中使用的变量名。 
* `path`：指定要监视的容器的路径，以及可选的 blob 名称模式。
* `connection`：包含存储连接字符串的应用设置的名称。 如果将 `connection` 留空，则触发器将使用由 AzureWebJobsStorage 应用设置指定的 Function App 的默认存储连接字符串。
* `type`：必须设置为 blobTrigger。
* `direction`：必须设置为 in。

监视添加到 samples-workitems 容器中的 blob 的存储 blob 触发器的示例 function.json：

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

#### <a name="blob-trigger-supported-types"></a>Blob 触发器支持的类型
Blob 可以反序列化为 Node 或 C# 函数中的以下任何类型：

* （JSON 中的）对象
* String

在 C# 函数中，还可以绑定到任何以下类型：

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* [ICloudBlobStreamBinder](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md#icbsb) 反序列化的其他类型 

#### <a name="blob-trigger-c-code-example"></a>Blob 触发器 C# 代码示例
此 C# 代码示例将记录添加到受监视的容器中的每个 blob 的内容。

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

#### <a name="blob-trigger-name-patterns"></a>Blob 触发器名称模式
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

可以通过为文件扩展指定带有固定值的模式来限制触发该函数的 blob 类型。 如果将 `path` 设置为 samples / {name} .png ，示例容器中只有 .png  blob 将触发该函数。

如果你需要指定的名称中包含大括号的 Blob 名称的名称模式，增加大括号。 例如，如果想要在映像容器中查找其名称类似以下内容的 blob：

        {20140101}-soundfile.mp3

为 `path` 属性使用以下内容：

        images/{{20140101}}-{name}

在示例中，`name` 变量值将是 soundfile.mp3。 

#### <a name="blob-receipts"></a>Blob 回执
Azure Functions 运行时确保没有为相同的新 blob 或更新 blob 多次调用 blob 触发器函数。 为此，它会维护 blob 回执，以确定是否已处理给定的 blob 版本。

Blob 回执存储在 AzureWebJobsStorage 连接字符串指定的 Azure 存储帐户中名为 *azure-webjobs-hosts* 的容器中。 Blob 回执包含以下信息：

* 为 blob 调用的函数（“{function app name}.Functions.{Function name}”，例如 "functionsf74b96f7.Functions.CopyBlob"）
* 容器名称
* Blob 类型（"BlockBlob" 或 "PageBlob"）
* Blob 名称
* ETag（blob 版本标识符，例如："0x8D1DC6E70A277EF"）

如果想要强制重新处理某个 blob，则可以从 azure-webjobs-hosts 容器中手动删除该 blob 的 blob 回执。

#### <a name="handling-poison-blobs"></a>处理有害 blob
当 blob 触发器函数失败时，如果失败是暂时性错误导致的，则 SDK 会再次调用该函数。 如果失败是由 Blob 的内容导致的，则该函数每次尝试处理 Blob 时都会失败。 默认情况下，对于给定的 Blob，SDK 调用一个函数最多 5 次。 如果第五次尝试失败，SDK 会将消息添加到名为 webjobs-blobtrigger-poison 的队列中。

有害 Blob 的队列消息是包含以下属性的 JSON 对象：

* FunctionId（格式为 {function app name}.Functions.{function name}）
* BlobType（"BlockBlob" 或 "PageBlob"）
* ContainerName
* BlobName
* ETag（blob 版本标识符，例如："0x8D1DC6E70A277EF"）

#### <a name="blob-polling-for-large-containers"></a>大容器的 blob 轮询
如果触发器正在监视的 blob 容器包含超过 10,000 个 blob，则 Functions 运行时将扫描日志文件以查看新的或更改的 blob。 此过程不是实时的；创建 blob 之后数分钟或更长时间内可能仍不会触发函数。 此外，[存储日志是基于“最大努力”创建的](https://msdn.microsoft.com/library/azure/hh343262.aspx)；不能保证能捕获所有的事件。 在某些情况下可能遗漏日志。 如果应用程序不接受大容器的 blob 触发器的速度和可靠性限制，建议在创建 blob 时创建队列消息，并使用队列触发器而不是 blob 触发器来处理 blob。

## <a name="a-idstorageblobbindingsa-azure-storage-blob-input-and-output-bindings"></a><a id="storageblobbindings"></a> Azure 存储 blob 输入和输出绑定
#### <a name="functionjson-for-a-storage-blob-input-or-output-binding"></a>存储 blob 输入或输出绑定的 function.json
function.json 文件指定以下属性。

* `name`：在 blob 的函数代码中使用的变量名。 
* `path`：指定要从中读取或写入 blob 的容器的路径，以及可选的 blob 名称模式。
* `connection`：包含存储连接字符串的应用设置的名称。 如果将 `connection` 留空，则绑定将使用由 AzureWebJobsStorage 应用设置指定的 Function App 的默认存储连接字符串。
* `type`：必须设置为 blob。
* `direction`:设置为 in 或 out。 

存储 blob 输入或输出绑定的示例 function.json，使用队列触发器复制 blob：

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

#### <a name="blob-input-and-output-supported-types"></a>Blob 输入和输出支持的类型
`blob` 绑定可以序列化或反序列化 Node.js 或 C# 函数中的以下类型：

* 对象（输出 blob 的 C# 中为 `out T`：如果函数结束时参数为 null，则创建 blob 为 null 对象）
* 字符串（输出 blob 的 C# 中为 `out string`：仅当函数返回时字符串参数非 null 的情况下，才创建 blob）

在 C# 函数中，还可以绑定到以下类型：

* `TextReader` （仅输入）
* `TextWriter` （仅输出）
* `Stream`
* `CloudBlobStream` （仅输出）
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

#### <a name="blob-output-c-code-example"></a>Blob 输出 C# 代码示例
此 C# 代码示例复制在队列消息中接收到其名称的 blob。

```csharp
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

## <a name="a-idstoragetablesbindingsa-azure-storage-tables-input-and-output-bindings"></a><a id="storagetablesbindings"></a> Azure 存储表输入和输出绑定
#### <a name="functionjson-for-storage-tables"></a>存储表的 function.json
function.json 指定以下属性。

* `name`：在表绑定的函数代码中使用的变量名。 
* `tableName`：表的名称。
* `partitionKey` 和 `rowKey`：一起用于读取 C# 或 Node 函数中的单个实体，或者在 Node 函数中写入单个实体。
* `take`：在 Node 函数中为表输入读取的最大行数。
* `filter`：Node 函数中表输入的 OData 筛选表达式。
* `connection`：包含存储连接字符串的应用设置的名称。 如果将 `connection` 留空，则绑定将使用由 AzureWebJobsStorage 应用设置指定的 Function App 的默认存储连接字符串。
* `type`：必须设置为表。
* `direction`:设置为 in 或 out。 

下面的示例 function.json 使用队列触发器来读取单个表行。 JSON 提供硬编码的分区键值，并指定行键来自于队列消息。

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
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### <a name="storage-tables-input-and-output-supported-types"></a>存储表输入和输出支持的类型
`table` 绑定可以序列化或反序列化 Node.js 或 C# 函数中的对象。 这些对象将具有 RowKey 和 PartitionKey 属性。 

在 C# 函数中，还可以绑定到以下类型：

* `T` 在其中实现 `ITableEntity` 的 `T`
* `IQueryable<T>` （仅输入）
* `ICollector<T>` （仅输出）
* `IAsyncCollector<T>` （仅输出）

#### <a name="storage-tables-binding-scenarios"></a>存储表绑定方案
表绑定支持以下方案：

* 在 C# 或 Node 函数中读取单个行。
  
    设置 `partitionKey` 和 `rowKey`。 此方案中不使用 `filter` 和 `take` 属性。
* 在 C# 函数中读取多个行。
  
    Functions 运行时提供绑定到表的 `IQueryable<T>` 对象。 类型 `T` 必须派生自 `TableEntity` 或实现 `ITableEntity`。 此方案中不使用 `partitionKey`、`rowKey`、`filter` 和 `take` 属性；可以使用 `IQueryable` 对象来执行任何所需的筛选。 
* 在 Node 函数中读取多个行。
  
    设置 `filter` 和 `take` 属性。 不要设置 `partitionKey` 或 `rowKey`。
* 在 C# 函数中写入一个或多个行。
  
    Functions 运行时提供绑定到表的 `ICollector<T>` 或 `IAsyncCollector<T>`，其中 `T` 指定要添加的实体的架构。 通常情况下，类型 `T` 派生自 `TableEntity` 或实现 `ITableEntity`，但它并不一定要执行这些操作。 此方案中不使用 `partitionKey`、`rowKey`、`filter` 和 `take` 属性。

#### <a name="storage-tables-example-read-a-single-table-entity-in-c-or-node"></a>存储表示例：在 C# 或 Node 中读取单个表实体
以下 C# 代码示例适用于之前显示的 function.json 文件，用以读取单个表实体。 队列消息具有行键值，且将表实体读取为 run.csx 文件中定义的类型。 该类型包括 `PartitionKey` 和 `RowKey` 属性，并且不是派生自 `TableEntity`。 

```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

以下 F# 代码示例也适用于前面的 function.json 文件，用以读取单个表实体。

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.Info(sprintf "Name in Person entity: %s" personEntity.Name)
```

以下 Node 代码示例也适用于前面的 function.json 文件，用以读取单个表实体。

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

#### <a name="storage-tables-example-read-multiple-table-entities-in-c"></a>存储表示例：在 C 中读取多个表实体
以下 function.json 和 C# 代码示例读取在队列消息中指定的分区键的实体。

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
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnection",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

C# 代码添加对 Azure 存储 SDK 的引用，以便实体类型可以从 `TableEntity` 派生。

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
``` 

#### <a name="storage-tables-example-create-table-entities-in-c"></a>存储表示例：在 C 中创建表实体
以下 function.json 和 run.csx 示例显示如何在 C# 中写入表实体。

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

#### <a name="storage-tables-example-create-table-entities-in-f"></a>存储表示例：在 F 中创建表实体
以下 function.json 和 run.csx 示例显示如何在 F# 中写入表实体。

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: TraceWriter) =
    for i = 1 to 10 do
        log.Info(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

#### <a name="storage-tables-example-create-a-table-entity-in-node"></a>存储表示例：在 Node 中创建表实体
以下 function.json 和 run.csx 示例显示如何在 Node 中写入表实体。

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
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "name": "personEntity",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": true
}
```

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.bindings.personEntity = {"Name": "Name" + myQueueItem }
    context.done();
};
```

## <a name="next-steps"></a>后续步骤
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]




<!--HONumber=Nov16_HO3-->


