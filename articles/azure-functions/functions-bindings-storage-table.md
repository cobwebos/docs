---
title: Azure Functions 的 Azure 表存储绑定
description: 了解如何在 Azure Functions 中使用 Azure 表存储绑定。
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.openlocfilehash: edeafb5730f06dac22fd9919ca42ea388d5fd0f6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79277175"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Azure Functions 的 Azure 表存储绑定

本文介绍如何在 Azure Functions 中使用 Azure 表存储绑定。 Azure Functions 支持 Azure 表存储使用输入和输出绑定。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>包 - Functions 1.x

[Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 包 2.x 版中提供了表存储绑定。 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) GitHub 存储库中提供了此包的源代码。

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>包-函数2.x 和更高版本

[Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet 包 3.x 版中提供了表存储绑定。 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) GitHub 存储库中提供了此包的源代码。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>输入

使用 Azure 表存储输入绑定读取 Azure 存储帐户中的表。

# <a name="c"></a>[C#](#tab/csharp)

### <a name="one-entity"></a>一个实体

以下示例演示读取单个表行的 [C# 函数](functions-dotnet-class-library.md)。 对于表中插入的每个记录，将触发函数。

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
        ILogger log)
    {
        log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
    }
}
```

### <a name="iqueryable"></a>IQueryable

下面的示例演示一个[ C#函数](functions-dotnet-class-library.md)，该函数读取 `MyPoco` 类派生自 `TableEntity`的多个表行。

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
        ILogger log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
        }
    }
}
```

### <a name="cloudtable"></a>CloudTable

`IQueryable`Functions v2 运行时[不支持 ](functions-versions.md)。 一种替代方法是使用 `CloudTable` 方法参数通过 Azure 存储 SDK 来读取表。 下面是查询 Azure Functions 日志表的函数示例：

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;

namespace FunctionAppCloudTable2
{
    public class LogEntity : TableEntity
    {
        public string OriginalName { get; set; }
    }
    public static class CloudTableDemo
    {
        [FunctionName("CloudTableDemo")]
        public static async Task Run(
            [TimerTrigger("0 */1 * * * *")] TimerInfo myTimer, 
            [Table("AzureWebJobsHostLogscommon")] CloudTable cloudTable,
            ILogger log)
        {
            log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

            TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
                TableQuery.CombineFilters(
                    TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
                        "FD2"),
                    TableOperators.And,
                    TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
                        "t")));

            // Execute the query and loop through the results
            foreach (LogEntity entity in 
                await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
            {
                log.LogInformation(
                    $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
            }
        }
    }
}
```

有关如何使用 CloudTable 的详细信息，请参阅 [Azure 表存储入门](../cosmos-db/table-storage-how-to-use-dotnet.md)。

如果尝试绑定到 `CloudTable` 并收到错误消息，请确保已引用[正确的存储 SDK 版本](#azure-storage-sdk-version-in-functions-1x)。

# <a name="c-script"></a>[C#脚本](#tab/csharp-script)

### <a name="one-entity"></a>一个实体

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
public static void Run(string myQueueItem, Person personEntity, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    log.LogInformation($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

### <a name="iqueryable"></a>IQueryable

以下示例演示 *function.json* 文件中的一个表输入绑定以及使用该绑定的 [C# 脚本](functions-reference-csharp.md)代码。 该函数读取队列消息中指定的分区键的实体。

function.json 文件如下所示：

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
using Microsoft.Extensions.Logging;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.LogInformation($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

### <a name="cloudtable"></a>CloudTable

在[版本2.x 和更高版本](functions-versions.md)的函数运行时中不支持 `IQueryable`。 一种替代方法是使用 `CloudTable` 方法参数通过 Azure 存储 SDK 来读取表。 下面是查询 Azure Functions 日志表的函数示例：

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */1 * * * *"
    },
    {
      "name": "cloudTable",
      "type": "table",
      "connection": "AzureWebJobsStorage",
      "tableName": "AzureWebJobsHostLogscommon",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, CloudTable cloudTable, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

    TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
            "FD2"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
            "a")));

    // Execute the query and loop through the results
    foreach (LogEntity entity in 
    await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
    {
        log.LogInformation(
            $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
    }
}

public class LogEntity : TableEntity
{
    public string OriginalName { get; set; }
}
```

