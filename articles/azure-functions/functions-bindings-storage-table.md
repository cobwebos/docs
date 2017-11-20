---
title: "Azure Functions 表存储绑定"
description: "了解如何在 Azure Functions 中使用 Azure 表存储绑定。"
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions，函数，事件处理，动态计算，无服务体系结构"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/08/2017
ms.author: chrande
ms.openlocfilehash: 2f54df931d03318a50e9397211e3c50d0898556d
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
# <a name="azure-functions-table-storage-bindings"></a>Azure Functions 表存储绑定

本文介绍如何在 Azure Functions 中使用 Azure 表存储绑定。 Azure Functions 支持 Azure 表存储使用输入和输出绑定。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="table-storage-input-binding"></a>表存储输入绑定

使用 Azure 表存储输入绑定读取 Azure 存储帐户中的表。

## <a name="input---example"></a>输入 - 示例

参阅语言特定的示例：

* [预编译 C# - 读取一个实体](#input---c-example-1)
* [预编译 C# - 读取多个实体](#input---c-example-2)
* [C# 脚本 - 读取一个实体](#input---c-script-example-1)
* [C# 脚本 - 读取多个实体](#input---c-script-example-2)
* [F#](#input---f-example-2)
* [JavaScript](#input---javascript-example)

### <a name="input---c-example-1"></a>输入 - C# 示例 1

以下示例演示读取单个表行的[预编译 C#](functions-dotnet-class-library.md) 代码。 

行键值“{queueTrigger}”指示行键来自队列消息字符串。

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition", "{queueTrigger}")] MyPoco poco, 
        TraceWriter log)
    {
        log.Info($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}";
    }
}
```

### <a name="input---c-example-2"></a>输入 - C# 示例 2

以下示例演示读取多个表行的[预编译 C#](functions-dotnet-class-library.md) 代码。 请注意，`MyPoco` 类派生自 `TableEntity`。

```csharp
public class TableStorage
{
    public class MyPoco : TableEntity
    {
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition")] IQueryable<MyPoco> pocos, 
        TraceWriter log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.Info($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}";
        }
    }
}
```

### <a name="input---c-script-example-1"></a>输入 - C# 脚本示例 1

以下示例演示 *function.json* 文件中的一个表输入绑定以及使用该绑定的 [C# 脚本](functions-reference-csharp.md)代码。 该函数使用队列触发器来读取单个表行。 

*function.json* 文件指定 `partitionKey` 和 `rowKey`。 `rowKey` 值“{queueTrigger}”指示行键来自队列消息字符串。

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
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

[配置](#input---configuration)部分解释了这些属性。

C# 脚本代码如下所示：

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

### <a name="input---c-script-example-2"></a>输入 - C# 脚本示例 2

以下示例演示 *function.json* 文件中的一个表输入绑定以及使用该绑定的 [C# 脚本](functions-reference-csharp.md)代码。 该函数读取队列消息中指定的分区键的实体。

*function.json* 文件如下所示：

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
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnectionAppSetting",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

[配置](#input---configuration)部分解释了这些属性。

C# 脚本代码添加对 Azure 存储 SDK 的引用，以便实体类型可以从 `TableEntity` 派生。

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

### <a name="input---f-example"></a>输入 - F# 示例

以下示例演示 *function.json* 文件中的一个表输入绑定以及使用该绑定的 [F# 脚本](functions-reference-fsharp.md)代码。 该函数使用队列触发器来读取单个表行。 

*function.json* 文件指定 `partitionKey` 和 `rowKey`。 `rowKey` 值“{queueTrigger}”指示行键来自队列消息字符串。

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
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

[配置](#input---configuration)部分解释了这些属性。

F# 代码如下所示：

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

### <a name="input---javascript-example"></a>输入 - JavaScript 示例

以下示例演示 *function.json* 文件中的一个表输入绑定以及使用该绑定的 [JavaScript 代码] (functions-reference-node.md)。 该函数使用队列触发器来读取单个表行。 

*function.json* 文件指定 `partitionKey` 和 `rowKey`。 `rowKey` 值“{queueTrigger}”指示行键来自队列消息字符串。

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
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

[配置](#input---configuration)部分解释了这些属性。

JavaScript 代码如下所示：

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

## <a name="input---attributes-for-precompiled-c"></a>输入 - 预编译 C# 的特性
 
对于[预编译 C#](functions-dotnet-class-library.md) 函数，请使用以下特性来配置表输入绑定：

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs)，在 NuGet 包 [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) 中定义。

  该特性的构造函数采用表名称、分区键和行键。 可对函数的 out 参数或返回值使用该特性，如以下示例中所示：

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      TraceWriter log)
  ```

  可以设置 `Connection` 属性来指定要使用的存储帐户，如以下示例中所示：

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      TraceWriter log)
  ```

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)，在 NuGet 包 [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) 中定义

  提供另一种方式来指定要使用的存储帐户。 构造函数采用包含存储连接字符串的应用设置的名称。 可以在参数、方法或类级别应用该特性。 以下示例演示类级别和方法级别：

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  ```

要使用的存储帐户按以下顺序确定：

* `Table` 特性的 `Connection` 属性。
* 作为 `Table` 特性应用到同一参数的 `StorageAccount` 特性。
* 应用到函数的 `StorageAccount` 特性。
* 应用到类的 `StorageAccount` 特性。
* 函数应用的默认存储帐户（“AzureWebJobsStorage”应用设置）。

## <a name="input---configuration"></a>输入 - 配置

下表解释了在 *function.json* 文件和 `Table` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**类型** | 不适用 | 必须设置为 `table`。 在 Azure 门户中创建绑定时，会自动设置此属性。|
|**direction** | 不适用 | 必须设置为 `in`。 在 Azure 门户中创建绑定时，会自动设置此属性。 |
|**name** | 不适用 | 表示函数代码中的表或实体的变量的名称。 | 
|**tableName** | **TableName** | 表的名称。| 
|**partitionKey** | **PartitionKey** |可选。 要读取的表实体的分区键。 有关如何使用此属性的指导，请参阅[用法](#input---usage)部分。| 
|**rowKey** |**RowKey** | 可选。 要读取的表实体的行键。 有关如何使用此属性的指导，请参阅[用法](#input---usage)部分。| 
|**take** |**Take** | 可选。 要在 JavaScript 中读取的最大实体数。 有关如何使用此属性的指导，请参阅[用法](#input---usage)部分。| 
|**filter** |**筛选器** | 可选。 JavaScript 中的表输入的 OData 筛选表达式。 有关如何使用此属性的指导，请参阅[用法](#input---usage)部分。| 
|**连接** |**Connection** | 包含要用于此绑定的存储连接字符串的应用设置的名称。 如果应用设置名称以“AzureWebJobs”开始，则只能在此处指定该名称的余下部分。 例如，如果将 `connection` 设置为“MyStorage”，函数运行时将会查找名为“AzureWebJobsMyStorage”的应用设置。 如果将 `connection` 留空，函数运行时将使用名为 `AzureWebJobsStorage` 的应用设置中的默认存储连接字符串。<br/>在本地进行开发时，应用设置将取 [local.settings.json 文件](functions-run-local.md#local-settings-file)的值。|

## <a name="input---usage"></a>输入 - 用法

表存储输入绑定支持以下方案：

* **在 C# 或 C# 脚本中读取一行**

  设置 `partitionKey` 和 `rowKey`。 使用方法参数 `T <paramName>` 访问表数据。 在 C# 脚本中，`paramName` 是在 *function.json* 的 `name` 属性中指定的值。 `T` 通常是实现 `ITableEntity` 或派生自 `TableEntity` 的类型。 此方案中不使用 `filter` 和 `take` 属性。 

* **在 C# 或 C# 脚本中读取一行或多行**

  使用方法参数 `IQueryable<T> <paramName>` 访问表数据。 在 C# 脚本中，`paramName` 是在 *function.json* 的 `name` 属性中指定的值。 `T` 必须是实现 `ITableEntity` 或派生自 `TableEntity` 的类型。 可以使用 `IQueryable` 方法执行任何所需的筛选。 此方案中不使用 `partitionKey`、`rowKey`、`filter` 和 `take` 属性。  

> [!NOTE]
> `IQueryable` 在 .NET Core 中无法正常运行，因此不能在 [Functions v2 运行时](functions-versions.md)中运行。

  一种替代方法是使用 `CloudTable paramName` 方法参数通过 Azure 存储 SDK 来读取表。

* **在 JavaScript 中读取一行或多行**

  设置 `filter` 和 `take` 属性。 不要设置 `partitionKey` 或 `rowKey`。 使用 `context.bindings.<name>` 访问输入一个或多个输入表实体。 反序列化的对象具有 `RowKey` 和 `PartitionKey` 属性。

## <a name="table-storage-output-binding"></a>表存储输出绑定

使用 Azure 表存储输出绑定读取将实体写入 Azure 存储帐户中的表。

## <a name="output---example"></a>输出 - 示例

参阅语言特定的示例：

* [预编译 C#](#output---c-example)
* [C# 脚本](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>输出 - C# 示例

以下示例演示使用 HTTP 触发器写入单个表行的[预编译 C#](functions-dotnet-class-library.md) 代码。 

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, TraceWriter log)
    {
        log.Info($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

### <a name="output---c-script-example"></a>输出 - C# 脚本示例

以下示例演示 *function.json* 文件中的一个表输出绑定以及使用该绑定的 [C# 脚本](functions-reference-csharp.md)代码。 该函数写入多个表实体。

*function.json* 文件如下所示：

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
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[配置](#output---configuration)部分解释了这些属性。

C# 脚本代码如下所示：

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

### <a name="output---f-example"></a>输出 - F# 示例

以下示例演示 *function.json* 文件中的一个表输出绑定以及使用该绑定的 [F# 脚本](functions-reference-fsharp.md)代码。 该函数写入多个表实体。

*function.json* 文件如下所示：

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
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[配置](#output---configuration)部分解释了这些属性。

F# 代码如下所示：

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

### <a name="output---javascript-example"></a>输出 - JavaScript 示例

以下示例演示 *function.json* 文件中的一个表输出绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。 该函数写入多个表实体。

*function.json* 文件如下所示：

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
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[配置](#output---configuration)部分解释了这些属性。

JavaScript 代码如下所示：

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

## <a name="output---attributes-for-precompiled-c"></a>输出 - 预编译 C# 的特性

 对于[预编译 C#](functions-dotnet-class-library.md) 函数，请使用 NuGet 包 [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) 中定义的 [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs)。

该特性的构造函数采用表名称。 可对函数的 `out` 参数或返回值使用该特性，如以下示例中所示：

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    TraceWriter log)
```

可以设置 `Connection` 属性来指定要使用的存储帐户，如以下示例中所示：

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    TraceWriter log)
```

可以使用 `StorageAccount` 特性在类、方法或参数级别指定存储帐户。 有关详细信息，请参阅[输入 - 预编译 C# 的特性](#input---attributes-for-precompiled-c)。

## <a name="output---configuration"></a>输出 - 配置

下表解释了在 *function.json* 文件和 `Table` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**类型** | 不适用 | 必须设置为 `table`。 在 Azure 门户中创建绑定时，会自动设置此属性。|
|**direction** | 不适用 | 必须设置为 `out`。 在 Azure 门户中创建绑定时，会自动设置此属性。 |
|**name** | 不适用 | 在函数代码中使用的、表示表或实体的变量名称。 设置为 `$return` 可引用函数返回值。| 
|**tableName** |**TableName** | 表的名称。| 
|**partitionKey** |**PartitionKey** | 要写入的表实体的分区键。 有关如何使用此属性的指导，请参阅[用法部分](#output---usage)。| 
|**rowKey** |**RowKey** | 要写入的表实体的行键。 有关如何使用此属性的指导，请参阅[用法部分](#output---usage)。| 
|**连接** |**Connection** | 包含要用于此绑定的存储连接字符串的应用设置的名称。 如果应用设置名称以“AzureWebJobs”开始，则只能在此处指定该名称的余下部分。 例如，如果将 `connection` 设置为“MyStorage”，函数运行时将会查找名为“AzureWebJobsMyStorage”的应用设置。 如果将 `connection` 留空，函数运行时将使用名为 `AzureWebJobsStorage` 的应用设置中的默认存储连接字符串。<br/>在本地进行开发时，应用设置将取 [local.settings.json 文件](functions-run-local.md#local-settings-file)的值。|

## <a name="output---usage"></a>输出 - 用法

表存储输出绑定支持以下方案：

* **以任何语言编写一行**

  在 C# 和 C# 脚本中，可以使用 `out T paramName` 等方法参数或函数返回值访问输出表实体。 在 C# 脚本中，`paramName` 是在 *function.json* 的 `name` 属性中指定的值。 如果 *function.json* 文件或 `Table` 特性提供了分区键和行键，则 `T` 可以是任何可序列化类型。 否则，`T` 必须是包含 `PartitionKey` 和 `RowKey` 属性的类型。 在此方案中，`T` 通常实现 `ITableEntity` 或派生自 `TableEntity`，但不一定非要这样。

* **在 C# 或 C# 脚本中写入一行或多行**

  在 C# 和 C# 脚本中，可以使用方法参数 `ICollector<T> paramName` 或 `ICollectorAsync<T> paramName` 访问输出表实体。 在 C# 脚本中，`paramName` 是在 *function.json* 的 `name` 属性中指定的值。 `T` 指定要添加的实体的架构。 通常，`T` 派生自 `TableEntity` 或实现 `ITableEntity`，但不一定非要这样。 此方案不使用 *function.json* 中的分区键和行键值，也不使用 `Table` 特性构造函数。

  一种替代方法是使用 `CloudTable paramName` 方法参数通过 Azure 存储 SDK 来写入表。

* **在 JavaScript 中写入一行或多行**

  在 JavaScript 函数中，可以使用 `context.bindings.<name>` 访问表输出。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 Azure Functions 触发器和绑定](functions-triggers-bindings.md)
