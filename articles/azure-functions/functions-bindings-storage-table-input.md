---
title: 适用于 Azure Functions 的 Azure 表存储输入绑定
description: 了解如何在 Azure Functions 中使用 Azure 表存储输入绑定。
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 7f5db2a2df7314c89f2ebba8e7e54ebe24126386
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92098220"
---
# <a name="azure-table-storage-input-bindings-for-azure-functions"></a>适用于 Azure Functions 的 Azure 表存储输入绑定

使用 Azure 表存储输入绑定读取 Azure 存储帐户中的表。

## <a name="example"></a>示例

# <a name="c"></a>[C#](#tab/csharp)

### <a name="one-entity"></a>一个实体

以下示例演示读取单个表行的 [C# 函数](functions-dotnet-class-library.md)。 对于发送到队列的每个消息，将触发该函数。

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

### <a name="cloudtable"></a>CloudTable

`CloudTable` 仅在 [函数 v2 和更高版本的运行](functions-versions.md)时中支持。

使用 `CloudTable` 方法参数通过 Azure 存储 SDK 来读取表。 下面是一个查询 Azure Functions 日志表的函数示例：

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Cosmos.Table;
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

有关如何使用 CloudTable 的详细信息，请参阅 [Azure 表存储入门](../cosmos-db/tutorial-develop-table-dotnet.md)。

如果在尝试绑定到 `CloudTable` 时出现错误消息，请确保引用[正确的存储 SDK 版本](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)。

### <a name="iqueryable"></a>IQueryable

`IQueryable` 仅在 [Functions v1 运行时](functions-versions.md)中受支持。

以下示例显示了一个 [C# 函数](functions-dotnet-class-library.md)，它从 `TableEntity` 中读取其中派生了 `MyPoco` 类的多个表行。

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

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

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

[配置](#configuration)部分解释了这些属性。

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

### <a name="cloudtable"></a>CloudTable

`IQueryable` 在[版本 2.x 及更高版本](functions-versions.md)的 Functions 运行时中不受支持。 一种替代方法是使用 `CloudTable` 方法参数通过 Azure 存储 SDK 来读取表。 下面是一个查询 Azure Functions 日志表的函数示例：

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

有关如何使用 CloudTable 的详细信息，请参阅 [Azure 表存储入门](../cosmos-db/tutorial-develop-table-dotnet.md)。

如果在尝试绑定到 `CloudTable` 时出现错误消息，请确保引用[正确的存储 SDK 版本](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)。

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

[配置](#configuration)部分解释了这些属性。

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

以下示例演示 *function.json* 文件中的一个表输入绑定以及使用该绑定的 [JavaScript 脚本](functions-reference-node.md)代码。 该函数使用队列触发器来读取单个表行。 

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

[配置](#configuration)部分解释了这些属性。

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

以下示例显示了一个 HTTP 触发函数，该函数返回表存储中指定分区中的 person 对象列表。 在此示例中，分区键将从 http 路由中提取，表名和连接将从函数设置中提取。 

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

TableInput 注释还可以从请求的 json 正文中提取绑定，如下面的示例所示。

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

下面的示例使用 Filter 在 Azure 表中查询具有特定名称的人员，并将可能的匹配项数目限制为 10 个结果。

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

## <a name="attributes-and-annotations"></a>特性和注释

# <a name="c"></a>[C#](#tab/csharp)

 在 [C# 类库](functions-dotnet-class-library.md)中，请使用以下属性来配置表输入绑定：

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  该特性的构造函数采用表名称、分区键和行键。 可对函数的 `out` 参数或返回值使用该特性，如以下示例中所示：

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

  有关完整示例，请参阅 c # 示例。

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
* 作为 `Table` 特性应用到同一参数的 `StorageAccount` 特性。
* 应用到函数的 `StorageAccount` 特性。
* 应用到类的 `StorageAccount` 特性。
* 函数应用的默认存储帐户（“AzureWebJobsStorage”应用设置）。

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

C# 脚本不支持特性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支持特性。

# <a name="python"></a>[Python](#tab/python)

Python 不支持特性。

# <a name="java"></a>[Java](#tab/java)

在 [Java 函数运行时库](/java/api/overview/azure/functions/runtime)中，对其值将来自表存储的参数使用 `@TableInput` 注释。  可以将此注释与本机 Java 类型、POJO 或使用了 `Optional<T>` 的可为 null 的值一起使用。

---

## <a name="configuration"></a>配置

下表解释了在 function.json 文件和 `Table` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**type** | 不适用 | 必须设置为 `table`。 在 Azure 门户中创建绑定时，会自动设置此属性。|
|**direction** | 不适用 | 必须设置为 `in`。 在 Azure 门户中创建绑定时，会自动设置此属性。 |
|**name** | 不适用 | 表示函数代码中的表或实体的变量的名称。 | 
|**tableName** | **TableName** | 表的名称。| 
|**partitionKey** | **PartitionKey** |可选。 要读取的表实体的分区键。 有关如何使用此属性的指导，请参阅[用法](#usage)部分。| 
|**rowKey** |**RowKey** | 可选。 要读取的表实体的行键。 有关如何使用此属性的指导，请参阅[用法](#usage)部分。| 
|**take** |**Take** | 可选。 要在 JavaScript 中读取的最大实体数。 有关如何使用此属性的指导，请参阅[用法](#usage)部分。| 
|**filter** |**Filter** | 可选。 JavaScript 中的表输入的 OData 筛选表达式。 有关如何使用此属性的指导，请参阅[用法](#usage)部分。| 
|连接 |**Connection** | 包含要用于此绑定的存储连接字符串的应用设置的名称。 设置可以是带有“AzureWebJobs”前缀的应用设置的名称，也可以是连接字符串的名称。 例如，如果设置名称是“AzureWebJobsMyStorage”，则可以在此处指定“MyStorage”。 Functions 运行时将自动查找名为“AzureWebJobsMyStorage”的应用设置。 如果将 `connection` 留空，函数运行时将使用名为 `AzureWebJobsStorage` 的应用设置中的默认存储连接字符串。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用情况

# <a name="c"></a>[C#](#tab/csharp)

* **读取一行**

  设置 `partitionKey` 和 `rowKey`。 使用方法参数 `T <paramName>` 访问表数据。 在 C# 脚本中，`paramName` 是在 *function.json* 的 `name` 属性中指定的值。 `T` 通常是实现 `ITableEntity` 或派生自 `TableEntity` 的类型。 此方案中不使用 `filter` 和 `take` 属性。

* **读取一行或多行**

  使用方法参数 `IQueryable<T> <paramName>` 访问表数据。 在 C# 脚本中，`paramName` 是在 *function.json* 的 `name` 属性中指定的值。 `T` 必须是实现 `ITableEntity` 或派生自 `TableEntity` 的类型。 可以使用 `IQueryable` 方法执行任何所需的筛选。 此方案中不使用 `partitionKey`、`rowKey`、`filter` 和 `take` 属性。  

  > [!NOTE]
  > [Functions v2 运行时](functions-versions.md)不支持 `IQueryable`。 一种替代方法是[使用 CloudTable paramName 方法参数](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable)通过 Azure 存储 SDK 来读取表。 如果在尝试绑定到 `CloudTable` 时出现错误消息，请确保引用[正确的存储 SDK 版本](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)。

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

* **读取一行**

  设置 `partitionKey` 和 `rowKey`。 使用方法参数 `T <paramName>` 访问表数据。 在 C# 脚本中，`paramName` 是在 *function.json* 的 `name` 属性中指定的值。 `T` 通常是实现 `ITableEntity` 或派生自 `TableEntity` 的类型。 此方案中不使用 `filter` 和 `take` 属性。

* **读取一行或多行**

  使用方法参数 `IQueryable<T> <paramName>` 访问表数据。 在 C# 脚本中，`paramName` 是在 *function.json* 的 `name` 属性中指定的值。 `T` 必须是实现 `ITableEntity` 或派生自 `TableEntity` 的类型。 可以使用 `IQueryable` 方法执行任何所需的筛选。 此方案中不使用 `partitionKey`、`rowKey`、`filter` 和 `take` 属性。  

  > [!NOTE]
  > [Functions v2 运行时](functions-versions.md)不支持 `IQueryable`。 一种替代方法是[使用 CloudTable paramName 方法参数](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable)通过 Azure 存储 SDK 来读取表。 如果在尝试绑定到 `CloudTable` 时出现错误消息，请确保引用[正确的存储 SDK 版本](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

设置 `filter` 和 `take` 属性。 不要设置 `partitionKey` 或 `rowKey`。 使用 `context.bindings.<BINDING_NAME>` 访问输入一个或多个输入表实体。 反序列化的对象具有 `RowKey` 和 `PartitionKey` 属性。

# <a name="python"></a>[Python](#tab/python)

表数据作为 JSON 字符串传递给函数。 通过调用来反序列化消息， `json.loads` 如输入 [示例](#example)中所示。

# <a name="java"></a>[Java](#tab/java)

使用 [TableInput](/java/api/com.microsoft.azure.functions.annotation.tableinput) 特性可以访问触发了函数的表行。

---

## <a name="next-steps"></a>后续步骤

* [从函数中写入表存储数据](./functions-bindings-storage-table-output.md)