有关如何使用 CloudTable 的详细信息，请参阅 [Azure 表存储入门](../cosmos-db/table-storage-how-to-use-dotnet.md)。

如果尝试绑定到 `CloudTable` 并收到错误消息，请确保已引用[正确的存储 SDK 版本](#azure-storage-sdk-version-in-functions-1x)。


# <a name="javascript"></a>[JavaScript](#tab/javascript)

以下示例演示了 function.json 文件中的表输入绑定以及使用该绑定的 [JavaScript 代码](functions-reference-node.md)。 该函数使用队列触发器来读取单个表行。 

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

# <a name="python"></a>[Python](#tab/python)

单个表行 

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "messageJSON",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "rowKey": "{id}",
      "connection": "AzureWebJobsStorage",
      "direction": "in"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ],
      "route": "messages/{id}"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ],
  "disabled": false
}
```

```python
import json

import azure.functions as func

def main(req: func.HttpRequest, messageJSON) -> func.HttpResponse:

    message = json.loads(messageJSON)
    return func.HttpResponse(f"Table row: {messageJSON}")
```

# <a name="java"></a>[Java](#tab/java)

下面的示例显示了一个 HTTP 触发函数，该函数返回位于表存储中指定分区内的 person 对象的列表。 在此示例中，分区键从 http 路由提取，tableName 和连接来自函数设置。 

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() { return this.PartitionKey; }
    public void setPartitionKey(String key) { this.PartitionKey = key; }
    public String getRowKey() { return this.RowKey; }
    public void setRowKey(String key) { this.RowKey = key; }
    public String getName() { return this.Name; }
    public void setName(String name) { this.Name = name; }
}

@FunctionName("getPersonsByPartitionKey")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}") HttpRequestMessage<Optional<String>> request,
        @BindingName("partitionKey") String partitionKey,
        @TableInput(name="persons", partitionKey="{partitionKey}", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with partition key: " + partitionKey);

    return persons;
}
```

TableInput 批注还可以从请求的 json 正文中提取绑定，如下面的示例所示。

```java
@FunctionName("GetPersonsByKeysFromRequest")
public HttpResponseMessage get(
        @HttpTrigger(name = "getPerson", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="query") HttpRequestMessage<Optional<String>> request,
        @TableInput(name="persons", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") Person person,
        final ExecutionContext context) {

    if (person == null) {
        return request.createResponseBuilder(HttpStatus.NOT_FOUND)
                    .body("Person not found.")
                    .build();
    }

    return request.createResponseBuilder(HttpStatus.OK)
                    .header("Content-Type", "application/json")
                    .body(person)
                    .build();
}
```

下面的示例使用筛选器在 Azure 表中查询具有特定名称的人员，并将可能的匹配项数目限制为10个结果。

```java
@FunctionName("getPersonsByName")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="filter/{name}") HttpRequestMessage<Optional<String>> request,
        @BindingName("name") String name,
        @TableInput(name="persons", filter="Name eq '{name}'", take = "10", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with name: " + name);

    return persons;
}
```

---

## <a name="input---attributes-and-annotations"></a>输入-特性和批注

