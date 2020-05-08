---
title: Azure Functions 的移动应用绑定
description: 了解如何在 Azure Functions 中使用 Azure 移动应用绑定。
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 3f16f2ef077a1fc3c82075aaf7b7685f941d0a31
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82559590"
---
# <a name="mobile-apps-bindings-for-azure-functions"></a>Azure Functions 的移动应用绑定 

> [!NOTE]
> Azure 移动应用绑定仅适用于 Azure Functions 1.x。 Azure Functions 2.x 及更高版本不支持这些绑定。

本文介绍如何在 Azure Functions 中使用 [Azure 移动应用](../app-service-mobile/app-service-mobile-value-prop.md)绑定。 Azure Functions 支持移动应用的输入和输出绑定。

使用移动应用绑定可以读取和更新移动应用中的数据表。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>包 - Functions 1.x

[Microsoft.Azure.WebJobs.Extensions.MobileApps](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps) NuGet 包 1.x 版中提供了移动应用绑定。 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.MobileApps/) GitHub 存储库中提供了此包的源代码。

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="input"></a>输入

移动应用输入绑定从移动表终结点加载记录，并将其传递到函数中。 在 C# 函数和 F# 函数中，函数成功退出后，对记录所做的任何更改都会自动发送回表。

## <a name="input---example"></a>输入 - 示例

参阅语言特定的示例：

