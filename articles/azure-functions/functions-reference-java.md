---
title: "Azure Functions Java 开发人员参考 | Microsoft Docs"
description: "了解如何使用 Java 开发函数。"
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: "Azure Functions, Functions, 事件处理, webhook, 动态计算, 无服务器体系结构, java"
ms.service: functions
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/20/2017
ms.author: routlaw
ms.openlocfilehash: f8812c2e8ac3398dabd17feaf97897efca5566f3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-java-developer-guide"></a>Azure Functions Java 开发人员指南
> [!div class="op_single_selector"]
[!INCLUDE [functions-selector-languages](../../includes/functions-selector-languages.md)]

## <a name="programming-model"></a>编程模型 

Azure 函数应为处理输入并生成输出的无状态类方法。 虽然支持编写实例方法，但函数却不能依赖于类的任何实例字段。 所有函数方法都必须具有 `public` 访问修饰符。

## <a name="triggers-and-annotations"></a>触发器和注释

通常情况下，Azure 函数是因外部触发器而调用的。 函数需要处理该触发器以及与之关联的输入，并生成一个或多个输出。

`azure-functions-java-core` 包中包含了 Java 注释，以便将输入和输出绑定到方法。 下表中列出了受支持的输入触发器和输出绑定注释：

绑定 | 批注
---|---
CosmosDB | 不适用
HTTP | <ul><li>`HttpTrigger`</li><li>`HttpOutput`</li></ul>
移动应用 | 不适用
通知中心 | 不适用
存储 Blob | <ul><li>`BlobTrigger`</li><li>`BlobOutput`</li><li>`BlobOutput`</li></ul>
存储队列 | <ul><li>`QueueTrigger`</li><li>`QueueOutput`</li></ul>
存储表 | <ul><li>`TableInput`</li><li>`TableOutput`</li></ul>
计时器 | <ul><li>`TimerTrigger`</li></ul>
Twilio | 不适用

也可在 [function.json](/azure/azure-functions/functions-reference#function-code) 中定义应用程序的触发器输入和输出。

> [!IMPORTANT] 
> 必须在 [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) 中配置 Azure 存储帐户，才能本地运行 Azure 存储 Blob、队列或表触发器。

使用注释的示例：

```java
import com.microsoft.azure.serverless.functions.annotation.HttpTrigger;
import com.microsoft.azure.serverless.functions.ExecutionContext;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

没有使用注释编写的相同函数：

```java
package com.example;

public class MyClass {
    public static String echo(String in) {
        return in;
    }
}
```

包含相应的 `function.json`：

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="data-types"></a>数据类型

可在 Java 中对输入和输出数据自由使用所有数据类型，包括本机类型、自定义 Java 类型和在 `azure-functions-java-core` 包中定义的专用 Azure 类型。 Azure Functions 运行时会尝试将接收的输入转换为代码请求的类型。

### <a name="strings"></a>字符串

如果函数的相应输入参数类型为 `String` 类型，则传递到函数方法的值将被转换为字符串。 

### <a name="plain-old-java-objects-pojos"></a>普通旧 Java 对象 (POJO)

如果函数方法的输入需要该 Java 类型，则格式为 JSON 的字符串将被转换为 Java 类型。 通过此转换，可将 JSON 输入传递到函数并使用代码中的 Java 类型，无需在自己的代码中实现转换。

用作函数输入的 POJO 类型必须具有与在函数中使用的函数方法相同的 `public` 访问修饰符。 无需声明 POJO 类字段 `public`。 例如，可将 JSON 字符串 `{ "x": 3 }` 转换为以下 POJO 类型：

```Java
public class MyData {
    private int x;
}
```

### <a name="binary-data"></a>二进制数据

在 Azure 函数代码中，二进制数据表示为 `byte[]`。 通过将 function.json 中的 `dataType` 字段设置为 `binary`，将二进制输入或输出绑定到函数：

```json
 {
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "blob",
      "name": "content",
      "direction": "in",
      "dataType": "binary",
      "path": "container/myfile.bin",
      "connection": "ExampleStorageAccount"
    },
  ]
}
```

然后在函数代码中进行使用：

```java
// Class definition and imports are omitted here
public static String echoLength(byte[] content) {
}
```

使用 `OutputBinding<byte[]>` 类型进行二进制输出绑定。


## <a name="function-method-overloading"></a>函数方法重载

可以重载具有相同名称但具有不同类型的函数方法。 例如，一个类中可以同时具有 `String echo(String s)` 和 `String echo(MyType s)`，并且 Azure Functions 运行时可通过检查实际输入类型来决定调用哪种方法（对于 HTTP 输入，MIME 类型 `text/plain` 通向 `String`，而 `application/json` 表示 `MyType`）。

## <a name="inputs"></a>输入

在 Azure Functions 中，输入分为两种类别：一种是触发器输入，另一种则是附加输入。 虽然它们在 `function.json` 中并不相同，但它们在 Java 代码中的使用方法却是相同的。 请看以下代码片段示例：

```java
package com.example;