# <a name="c"></a>[C#](#tab/csharp)

 在 [C# 类库](functions-dotnet-class-library.md)中，请使用以下属性来配置表输入绑定：

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  该特性的构造函数采用表名称、分区键和行键。 特性可用于 `out` 参数或函数的返回值，如下面的示例中所示：

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  可以设置 `Connection` 属性来指定要使用的存储帐户，如以下示例中所示：

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  有关完整示例，请参阅“输入 - C#”示例。

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  提供另一种方式来指定要使用的存储帐户。 构造函数采用包含存储连接字符串的应用设置的名称。 可以在参数、方法或类级别应用该特性。 以下示例演示类级别和方法级别：

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

要使用的存储帐户按以下顺序确定：

* `Table` 特性的 `Connection` 属性。
* 作为 `StorageAccount` 特性应用到同一参数的 `Table` 特性。
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

在 [Java 函数运行时库](/java/api/overview/azure/functions/runtime)中，对其值将来自表存储的参数使用 `@TableInput` 注释。  可以将此注释与本机 Java 类型、POJO 或使用了 `Optional<T>` 的可为 null 的值一起使用。

---

## <a name="input---configuration"></a>输入 - 配置

下表解释了在 function.json 文件和 `Table` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|type | 不适用 | 必须设置为 `table`。 在 Azure 门户中创建绑定时，会自动设置此属性。|
|direction | 不适用 | 必须设置为 `in`。 在 Azure 门户中创建绑定时，会自动设置此属性。 |
|name | 不适用 | 表示函数代码中的表或实体的变量的名称。 | 
|**tableName** | **TableName** | 表的名称。| 
|**partitionKey** | **PartitionKey** |可选。 要读取的表实体的分区键。 有关如何使用此属性的指导，请参阅[用法](#input---usage)部分。| 
|**rowKey** |**RowKey** | 可选。 要读取的表实体的行键。 有关如何使用此属性的指导，请参阅[用法](#input---usage)部分。| 
|**take** |**Take** | 可选。 要在 JavaScript 中读取的最大实体数。 有关如何使用此属性的指导，请参阅[用法](#input---usage)部分。| 
|**filter** |**筛选器** | 可选。 JavaScript 中的表输入的 OData 筛选表达式。 有关如何使用此属性的指导，请参阅[用法](#input---usage)部分。| 
|连接 |**Connection** | 包含要用于此绑定的存储连接字符串的应用设置的名称。 如果应用设置名称以“AzureWebJobs”开始，则只能在此处指定该名称的余下部分。 例如，如果将 `connection` 设置为 "MyStorage"，函数运行时将查找名为 "MyStorage" 的应用设置。 如果将 `connection` 留空，函数运行时将使用名为 `AzureWebJobsStorage` 的应用设置中的默认存储连接字符串。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>输入 - 用法

# <a name="c"></a>[C#](#tab/csharp)

* **在中读取一行**

  设置 `partitionKey` 和 `rowKey`。 使用方法参数 `T <paramName>` 访问表数据。 在 C# 脚本中，`paramName` 是在 `name`function.json*的* 属性中指定的值。 `T` 通常是实现 `ITableEntity` 或派生自 `TableEntity` 的类型。 此方案中不使用 `filter` 和 `take` 属性。

* **读取一行或多行**

  使用方法参数 `IQueryable<T> <paramName>` 访问表数据。 在 C# 脚本中，`paramName` 是在 `name`function.json*的* 属性中指定的值。 `T` 必须是实现 `ITableEntity` 或派生自 `TableEntity` 的类型。 可以使用 `IQueryable` 方法执行任何所需的筛选。 此方案中不使用 `partitionKey`、`rowKey`、`filter` 和 `take` 属性。  

  > [!NOTE]
  > `IQueryable`Functions v2 运行时[不支持 ](functions-versions.md)。 一种替代方法是[使用 CloudTable paramName 方法参数](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable)通过 Azure 存储 SDK 来读取表。 如果尝试绑定到 `CloudTable` 并收到错误消息，请确保已引用[正确的存储 SDK 版本](#azure-storage-sdk-version-in-functions-1x)。

# <a name="c-script"></a>[C#脚本](#tab/csharp-script)

* **在中读取一行**

  设置 `partitionKey` 和 `rowKey`。 使用方法参数 `T <paramName>` 访问表数据。 在 C# 脚本中，`paramName` 是在 `name`function.json*的* 属性中指定的值。 `T` 通常是实现 `ITableEntity` 或派生自 `TableEntity` 的类型。 此方案中不使用 `filter` 和 `take` 属性。

* **读取一行或多行**

  使用方法参数 `IQueryable<T> <paramName>` 访问表数据。 在 C# 脚本中，`paramName` 是在 `name`function.json*的* 属性中指定的值。 `T` 必须是实现 `ITableEntity` 或派生自 `TableEntity` 的类型。 可以使用 `IQueryable` 方法执行任何所需的筛选。 此方案中不使用 `partitionKey`、`rowKey`、`filter` 和 `take` 属性。  

  > [!NOTE]
  > `IQueryable`Functions v2 运行时[不支持 ](functions-versions.md)。 一种替代方法是[使用 CloudTable paramName 方法参数](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable)通过 Azure 存储 SDK 来读取表。 如果尝试绑定到 `CloudTable` 并收到错误消息，请确保已引用[正确的存储 SDK 版本](#azure-storage-sdk-version-in-functions-1x)。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

设置 `filter` 和 `take` 属性。 不要设置 `partitionKey` 或 `rowKey`。 使用 `context.bindings.<BINDING_NAME>` 访问输入一个或多个输入表实体。 反序列化的对象具有 `RowKey` 和 `PartitionKey` 属性。

# <a name="python"></a>[Python](#tab/python)

表数据作为 JSON 字符串传递给函数。 通过调用 `json.loads` 如输入[示例](#input)中所示对消息进行反序列化。

# <a name="java"></a>[Java](#tab/java)

[TableInput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.tableinput)属性使你能够访问触发函数的表行。

---

## <a name="output"></a>输出

使用 Azure 表存储输出绑定读取将实体写入 Azure 存储帐户中的表。

> [!NOTE]
> 此输出绑定不支持更新现有实体。 请使用 `TableOperation.Replace`Azure 存储 SDK[ 中的 ](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity) 操作来更新现有实体。

# <a name="c"></a>[C#](#tab/csharp)

以下示例演示使用 HTTP 触发器写入单个表行的 [C# 函数](functions-dotnet-class-library.md)。 

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
    public static MyPoco TableOutput([HttpTrigger] dynamic input, ILogger log)
    {
        log.LogInformation($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

# <a name="c-script"></a>[C#脚本](#tab/csharp-script)

以下示例演示 *function.json* 文件中的一个表输出绑定以及使用该绑定的 [C# 脚本](functions-reference-csharp.md)代码。 该函数写入多个表实体。

function.json 文件如下所示：

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
public static void Run(string input, ICollector<Person> tableBinding, ILogger log)
{
    for (int i = 1; i < 10; i++)
        {
            log.LogInformation($"Adding Person entity {i}");
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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

以下示例演示 *function.json* 文件中的一个表输出绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。 该函数写入多个表实体。

function.json 文件如下所示：

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

# <a name="python"></a>[Python](#tab/python)

下面的示例演示如何使用表存储输出绑定。 `table` 绑定通过向 `name`、`tableName`、`partitionKey`和 `connection`赋值来在*函数 json*中进行配置：

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "message",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "connection": "AzureWebJobsStorage",
      "direction": "out"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

以下函数为 `rowKey` 值生成一个唯一的 UUI，并将该消息保存到表存储中。

```python
import logging
import uuid
import json

import azure.functions as func

def main(req: func.HttpRequest, message: func.Out[str]) -> func.HttpResponse:

    rowKey = str(uuid.uuid4())

    data = {
        "Name": "Output binding message",
        "PartitionKey": "message",
        "RowKey": rowKey
    }

    message.set(json.dumps(data))

    return func.HttpResponse(f"Message created with the rowKey: {rowKey}")
```

# <a name="java"></a>[Java](#tab/java)

下面的示例演示一个 Java 函数，该函数使用 HTTP 触发器写入单个表行。

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPerson {

    @FunctionName("addPerson")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPerson", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}/{rowKey}") HttpRequestMessage<Optional<Person>> request,
            @BindingName("partitionKey") String partitionKey,
            @BindingName("rowKey") String rowKey,
            @TableOutput(name="person", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person> person,
            final ExecutionContext context) {

        Person outPerson = new Person();
        outPerson.setPartitionKey(partitionKey);
        outPerson.setRowKey(rowKey);
        outPerson.setName(request.getBody().get().getName());

        person.setValue(outPerson);

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(outPerson)
                        .build();
    }
}
```

下面的示例演示一个 Java 函数，该函数使用 HTTP 触发器来写入多个表行。

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPersons {

    @FunctionName("addPersons")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPersons", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/") HttpRequestMessage<Optional<Person[]>> request,
            @TableOutput(name="person", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person[]> persons,
            final ExecutionContext context) {

        persons.setValue(request.getBody().get());

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(request.getBody().get())
                        .build();
    }
}
```

---

## <a name="output---attributes-and-annotations"></a>输出-属性和批注

# <a name="c"></a>[C#](#tab/csharp)

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)。

该特性的构造函数采用表名称。 特性可用于 `out` 参数或函数的返回值，如下面的示例中所示：

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

可以设置 `Connection` 属性来指定要使用的存储帐户，如以下示例中所示：

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

有关完整示例，请参阅[输出 - C# 示例](#output)。

可以使用 `StorageAccount` 特性在类、方法或参数级别指定存储帐户。 有关详细信息，请参阅[输入 - 特性](#input---attributes-and-annotations)。

# <a name="c-script"></a>[C#脚本](#tab/csharp-script)

C#脚本不支持特性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支持特性。

# <a name="python"></a>[Python](#tab/python)

Python 不支持特性。

# <a name="java"></a>[Java](#tab/java)

在[Java 函数运行时库](/java/api/overview/azure/functions/runtime)中，对参数使用[TableOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/)批注，以将值写入表存储。

[有关更多详细信息](#output)，请参阅示例。

---

## <a name="output---configuration"></a>输出 - 配置

下表解释了在 function.json 文件和 `Table` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|type | 不适用 | 必须设置为 `table`。 在 Azure 门户中创建绑定时，会自动设置此属性。|
|direction | 不适用 | 必须设置为 `out`。 在 Azure 门户中创建绑定时，会自动设置此属性。 |
|name | 不适用 | 在函数代码中使用的、表示表或实体的变量名称。 设置为 `$return` 可引用函数返回值。| 
|**tableName** |**TableName** | 表的名称。| 
|**partitionKey** |**PartitionKey** | 要写入的表实体的分区键。 有关如何使用此属性的指导，请参阅[用法部分](#output---usage)。| 
|**rowKey** |**RowKey** | 要写入的表实体的行键。 有关如何使用此属性的指导，请参阅[用法部分](#output---usage)。| 
|连接 |**Connection** | 包含要用于此绑定的存储连接字符串的应用设置的名称。 如果应用设置名称以“AzureWebJobs”开始，则只能在此处指定该名称的余下部分。 例如，如果将 `connection` 设置为 "MyStorage"，函数运行时将查找名为 "MyStorage" 的应用设置。 如果将 `connection` 留空，函数运行时将使用名为 `AzureWebJobsStorage` 的应用设置中的默认存储连接字符串。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>输出 - 用法

# <a name="c"></a>[C#](#tab/csharp)

使用方法参数 `ICollector<T> paramName` 或 `IAsyncCollector<T> paramName` （其中 `T` 包括 `PartitionKey` 和 `RowKey` 属性）访问输出表实体。 这些属性通常附带 `ITableEntity` 或继承 `TableEntity`。

或者，可以使用 `CloudTable` 方法参数通过 Azure 存储 SDK 写入表。 如果尝试绑定到 `CloudTable` 并收到错误消息，请确保已引用[正确的存储 SDK 版本](#azure-storage-sdk-version-in-functions-1x)。

# <a name="c-script"></a>[C#脚本](#tab/csharp-script)

使用方法参数 `ICollector<T> paramName` 或 `IAsyncCollector<T> paramName` （其中 `T` 包括 `PartitionKey` 和 `RowKey` 属性）访问输出表实体。 这些属性通常附带 `ITableEntity` 或继承 `TableEntity`。 `paramName` 值是在*函数 json*的 `name` 属性中指定的。

或者，可以使用 `CloudTable` 方法参数通过 Azure 存储 SDK 写入表。 如果尝试绑定到 `CloudTable` 并收到错误消息，请确保已引用[正确的存储 SDK 版本](#azure-storage-sdk-version-in-functions-1x)。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

使用 `context.bindings.<name>`，其中 `<name>` 是*函数 json*的 `name` 属性中指定的值来访问输出事件。

# <a name="python"></a>[Python](#tab/python)

有两个选项可用于从函数输出表存储行消息：

- **返回值**：将*函数*中的 `name` 属性设置为 `$return`。 使用此配置时，函数的返回值将持久保存为表存储行。

- **命令式**：向声明为[Out](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python)类型的参数的[set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none)方法传递值。 传递给 `set` 的值将作为事件中心消息保留。

# <a name="java"></a>[Java](#tab/java)

可以通过以下两个选项使用[TableStorageOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.tableoutput?view=azure-java-stablet)批注从函数中输出表存储行：

- **返回值**：通过将批注应用于函数本身，函数的返回值将持久保存为表存储行。

- **命令式**：若要显式设置消息值，请将批注应用于类型[`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding)的特定参数，其中 `T` 包含 `PartitionKey` 和 `RowKey` 属性。 这些属性通常附带 `ITableEntity` 或继承 `TableEntity`。

---

## <a name="exceptions-and-return-codes"></a>异常和返回代码

| 绑定 | 参考 |
|---|---|
| 表 | [表错误代码](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| Blob、表、队列 | [存储错误代码](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob、表、队列 | [故障排除](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 Azure Functions 触发器和绑定](functions-triggers-bindings.md)