* [C# 脚本 (.csx)](#input---c-script-example)
* JavaScript

### <a name="input---c-script-example"></a>输入 - C# 脚本示例

以下示例演示 *function.json* 文件中的一个移动应用输入绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。 该函数由具有记录标识符的队列消息触发。 该函数读取指定的记录并修改其 `Text` 属性。

下面是 function.json  文件中的绑定数据：

```json
{
"bindings": [
    {
        "name": "myQueueItem",
        "queueName": "myqueue-items",
        "connection": "",
        "type": "queueTrigger",
        "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id": "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
]
}
```
[配置](#input---configuration)部分解释了这些属性。

C# 脚本代码如下所示：

```cs
#r "Newtonsoft.Json"    
using Newtonsoft.Json.Linq;

public static void Run(string myQueueItem, JObject record)
{
    if (record != null)
    {
        record["Text"] = "This has changed.";
    }    
}
```

### <a name="input---javascript"></a>输入 - JavaScript

以下示例演示 *function.json* 文件中的一个移动应用输入绑定以及使用该绑定的 [JavaScript 脚本函数](functions-reference-node.md)。 该函数由具有记录标识符的队列消息触发。 该函数读取指定的记录并修改其 `Text` 属性。

下面是 function.json  文件中的绑定数据：

```json
{
"bindings": [
    {
        "name": "myQueueItem",
        "queueName": "myqueue-items",
        "connection": "",
        "type": "queueTrigger",
        "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id": "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
]
}
```
[配置](#input---configuration)部分解释了这些属性。

JavaScript 代码如下所示：

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

## <a name="input---attributes"></a>输入 - 特性

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) 特性。

有关可以配置的特性属性的信息，请参阅[下面的“配置”部分](#input---configuration)。

## <a name="input---configuration"></a>输入 - 配置

下表解释了在 function.json  文件和 `MobileTable` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
| type | 不适用 | 必须设置为“mobileTable”|
| direction | 不适用 |必须设置为“in”|
| name | 不适用 | 函数签名中的输入参数的名称。|
|**tableName** |**TableName**|移动应用的数据表的名称|
| **id**| **Id** | 要检索的记录的标识符。 可以是静态的，也可以基于调用函数的触发器。 例如，如果对函数使用队列触发器，则 `"id": "{queueTrigger}"` 会使用队列消息的字符串值作为记录 ID 进行检索。|
|连接 |**Connection**|包含移动应用 URL 的应用设置的名称。 该函数使用此 URL 为移动应用构造所需的 REST 操作。 首先在函数应用中创建应用设置，它包含移动应用的 URL，并在输入绑定的 `connection` 属性中指定应用设置的名称。 URL 类似于 `http://<appname>.azurewebsites.net`。
|**apiKey**|**ApiKey**|包含移动应用 API 密钥的应用设置的名称。 若要[在 Node.js 移动应用中实现 API 密钥](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)，或[在 .NET 移动应用中实现 API 密钥](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key)，请提供该 API 密钥。 为此，请提供该密钥，在函数应用中创建应用设置（包含 API 密钥），然后在输入绑定中添加具有应用设置名称的 `apiKey` 属性。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> 不要与移动应用客户端共享该 API 密钥。 应仅将其安全分布到服务端客户端，如 Azure Functions。 Azure Functions 会将连接信息和 API 密钥存储为应用设置，这样它们就不会在源代码管理储存库中进行检查。 这会保护敏感信息。

## <a name="input---usage"></a>输入 - 用法

在 C# 函数中，当找到具有指定 ID 的记录时，该记录会传递到命名的 [JObject](https://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) 参数。 未找到该记录时，参数值为 `null`。 

在 JavaScript 函数中，该记录会传递给 `context.bindings.<name>` 对象。 未找到该记录时，参数值为 `null`。 

在 C# 函数和 F# 函数中，函数成功退出后，对输入记录（输入参数）所做的任何更改都会自动发回到表。 不能修改 JavaScript 函数中的记录。

## <a name="output"></a>输出

使用移动应用输出绑定将新记录写入移动应用表。  

## <a name="output---example"></a>输出 - 示例

参阅语言特定的示例：

* [C#](#output---c-example)
* [C# 脚本 (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>输出 - C# 示例

以下示例演示由队列消息触发，并在移动应用表中创建记录的 [C# 函数](functions-dotnet-class-library.md)。

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
    TraceWriter log)
{
    return new { Text = $"I'm running in a C# function! {myQueueItem}" };
}
```

### <a name="output---c-script-example"></a>输出 - C# 脚本示例

以下示例演示 *function.json* 文件中的一个移动应用输出绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。 该函数由队列消息触发，可为 `Text` 属性创建一个具有硬编码值的新记录。

下面是 function.json  文件中的绑定数据：

```json
{
"bindings": [
    {
        "name": "myQueueItem",
        "queueName": "myqueue-items",
        "connection": "",
        "type": "queueTrigger",
        "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "out"
    }
]
}
```

[配置](#output---configuration)部分解释了这些属性。

C# 脚本代码如下所示：

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

### <a name="output---javascript-example"></a>输出 - JavaScript 示例

以下示例演示 *function.json* 文件中的一个移动应用输出绑定以及使用该绑定的 [JavaScript 脚本函数](functions-reference-node.md)。 该函数由队列消息触发，可为 `Text` 属性创建一个具有硬编码值的新记录。

下面是 function.json  文件中的绑定数据：

```json
{
"bindings": [
    {
        "name": "myQueueItem",
        "queueName": "myqueue-items",
        "connection": "",
        "type": "queueTrigger",
        "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "out"
    }
],
"disabled": false
}
```

[配置](#output---configuration)部分解释了这些属性。

JavaScript 代码如下所示：

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="output---attributes"></a>输出 - 特性

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) 特性。

有关可以配置的特性属性的信息，请参阅[输出 - 配置](#output---configuration)。 下面是某个方法签名中的 `MobileTable` 特性示例：

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
    TraceWriter log)
{
    ...
}
```

有关完整示例，请参阅[输出 - C# 示例](#output---c-example)。

## <a name="output---configuration"></a>输出 - 配置

下表解释了在 function.json  文件和 `MobileTable` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
| type | 不适用 | 必须设置为“mobileTable”|
| direction | 不适用 |必须设置为“out”|
| name | 不适用 | 函数签名中的输出参数的名称。|
|**tableName** |**TableName**|移动应用的数据表的名称|
|连接 |**MobileAppUriSetting**|包含移动应用 URL 的应用设置的名称。 该函数使用此 URL 为移动应用构造所需的 REST 操作。 首先在函数应用中创建应用设置，它包含移动应用的 URL，并在输入绑定的 `connection` 属性中指定应用设置的名称。 URL 类似于 `http://<appname>.azurewebsites.net`。
|**apiKey**|**ApiKeySetting**|包含移动应用 API 密钥的应用设置的名称。 若要[在 Node.js 移动应用后端中实现 API 密钥](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)，或[在 .NET 移动应用后端中实现 API 密钥](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key)，请提供该 API 密钥。 为此，请提供该密钥，在函数应用中创建应用设置（包含 API 密钥），然后在输入绑定中添加具有应用设置名称的 `apiKey` 属性。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> 不要与移动应用客户端共享该 API 密钥。 应仅将其安全分布到服务端客户端，如 Azure Functions。 Azure Functions 会将连接信息和 API 密钥存储为应用设置，这样它们就不会在源代码管理储存库中进行检查。 这会保护敏感信息。

## <a name="output---usage"></a>输出 - 用法

在 C# 脚本函数中，使用类型为 `out object` 的命名输出参数来访问输出记录。 在 C# 类库中，`MobileTable` 属性可与以下任何类型结合使用：

* `ICollector<T>` 或 `IAsyncCollector<T>`，其中，`T` 为 `JObject`，或包含 `public string Id` 属性的任何类型。
* `out JObject`
* `out T` 或 `out T[]`，其中，`T` 为包含 `public string Id` 属性的任何类型。

在 Node.js 函数中，使用 `context.bindings.<name>` 来访问输出记录。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 Azure Functions 触发器和绑定](functions-triggers-bindings.md)