import com.microsoft.azure.serverless.functions.annotation.BindingName;

public class MyClass {
    public static String echo(String in, @BindingName("item") MyObject obj) {
        return "Hello, " + in + " and " + obj.getKey() + ".";
    }

    private static class MyObject {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
}
```

`@BindingName` 注释接受了 `String` 属性，此属性表示在 `function.json` 中定义的绑定/触发器的名称：

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "put" ],
      "route": "items/{id}"
    },
    {
      "type": "table",
      "name": "item",
      "direction": "in",
      "tableName": "items",
      "partitionKey": "Example",
      "rowKey": "{id}",
      "connection": "ExampleStorageAccount"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}
```

因此，调用此函数时，HTTP 请求有效负载会为参数 `in` 传递 `String` 并将 Azure 表存储 `MyObject` 类型传递给参数 `obj`。

## <a name="outputs"></a>Outputs

输出可以返回值或输出参数表示。 如果只有一个输出，则建议使用返回值。 对于多个输出，必须使用输出参数。

返回值是输出的最简单形式，只需返回任何类型的值，Azure Functions 运行时即可尝试将其封送回实际类型（如 HTTP 响应）。 在 `functions.json` 中，使用 `$return` 作为输出绑定的名称。

若要生成多个输出值，请使用 `azure-functions-java-core` 包中定义的 `OutputParameter<T>` 类型。 如果需要进行 HTTP 响应并将消息推送到队列，则可编写以下代码：

```java
package com.example;

import com.microsoft.azure.serverless.functions.OutputParameter;
import com.microsoft.azure.serverless.functions.annotation.BindingName;

public class MyClass {
    public static String echo(String body, @BindingName("message") OutputParameter<String> queue) {
        String result = "Hello, " + body + ".";
        queue.setValue(result);
        return result;
    }
}
```

并在 `function.json` 中定义输出绑定：

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "queue",
      "name": "message",
      "direction": "out",
      "queueName": "myqueue",
      "connection": "ExampleStorageAccount"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}
```
## <a name="specialized-types"></a>专用类型

有时，函数必须具有对输入和输出的详细控制。 提供 `azure-functions-java-core` 包中的专用类型，用于处理请求信息和定制 HTTP 触发器的返回状态：

| 专用类型      |       目标        | 典型用途                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage`  |    HTTP 触发器     | 获取方法、标头或查询 |
| `HttpResponseMessage` | HTTP 输出绑定 | 返回除 200 之外的状态   |

> [!NOTE] 
> 还可使用 `@BindingName` 注释来获取 HTTP 标头和查询。 例如，`@Bind("name") String query` 可循环访问 HTTP 请求标头和查询，并将该值传递给方法。 例如，如果请求 URL 为 `http://example.org/api/echo?name=test`，则 `query` 将为 `"test"`。

## <a name="functions-execution-context"></a>函数执行上下文

可通过 `azure-functions-java-core` 包中定义的 `ExecutionContext` 对象与 Azure Functions 执行环境交互。 使用 `ExecutionContext` 对象以使用代码中的调用信息和函数运行时信息。

### <a name="logging"></a>日志记录

可通过 `ExecutionContext` 对象访问函数运行时记录器。 此记录器绑定到 Azure Monitor，并允许标记在函数执行期间遇到的警告和错误。

收到的请求正文为空时，以下示例代码将记录警告消息。

```java
import com.microsoft.azure.serverless.functions.annotation.HttpTrigger;
import com.microsoft.azure.serverless.functions.ExecutionContext;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received in " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅以下资源：

* [Azure Functions 最佳实践](functions-best-practices.md)
* [Azure Functions 开发人员参考](functions-reference.md)
* [Azure Functions 触发器和绑定](functions-triggers-bindings.md)
