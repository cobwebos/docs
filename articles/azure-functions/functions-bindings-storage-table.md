---
title: "Azure Functions 存储表绑定 | Microsoft Docs"
description: "了解如何在 Azure Functions 中使用 Azure 存储触发器和绑定。"
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "Azure Functions，函数，事件处理，动态计算，无服务体系结构"
ms.assetid: 65b3437e-2571-4d3f-a996-61a74b50a1c2
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/28/2016
ms.author: chrande
translationtype: Human Translation
ms.sourcegitcommit: 0d37eb09a6c8a0bb39a331e51a8993c114202b91
ms.openlocfilehash: 88858cffa5ddc6ba83152d3430f5400a1c66a26a
ms.lasthandoff: 02/17/2017


---
# <a name="azure-functions-storage-table-bindings"></a>Azure Functions 存储表绑定
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

本文介绍如何在 Azure Functions 中配置和编码 Azure 存储表触发器和绑定。 Azure Functions 支持 Azure 存储表的输入和输出绑定。

存储表绑定支持以下方案：

* **在 C# 或 Node.js 函数中读取单个行** - 设置 `partitionKey` 和 `rowKey`。 此方案中不使用 `filter` 和 `take` 属性。
* **在 C# 函数中读取多个行** - 函数运行时提供绑定到表的 `IQueryable<T>` 对象。 类型 `T` 必须派生自 `TableEntity` 或实现 `ITableEntity`。 此方案中不使用 `partitionKey`、`rowKey`、`filter` 和 `take` 属性；可以使用 `IQueryable` 对象来执行任何所需的筛选。 
* **在 Node 函数中读取多个行** - 设置 `filter` 和 `take` 属性。 不要设置 `partitionKey` 或 `rowKey`。
* **在 C# 函数中写入一个或多个行** - 函数运行时提供绑定到表的 `ICollector<T>` 或 `IAsyncCollector<T>`，其中 `T` 指定想要添加的实体的架构。 通常情况下，类型 `T` 派生自 `TableEntity` 或实现 `ITableEntity`，但它并不一定要执行这些操作。 此方案中不使用 `partitionKey`、`rowKey`、`filter` 和 `take` 属性。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="input"></a>

## <a name="storage-table-input-binding"></a>存储表输入绑定
通过 Azure 存储表输入绑定可在函数中使用存储表。 

函数的存储表输入在函数 .json 的 `bindings` 数组中使用以下 JSON 对象：

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "table",
    "direction": "in",
    "tableName": "<Name of Storage table>",
    "partitionKey": "<PartitionKey of table entity to read - see below>",
    "rowKey": "<RowKey of table entity to read - see below>",
    "take": "<Maximum number of entities to read in Node.js - optional>",
    "filter": "<OData filter expression for table input in Node.js - optional>",
    "connection": "<Name of app setting - see below>",
}
```

注意以下事项： 

* 协同使用 `partitionKey` 和 `rowKey` 读取单个实体。 这些属性是可选的。 
* `connection` 必须包含具有存储连接字符串的应用设置的名称。 在 Azure 门户中，创建存储帐户或选择现有存储帐户时，“集成”选项卡中的标准编辑器会为你配置此应用设置。 还可以[手动配置此应用设置](functions-how-to-use-azure-function-app-settings.md#application-settings)。  

<a name="inputusage"></a>

## <a name="input-usage"></a>输入使用情况
在 C# 函数中，通过在函数签名中使用命名参数来绑定到输入表实体（一个或多个），如 `<T> <name>`。
其中 `T` 是数据要进行反序列化的目标数据类型，`paramName` 是在[输入绑定](#input)中指定的名称。 在 Node.js 函数中，可以使用 `context.bindings.<name>` 访问输入表实体（一个或多个）。

可以在 Node.js 或 C# 函数中反序列化输入数据。 反序列化的对象具有 `RowKey` 和 `PartitionKey` 属性。

在 C# 函数中，还可绑定到以下任意类型，Functions 运行时将尝试使用此类型反序列化此表数据：

* 实现 `ITableEntity` 的任何类型
* `IQueryable<T>`

<a name="inputsample"></a>

## <a name="input-sample"></a>输入示例
假设具有以下 function.json，其使用队列触发器来读取单个表行。 JSON 指定 `PartitionKey` 
`RowKey`。 `"rowKey": "{queueTrigger}"` 指示行键来自队列消息字符串。

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

请参阅读取单个表实体的特定于语言的示例。

* [C#](#inputcsharp)
* [F#](#inputfsharp)
* [Node.js](#inputnodejs)

<a name="inputcsharp"></a>

### <a name="input-sample-in-c"></a>C 中的输入示例# #
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

<a name="inputfsharp"></a>

### <a name="input-sample-in-f"></a>F 中的输入示例# #
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

<a name="inputnodejs"></a>

### <a name="input-sample-in-nodejs"></a>Node.js 中的输入示例
```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

<a name="output"></a>

## <a name="storage-table-output-binding"></a>存储表输出绑定
借助 Azure 存储表输出绑定，用户可将实体写入到函数中的存储表。 

函数的存储表输出在函数 .json 的 `bindings` 数组中使用以下 JSON 对象：

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "table",
    "direction": "out",
    "tableName": "<Name of Storage table>",
    "partitionKey": "<PartitionKey of table entity to write - see below>",
    "rowKey": "<RowKey of table entity to write - see below>",
    "connection": "<Name of app setting - see below>",
}
```

注意以下事项： 

* 协同使用 `partitionKey` 和 `rowKey` 写入单个实体。 这些属性是可选的。 当在函数代码中创建实体对象时还可以指定 `PartitionKey` 和 `RowKey`。
* `connection` 必须包含具有存储连接字符串的应用设置的名称。 在 Azure 门户中，创建存储帐户或选择现有存储帐户时，“集成”选项卡中的标准编辑器会为你配置此应用设置。 还可以[手动配置此应用设置](functions-how-to-use-azure-function-app-settings.md#application-settings)。 

<a name="outputusage"></a>

## <a name="output-usage"></a>输出使用情况
在 C# 函数中，通过在函数签名中使用名为 `out` 的参数（如 `out <T> <name>`）绑定到表输出，其中 `T` 是要将数据序列化的数据类型，`paramName` 是在[输出绑定](#output)中指定的名称。 在 Node.js 函数中，使用 `context.bindings.<name>` 访问表输出。

可以反序列化 Node.js 或 C# 函数中的对象。 在 C# 函数中，还可以绑定到以下类型：

* 实现 `ITableEntity` 的任何类型
* `ICollector<T>`（若要输出多个实体。 请参阅[示例](#outcsharp)。）
* `IAsyncCollector<T>`（异步版`ICollector<T>`）
* `CloudTable`（使用 Azure 存储 SDK。 请参阅[示例](#readmulti)。）

<a name="outputsample"></a>

## <a name="output-sample"></a>输出示例
以下 function.json 和 run.csx 示例演示了如何写入多个表实体。

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

请参阅读取多个表实体的特定于语言的示例。

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>C 中的输出示例# #
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
<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>F 中的输出示例# #
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

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Node.js 中的输出示例
```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }
    
    context.done();
};
```

<a name="readmulti"></a>

## <a name="sample-read-multiple-table-entities-in-c"></a>示例：在 C 中读取多个表实体#  #
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

## <a name="next-steps"></a>后续步骤
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


