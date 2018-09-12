---
title: Azure Functions Java 开发人员参考 | Microsoft Docs
description: 了解如何使用 Java 开发函数。
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: Azure Functions, Functions, 事件处理, webhook, 动态计算, 无服务器体系结构, java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 08/10/2018
ms.author: routlaw
ms.openlocfilehash: f0dc471e8875ad0d738fce10421c3586752148b9
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092303"
---
# <a name="azure-functions-java-developer-guide"></a>Azure Functions Java 开发人员指南

[!INCLUDE [functions-java-preview-note](../../includes/functions-java-preview-note.md)]

## <a name="programming-model"></a>编程模型 

Azure 函数应为处理输入并生成输出的无状态类方法。 虽然可编写实例方法，但函数却不能依赖于类的任何实例字段。 所有函数方法都必须具有 `public` 访问修饰符。

可在项目中放置多个函数。 不要将函数放入单独的 jar 中。

## <a name="triggers-and-annotations"></a>触发器和注释

 Azure Functions 由触发器（例如 HTTP 请求、计时器或数据更新）进行调用。 函数需要处理该触发器和任何其他输入以生成一个或多个输出。

使用 [ com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) 包中附带的 Java 注释将输入和输出绑定到方法。 Azure Functions 绑定参考文档和每个注释的 [Java 参考文档](/java/api/com.microsoft.azure.functions.annotation)中都提供了使用注释的示例代码，例如 [HTTP 触发器](/azure/azure-functions/functions-bindings-http-webhook)。

