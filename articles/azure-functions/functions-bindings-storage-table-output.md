---
title: 适用于 Azure Functions 的 Azure 表存储输出绑定
description: 了解如何在 Azure Functions 中使用 Azure 表存储输出绑定。
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: ec857db64529a27db7412c61f8f09c66f8a76363
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92098211"
---
# <a name="azure-table-storage-output-bindings-for-azure-functions"></a>适用于 Azure Functions 的 Azure 表存储输出绑定

使用 Azure 表存储输出绑定读取将实体写入 Azure 存储帐户中的表。

> [!NOTE]
> 此输出绑定不支持更新现有实体。 请使用 [Azure 存储 SDK](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity) 中的 `TableOperation.Replace` 操作来更新现有实体。

## <a name="example"></a>示例

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

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

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

[配置](#configuration)部分解释了这些属性。

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

[配置](#configuration)部分解释了这些属性。

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

下面的示例演示如何使用表存储输出绑定。 `table`通过将值分配给*function.json* `name` 、 `tableName` 、和，在function.js中配置绑定 `partitionKey` `connection` ：

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

以下函数为值生成唯一的 UUI `rowKey` ，并将该消息保存到表存储中。

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

以下示例演示了使用 HTTP 触发器写入单个表行的 Java 函数。

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

以下示例演示了使用 HTTP 触发器写入多个表行的 Java 函数。

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

## <a name="attributes-and-annotations"></a>特性和注释

# <a name="c"></a>[C#](#tab/csharp)

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)。

该特性的构造函数采用表名称。 可对函数的 `out` 参数或返回值使用该特性，如以下示例中所示：

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

有关完整示例，请参阅 [c # 示例](#example)。

可以使用 `StorageAccount` 特性在类、方法或参数级别指定存储帐户。 有关详细信息，请参阅[输入 - 特性](./functions-bindings-storage-table-input.md#attributes-and-annotations)。

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

C# 脚本不支持特性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支持特性。

# <a name="python"></a>[Python](#tab/python)

Python 不支持特性。

# <a name="java"></a>[Java](#tab/java)

在 [Java 函数运行时库](/java/api/overview/azure/functions/runtime)中，对参数使用 [TableOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) 注释以将值写入到表存储中。

请参阅[示例来了解更多详细信息](#example)。

---

## <a name="configuration"></a>配置

下表解释了在 function.json 文件和 `Table` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**type** | 不适用 | 必须设置为 `table`。 在 Azure 门户中创建绑定时，会自动设置此属性。|
|**direction** | 不适用 | 必须设置为 `out`。 在 Azure 门户中创建绑定时，会自动设置此属性。 |
|**name** | 不适用 | 在函数代码中使用的、表示表或实体的变量名称。 设置为 `$return` 可引用函数返回值。| 
|**tableName** |**TableName** | 表的名称。| 
|**partitionKey** |**PartitionKey** | 要写入的表实体的分区键。 有关如何使用此属性的指导，请参阅[用法部分](#usage)。| 
|**rowKey** |**RowKey** | 要写入的表实体的行键。 有关如何使用此属性的指导，请参阅[用法部分](#usage)。| 
|连接 |**Connection** | 包含要用于此绑定的存储连接字符串的应用设置的名称。 如果应用设置名称以“AzureWebJobs”开始，则只能在此处指定该名称的余下部分。 例如，如果将 `connection` 设置为“MyStorage”，Functions 运行时将会查找名为“MyStorage”的应用设置。 如果将 `connection` 留空，函数运行时将使用名为 `AzureWebJobsStorage` 的应用设置中的默认存储连接字符串。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用情况

# <a name="c"></a>[C#](#tab/csharp)

可以使用方法参数 `ICollector<T> paramName` 或 `IAsyncCollector<T> paramName` 访问输出表实体，其中 `T` 包括 `PartitionKey` 和 `RowKey` 属性。 实现 `ITableEntity` 或继承 `TableEntity` 时通常会伴随使用这些属性。

另外，还可以使用 `CloudTable` 方法参数通过 Azure 存储 SDK 来写入到表。 如果在尝试绑定到 `CloudTable` 时出现错误消息，请确保引用[正确的存储 SDK 版本](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)。

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

可以使用方法参数 `ICollector<T> paramName` 或 `IAsyncCollector<T> paramName` 访问输出表实体，其中 `T` 包括 `PartitionKey` 和 `RowKey` 属性。 实现 `ITableEntity` 或继承 `TableEntity` 时通常会伴随使用这些属性。 `paramName` 值是在 *function.json* 的 `name` 属性中指定的。

另外，还可以使用 `CloudTable` 方法参数通过 Azure 存储 SDK 来写入到表。 如果在尝试绑定到 `CloudTable` 时出现错误消息，请确保引用[正确的存储 SDK 版本](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

使用 `context.bindings.<name>` 访问输出事件，其中 `<name>` 是 function.json 的 `name` 属性中指定的值。

# <a name="python"></a>[Python](#tab/python)

有两个选项可用于从函数输出表存储行消息：

- **返回值**：将 function.json 中的 `name` 属性  设置为 `$return`。 使用此配置时，函数的返回值将持久保存为表存储行。

- **命令性**：将值传递给声明为 [Out](/python/api/azure-functions/azure.functions.out?view=azure-python&preserve-view=true) 类型的参数的 [set](/python/api/azure-functions/azure.functions.out?view=azure-python&preserve-view=true#set-val--t-----none) 方法。 传递给 `set` 的值将作为事件中心消息保留。

# <a name="java"></a>[Java](#tab/java)

有两个选项可用于使用 [TableStorageOutput](/java/api/com.microsoft.azure.functions.annotation.tableoutput?view=azure-java-stablet&preserve-view=true) 注释从函数来输出表存储行：

- **返回值**：通过将注释应用于函数本身，函数的返回值将持久保存为表存储行。

- **命令性**：若要显式设置消息值，请将注释应用于 [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) 类型的特定参数，其中 `T` 包括 `PartitionKey` 和 `RowKey` 属性。 实现 `ITableEntity` 或继承 `TableEntity` 时通常会伴随使用这些属性。

---

## <a name="exceptions-and-return-codes"></a>异常和返回代码

| 绑定 | 参考 |
|---|---|
| 表 | [表错误代码](/rest/api/storageservices/fileservices/table-service-error-codes) |
| Blob、表、队列 | [存储错误代码](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob、表、队列 | [故障排除](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 Azure Functions 触发器和绑定](functions-triggers-bindings.md)
