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
ms.date: 09/14/2018
ms.author: routlaw
ms.openlocfilehash: d88fda62c59d01a3703fdb583e0881aa8478a6cd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050761"
---
# <a name="azure-functions-java-developer-guide"></a>Azure Functions Java 开发人员指南

Azure Functions 运行时支持 [Java SE 8 LTS (zulu8.31.0.2-jre8.0.181-win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/)。 本指南包含有关使用 Java 编写 Azure Functions 的复杂性的信息。

Java 函数是`public`方法，带有批注修饰`@FunctionName`。 此方法定义对于 Java 函数，条目，并在特定包中必须唯一。 

本文假定你已阅读 [Azure Functions 开发人员参考](functions-reference.md)。 您还应当完成函数快速入门，以创建第一个函数，通过使用[Visual Studio Code](functions-create-first-function-vs-code.md)或[Maven](functions-create-first-java-maven.md)。

## <a name="programming-model"></a>编程模型 

[触发器和绑定](functions-triggers-bindings.md)是 Azure Functions 的基本概念。 触发器启动代码的执行。 绑定可让你向函数传递数据以及从函数返回数据，而无需编写自定义的数据访问代码。

## <a name="folder-structure"></a>文件夹结构

下面是 Azure Functions Java 项目的文件夹结构：

```
FunctionsProject
 | - src
 | | - main
 | | | - java
 | | | | - FunctionApp
 | | | | | - MyFirstFunction.java
 | | | | | - MySecondFunction.java
 | - target
 | | - azure-functions
 | | | - FunctionApp
 | | | | - FunctionApp.jar
 | | | | - host.json
 | | | | - MyFirstFunction
 | | | | | - function.json
 | | | | - MySecondFunction
 | | | | | - function.json
 | | | | - bin
 | | | | - lib
 | - pom.xml
```

您可以使用一个共享[host.json](functions-host-json.md)文件来配置函数应用。 每个函数都有自己的代码文件 (.Java) 和绑定配置文件 (function.json)。

可在项目中放置多个函数。 不要将函数放入单独的 jar 中。 `FunctionApp`在目标目录是部署到 Azure 中的函数应用获取的内容。

## <a name="triggers-and-annotations"></a>触发器和注释

 函数调用由触发器，如 HTTP 请求、 一个计时器或对数据的更新。 函数需要处理该触发器，以及任何其他输入，生成一个或多个输出。

使用 [ com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) 包中附带的 Java 注释将输入和输出绑定到方法。 有关详细信息，请参阅[Java 参考文档](/java/api/com.microsoft.azure.functions.annotation)。

> [!IMPORTANT] 
> 必须配置 Azure 存储帐户中的您[local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file)本地运行 Azure Blob 存储、 Azure 队列存储或 Azure 表存储触发器。

示例：

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

下面是生成相应`function.json`由[azure 函数的 maven 插件](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin):

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.Function.echo",
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

## <a name="jdk-runtime-availability-and-support"></a>JDK 运行时的可用性与支持 

对于 Java 函数应用在本地开发，下载并使用[适用于 Azure 的 Azul Zulu 企业](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf)从 Java 8 Jdk [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/)。 将函数应用部署到云时，Azure Functions 使用 Azul Java 8 JDK 运行时。

[Azure 支持](https://azure.microsoft.com/support/)Jdk 和函数的问题的应用可提供与[限定的支持计划](https://azure.microsoft.com/support/plans/)。

## <a name="customize-jvm"></a>自定义 JVM

Functions 可自定义 Java 虚拟机 (JVM) 用于运行 Java 函数。 [以下 JVM 选项](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7)默认情况下使用：

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

您可以提供其他参数中的应用设置命名`JAVA_OPTS`。 可以将应用设置添加到函数应用在 Azure 门户或 Azure CLI 部署到 Azure。

### <a name="azure-portal"></a>Azure 门户

在中[Azure 门户](https://portal.azure.com)，使用[应用程序设置选项卡](functions-how-to-use-azure-function-app-settings.md#settings)若要添加`JAVA_OPTS`设置。

### <a name="azure-cli"></a>Azure CLI

可以使用[az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings)命令设置`JAVA_OPTS`，如下面的示例：

```azurecli-interactive
az functionapp config appsettings set --name <APP_NAME> \
--resource-group <RESOURCE_GROUP> \
--settings "JAVA_OPTS=-Djava.awt.headless=true"
```
此示例将启用无外设模式。 替换`<APP_NAME>`的 function app 中，名称和`<RESOURCE_GROUP> `与资源组。

> [!WARNING]  
> 在[消耗计划](functions-scale.md#consumption-plan)，则必须添加`WEBSITE_USE_PLACEHOLDER`设置的值为`0`。  
此设置也会增加 Java 函数的冷启动时间。

## <a name="third-party-libraries"></a>第三方库 

Azure Functions 支持使用第三方库。 默认情况下，指定在项目中所有依赖项`pom.xml`文件将自动捆绑在过程[ `mvn package` ](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage)目标。 对于未在 `pom.xml` 文件中指定为依赖项的库，请将它们放在函数根目录的 `lib` 目录中。 依赖项放置在`lib`目录添加到系统类加载程序在运行时。

`com.microsoft.azure.functions:azure-functions-java-library`依赖项默认情况下提供的类路径上，无需将其纳入`lib`目录。 此外， [azure functions 的 java 辅助角色](https://github.com/Azure/azure-functions-java-worker)添加列出的依赖项[此处](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies)到类路径。

## <a name="data-type-support"></a>数据类型支持

你可以使用普通旧 Java 对象 (Pojo)，在定义类型`azure-functions-java-library`，或基元数据类型，如字符串和整数绑定输入或输出绑定。

### <a name="pojos"></a>Pojo

用于将输入的数据转换为 POJO， [azure functions 的 java 辅助角色](https://github.com/Azure/azure-functions-java-worker)使用[gson](https://github.com/google/gson)库。 用作函数输入的 POJO 类型应是 `public`。

### <a name="binary-data"></a>二进制数据

将二进制输入或输出到绑定`byte[]`，通过设置`dataType`字段中为将 function.json `binary`:

```java
   @FunctionName("BlobTrigger")
    @StorageAccount("AzureWebJobsStorage")
     public void blobTrigger(
        @BlobTrigger(name = "content", path = "myblob/{fileName}", dataType = "binary") byte[] content,
        @BindingName("fileName") String fileName,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Blob trigger function processed a blob.\n Name: " + fileName + "\n Size: " + content.length + " Bytes");
    }
```

如果您期望的 null 值，使用`Optional<T>`。

## <a name="bindings"></a>绑定

输入和输出绑定提供从代码内连接到数据的声明性方式。 一个函数可以有多个输入和输出绑定。

### <a name="input-binding-example"></a>输入的绑定示例

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { "put" }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") TestInputData inputData
        @TableOutput(name = "myOutputTable", tableName = "Person", connection = "AzureWebJobsStorage") OutputBinding<Person> testOutputData,
    ) {
        testOutputData.setValue(new Person(httpbody + "Partition", httpbody + "Row", httpbody + "Name"));
        return "Hello, " + inputReq + " and " + inputData.getKey() + ".";
    }

    public static class TestInputData {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
    public static class Person {
        public String PartitionKey;
        public String RowKey;
        public String Name;

        public Person(String p, String r, String n) {
            this.PartitionKey = p;
            this.RowKey = r;
            this.Name = n;
        }
    }
}
```

调用此函数使用 HTTP 请求。 
- HTTP 请求有效负载传递作为`String`自变量`inputReq`。
- 一个条目将检索从表存储，作为传递`TestInputData`的参数`inputData`。

若要接收一批的输入，可以将绑定到`String[]`， `POJO[]`， `List<String>`，或`List<POJO>`。

```java
@FunctionName("ProcessIotMessages")
    public void processIotMessages(
        @EventHubTrigger(name = "message", eventHubName = "%AzureWebJobsEventHubPath%", connection = "AzureWebJobsEventHubSender", cardinality = Cardinality.MANY) List<TestEventData> messages,
        final ExecutionContext context)
    {
        context.getLogger().info("Java Event Hub trigger received messages. Batch size: " + messages.size());
    }
    
    public class TestEventData {
    public String id;
}

```

每当配置的事件中心内出现新数据时，就会触发此函数。 因为`cardinality`设置为`MANY`，该函数从事件中心接收一批消息。 `EventData` 从事件中心将转换为`TestEventData`函数执行。

### <a name="output-binding-example"></a>输出绑定示例

您可以通过使用输出绑定绑定到的返回值`$return`。 

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

如果有多个输出绑定，请只使用其中一个绑定的返回值。

若要发送多个输出值，请使用 `azure-functions-java-library` 包中定义的 `OutputBinding<T>`。 

```java
@FunctionName("QueueOutputPOJOList")
    public HttpResponseMessage QueueOutputPOJOList(@HttpTrigger(name = "req", methods = { HttpMethod.GET,
            HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "itemsOut", queueName = "test-output-java-pojo", connection = "AzureWebJobsStorage") OutputBinding<List<TestData>> itemsOut, 
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");
       
        String query = request.getQueryParameters().get("queueMessageId");
        String queueMessageId = request.getBody().orElse(query);
        itemsOut.setValue(new ArrayList<TestData>());
        if (queueMessageId != null) {
            TestData testData1 = new TestData();
            testData1.id = "msg1"+queueMessageId;
            TestData testData2 = new TestData();
            testData2.id = "msg2"+queueMessageId;

            itemsOut.getValue().add(testData1);
            itemsOut.getValue().add(testData2);

            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + queueMessageId).build();
        } else {
            return request.createResponseBuilder(HttpStatus.INTERNAL_SERVER_ERROR)
                    .body("Did not find expected items in CosmosDB input list").build();
        }
    }

     public static class TestData {
        public String id;
    }
```

调用此函数在 HttpRequest。 它将写入队列存储的多个值。

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage 和 HttpResponseMessage

 这些定义在`azure-functions-java-library`。 它们是能够使用 HttpTrigger 函数的帮助器类型。

| 专用的类型      |       确定目标        | 典型的使用情况                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP 触发器     | 获取方法、 标头或查询 |
| `HttpResponseMessage` | HTTP 输出绑定 | 返回非 200 状态   |

## <a name="metadata"></a>元数据

少量的触发器会连同输入数据一起发送[触发器元数据](/azure/azure-functions/functions-triggers-bindings)。 可以使用批注`@BindingName`地绑定到触发器元数据。


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
在前面的示例中，`queryValue`绑定到查询字符串参数`name`在 http 请求 URL 中， `http://{example.host}/api/metadata?name=test`。 下面是另一个示例，演示如何将绑定到`Id`从队列触发器元数据。

```java
 @FunctionName("QueueTriggerMetadata")
    public void QueueTriggerMetadata(
        @QueueTrigger(name = "message", queueName = "test-input-java-metadata", connection = "AzureWebJobsStorage") String message,@BindingName("Id") String metadataId,
        @QueueOutput(name = "output", queueName = "test-output-java-metadata", connection = "AzureWebJobsStorage") OutputBinding<TestData> output,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Queue trigger function processed a message: " + message + " with metadaId:" + metadataId );
        TestData testData = new TestData();
        testData.id = metadataId;
        output.setValue(testData);
    }
```

> [!NOTE]
> 在批注中提供的名称必须与匹配的元数据属性。

## <a name="execution-context"></a>执行上下文

`ExecutionContext`定义在`azure-functions-java-library`，包含帮助器方法与 functions 运行时进行通信。

### <a name="logger"></a>记录器

使用`getLogger`中定义`ExecutionContext`，以将日志写入到函数代码中。

示例：

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

可以使用 Azure CLI 将流 Java stdout 和 stderr 日志记录，以及其他应用程序日志记录。 

下面介绍了如何将函数应用配置为使用 Azure CLI 编写应用程序日志记录：

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

通过使用 Azure CLI 流日志记录输出的 function app，请打开新的命令提示符、 Bash 或终端会话中，并输入以下命令：

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
[Az webapp log tail](/cli/azure/webapp/log)命令的选项，通过筛选输出`--provider`选项。 

若要使用 Azure CLI 为单个 ZIP 文件下载日志文件，打开新的命令提示符、 Bash 或终端会话中，并输入以下命令：

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

你必须启用了 Azure 门户或 Azure CLI 中运行此命令之前记录的文件系统。

## <a name="environment-variables"></a>环境变量

在 Functions 中，服务连接字符串等[应用设置](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)在执行过程中将公开为环境变量。 可以通过使用，访问这些设置`System.getenv("AzureWebJobsStorage")`。

例如，您可以添加[AppSetting](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings)，具有名称`testAppSetting`和值`testAppSettingValue`:

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("testAppSetting "+ System.getenv("testAppSettingValue"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>后续步骤

有关 Azure Functions Java 开发的详细信息，请参阅以下资源：

* [Azure Functions 最佳实践](functions-best-practices.md)
* [Azure Functions 开发人员参考](functions-reference.md)
* [Azure Functions 触发器和绑定](functions-triggers-bindings.md)
* 本地开发和调试[Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)， [IntelliJ](functions-create-maven-intellij.md)，和[Eclipse](functions-create-maven-eclipse.md)
* [使用 Visual Studio Code 远程调试 Java Azure Functions](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [适用于 Azure Functions 的 maven 插件](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* 简化通过函数创建`azure-functions:add`目标，并准备好的临时目录[ZIP 文件部署](deployment-zip-push.md)。