也可在函数的 [function.json](/azure/azure-functions/functions-reference#function-code) 中定义触发器输入和输出，而非通过注释定义。 不建议以此方式使用 `function.json` 而非注释。

> [!IMPORTANT] 
> 必须在 [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) 中配置 Azure 存储帐户，才能本地运行 Azure 存储 Blob、队列或表触发器。

使用注释的示例：

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
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

## <a name="third-party-libraries"></a>第三方库 

Azure Functions 支持使用第三方库。 默认情况下，项目 `pom.xml` 文件中指定的所有依赖项将在 `mvn package` 目标期间自动进行绑定。 对于未在 `pom.xml` 文件中指定为依赖项的库，请将它们放在函数根目录的 `lib` 目录中。 放置在 `lib` 目录中的依赖项将在运行时添加到系统类加载器中。

## <a name="data-type-support"></a>数据类型支持

可对输入和输出数据使用 Java 中的任何数据类型，包括本机类型、自定义的 Java 类型以及 `azure-functions-java-library` 包中定义的专用 Azure 类型。 Azure Functions 运行时会尝试将接收的输入转换为代码请求的类型。

### <a name="strings"></a>字符串

如果函数的相应输入参数类型为 `String` 类型，则传递到函数方法的值将被转换为字符串。 

### <a name="plain-old-java-objects-pojos"></a>普通旧 Java 对象 (POJO)

如果函数的输入签名需要此 Java 类型，则使用 JSON 格式化的字符串将强制转换为 Java 类型。 此转换允许传入 JSON 并使用 Java 类型。

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

空输入值可将 `null` 作为函数参数，但推荐使用 `Optional<T>` 来处理可能存在的空值。


## <a name="function-method-overloading"></a>函数方法重载

可以重载具有相同名称但具有不同类型的函数方法。 例如，可在一个类中同时拥有 `String echo(String s)` 和 `String echo(MyType s)`。 Azure Functions 根据输入类型决定调用哪个方法（对于 HTTP 输入，MIME 类型 `text/plain` 导致 `String`，而 `application/json` 代表 `MyType`）。

## <a name="inputs"></a>输入

在 Azure Functions 中，输入分为两种类别：一种是触发器输入，另一种则是附加输入。 虽然它们在 `function.json` 中并不相同，但它们在 Java 代码中的使用方法却是相同的。 请看以下代码片段示例：

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class MyClass {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { "put" }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String in,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") MyObject obj
    ) {
        return "Hello, " + in + " and " + obj.getKey() + ".";
    }

    public static class MyObject {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
}
```

触发此函数时，由 `String in` 将 HTTP 请求传递给函数。 将根据路由 URL 中的 ID 从 Azure 表存储中检索条目，并在函数体中将此条目设置为 `obj`。

## <a name="outputs"></a>Outputs

输出可以返回值或输出参数表示。 如果只有一个输出，则建议使用返回值。 对于多个输出，必须使用输出参数。

返回值是输出的最简单形式，只需返回任何类型的值，Azure Functions 运行时即可尝试将其封送回实际类型（如 HTTP 响应）。  可对函数方法应用任何输出注释（注释的 name 属性必须为 $return）以定义返回值输出。

若要生成多个输出值，请使用 `azure-functions-java-library` 包中定义的 `OutputBinding<T>` 类型。 如果需要进行 HTTP 响应并将消息推送到队列，则可编写以下代码：

例如，blob 内容复制函数可定义为以下代码。 此处使用 `@StorageAccount` 注释来防止 `@BlobTrigger` 和 `@BlobOutput` 的连接属性重复。

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class MyClass {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

对于参数，请使用 `OutputBinding<byte[]`> 来生成二进制输出值；对于返回值，只需使用 `byte[]` 即可。

## <a name="specialized-types"></a>专用类型

有时，函数必须具有对输入和输出的详细控制。 `azure-functions-java-core` 包中提供了专用类型，用于处理请求信息并定制 HTTP 触发器的返回状态：

| 专用类型      |       目标        | 典型用途                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP 触发器     | 获取方法、标头或查询 |
| `HttpResponseMessage<T>` | HTTP 输出绑定 | 返回除 200 之外的状态   |

> [!NOTE] 
> 还可使用 `@BindingName` 注释来获取 HTTP 标头和查询。 例如，`@BindingName("name") String query` 可循环访问 HTTP 请求标头和查询，并将该值传递给方法。 例如，如果请求 URL 为 `http://example.org/api/echo?name=test`，则 `query` 将为 `"test"`。

### <a name="metadata"></a>元数据

元数据来自不同的源，例如 HTTP 标头、HTTP 查询和[触发器元数据](/azure/azure-functions/functions-triggers-bindings#trigger-metadata-properties)。 配合使用 `@BindingName` 注释和元数据名称来获取值。

例如，如果所请求的URL 为 `http://{example.host}/api/metadata?name=test`，则下列代码片段中的 `queryValue` 将为 `"test"`。

```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class MyClass {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```

## <a name="execution-context"></a>执行上下文

通过 `azure-functions-java-library` 包中定义的 `ExecutionContext` 对象与 Azure Functions 执行环境进行交互。 使用 `ExecutionContext` 对象以使用代码中的调用信息和函数运行时信息。

### <a name="custom-logging"></a>自定义日志记录

可通过 `ExecutionContext` 对象访问函数运行时记录器。 此记录器绑定到 Azure Monitor，并允许标记在函数执行期间遇到的警告和错误。

收到的请求正文为空时，以下示例代码将记录警告消息。

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>查看日志和跟踪

可以使用 Azure CLI 来流式传输出 Java 标准、错误日志记录以及其他应用程序日志记录。 首先，将函数应用程序配置为使用 Azure CLI 编写应用程序日志记录：

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

若要使用 Azure CLI 流式传输函数应用的日志记录输出，请打开新的命令行提示符、Bash 或终端会话，并输入以下命令：

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
[az webapp log tail](/cli/azure/webapp/log) 命令可使用 `--provider` 选项筛选输出。 

若要使用 Azure CLI 下载单个 ZIP 文件形式的日志文件，请打开新的命令提示符、Bash 或终端会话，并输入以下命令：

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

运行此命令之前，必须已在 Azure 门户或 Azure CLI 中启用了文件系统日志记录。

## <a name="environment-variables"></a>环境变量

出于安全原因，请保留源代码中的密钥或令牌等机密信息。 通过在环境变量中读取密钥和令牌，可在函数代码中使用它们。

要在本地运行 Azure Functions 时设置环境变量，可选择将这些变量添加到 local.settings.json 文件中。 如果函数项目的根目录中没有此文件，则可创建一个。 文件的内容应如下所示：

```xml
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "AzureWebJobsDashboard": ""
  }
}
```

`values` 映射中的每个键/值映射都能在运行时用作环境变量，并可通过调用 `System.getenv("<keyname>")` 进行访问，例如 `System.getenv("AzureWebJobsStorage")`。 建议添加其他键/值对。

> [!NOTE]
> 如果采用此方法，请确保将 local.settings.json 文件添加到存储库忽略文件中，从而不提交此文件。

现在，你的代码依赖于这些环境变量，你可登录 Azure 门户以在函数应用设置中设置相同的键/值对，从而让代码在本地测试时和在部署到 Azure 时等效运行。

## <a name="next-steps"></a>后续步骤

有关 Azure 函数 Java 开发的详细信息，请参阅以下资源：

* [Azure Functions 最佳实践](functions-best-practices.md)
* [Azure Functions 开发人员参考](functions-reference.md)
* [Azure Functions 触发器和绑定](functions-triggers-bindings.md)
- 使用 [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)、[IntelliJ](functions-create-maven-intellij.md) 和 [Eclipse](functions-create-maven-eclipse.md) 的本地开发和调试。 
* [使用 Visual Studio Code 远程调试 Java Azure Functions](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [适用于 Azure Functions 的 Maven 插件](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) - 通过 `azure-functions:add` 目标简化函数创建并准备临时目录以用于 [ZIP 文件部署](deployment-zip-push.md)。
