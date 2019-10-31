---
title: Azure Functions Java 开发人员参考 | Microsoft Docs
description: 了解如何使用 Java 开发函数。
author: ggailey777
manager: gwallace
keywords: Azure Functions, Functions, 事件处理, webhook, 动态计算, 无服务器体系结构, java
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: glenga
ms.openlocfilehash: 97c721c504c460856796e296fefc33bf01f002f8
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176437"
---
# <a name="azure-functions-java-developer-guide"></a>Azure Functions Java 开发人员指南

Azure Functions 运行时支持[JAVA SE 8 LTS （祖鲁 8.31.0.2-jre 8.0.181-win_x64）](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/)。 本指南包含有关通过 Java 编写 Azure Functions 的复杂性的信息。

在其他语言中，Function App 可能有一个或多个函数。 Java 函数是 `public` 方法，使用批注 `@FunctionName`修饰。 此方法定义 Java 函数的条目，在特定包中必须是唯一的。 用 Java 编写的一个 Function App 可能有多个类，这些类具有使用 `@FunctionName`进行批注的多个公共方法。

本文假定你已阅读 [Azure Functions 开发人员参考](functions-reference.md)。 还应完成函数快速入门，通过使用[Visual Studio Code](functions-create-first-function-vs-code.md)或[Maven](functions-create-first-java-maven.md)来创建第一个函数。

## <a name="programming-model"></a>编程模型 

[触发器和绑定](functions-triggers-bindings.md)是 Azure Functions 的基本概念。 触发器启动代码的执行。 绑定可让你向函数传递数据以及从函数返回数据，而无需编写自定义的数据访问代码。

## <a name="create-java-functions"></a>创建 Java 函数

为了更轻松地创建 Java 函数，有了基于 Maven 的工具和原型，这些工具使用预定义的 Java 模板来帮助您创建具有特定函数触发器的项目。    

### <a name="maven-based-tooling"></a>基于 Maven 的工具

以下开发人员环境包含 Azure Functions 工具，可用于创建 Java 函数项目： 

+ [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Eclipse](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

以上链接文章介绍了如何使用所选的 IDE 创建第一个函数。 

### <a name="project-scaffolding"></a>项目基架

如果更喜欢从终端进行命令行开发，基架基于 Java 的函数项目的最简单方法是使用 `Apache Maven` 原型。 目前有两个函数原型用于 Maven：

+ **Java 原型**：在以下 GroupId 和 artifactId 中发布： [azure： azure 函数原型](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/)：

    ```
    mvn archetype:generate \
        -DarchetypeGroupId=com.microsoft.azure \
        -DarchetypeArtifactId=azure-functions-archetype 
    ```

    若要开始使用此原型，请参阅[Java 快速入门](functions-create-first-java-maven.md)。 

+ 在以下 groupId 和 artifactId Kotlin 下发布的**原型（预览版）** [： azure： Kotlin-原型](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-kotlin-archetype/)：

    ```
    mvn archetype:generate \
        -DarchetypeGroupId=com.microsoft.azure \
        -DarchetypeArtifactId=azure-functions-kotlin-archetype
    ```

可以在[Azure Maven 原型 GitHub 存储库](https://github.com/microsoft/azure-maven-archetypes)中找到这些原型的源代码。


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

_* Kotlin 项目看起来非常相似，因为它仍处于 Maven_

你可以使用共享的[host json](functions-host-json.md)文件来配置 function app。 每个函数都有自己的代码文件 (.Java) 和绑定配置文件 (function.json)。

可在项目中放置多个函数。 不要将函数放入单独的 jar 中。 目标目录中的 `FunctionApp` 将部署到 Azure 中的 function app。

## <a name="triggers-and-annotations"></a>触发器和注释

 函数由触发器调用，如 HTTP 请求、计时器或数据更新。 函数需要处理该触发器以及任何其他输入，以生成一个或多个输出。

使用 [ com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) 包中附带的 Java 注释将输入和输出绑定到方法。 有关详细信息，请参阅[Java 参考文档](/java/api/com.microsoft.azure.functions.annotation)。

> [!IMPORTANT] 
> 若要在本地运行 Azure Blob 存储、Azure 队列存储或 Azure 表存储触发器，必须在[本地设置](/azure/azure-functions/functions-run-local#local-settings-file)中配置 azure 存储帐户。

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

下面是由[maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin)生成的相应 `function.json`：

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

对于 Java function apps 的本地开发，请下载并使用[Azul 系统](https://www.azul.com/downloads/azure-only/zulu/)中 Azul 的适用于 Azure Java 8 Jdk 的[祖鲁企业](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf)。 将函数应用部署到云时，Azure Functions 使用 Azul Java 8 JDK 运行时。

有关 Jdk 和函数应用问题的[Azure 支持](https://azure.microsoft.com/support/)可通过[合格的支持计划](https://azure.microsoft.com/support/plans/)获得。

## <a name="customize-jvm"></a>自定义 JVM

函数可让你自定义用于运行 Java 函数的 Java 虚拟机（JVM）。 默认情况下，使用[以下 JVM 选项](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7)：

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

可以在名为 `JAVA_OPTS`的应用程序设置中提供其他参数。 你可以将应用设置添加到 Azure 门户或 Azure CLI 中部署到 Azure 的函数应用。

### <a name="azure-portal"></a>Azure 门户

在[Azure 门户](https://portal.azure.com)中，使用 "[应用程序设置" 选项卡](functions-how-to-use-azure-function-app-settings.md#settings)添加 `JAVA_OPTS` 设置。

### <a name="azure-cli"></a>Azure CLI

可以使用[az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings)命令设置 `JAVA_OPTS`，如以下示例中所示：

```azurecli-interactive
az functionapp config appsettings set --name <APP_NAME> \
--resource-group <RESOURCE_GROUP> \
--settings "JAVA_OPTS=-Djava.awt.headless=true"
```
此示例启用无外设模式。 将 `<APP_NAME>` 替换为函数应用的名称，并将 `<RESOURCE_GROUP>` 替换为资源组。

> [!WARNING]  
> 在[消耗计划](functions-scale.md#consumption-plan)中，你必须添加值为 `0`的 `WEBSITE_USE_PLACEHOLDER` 设置。  
此设置将增加 Java 函数的冷启动时间。

## <a name="third-party-libraries"></a>第三方库 

Azure Functions 支持使用第三方库。 默认情况下，项目中指定的所有依赖项都将在[`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage)目标中自动捆绑 `pom.xml` 文件中。 对于未在 `pom.xml` 文件中指定为依赖项的库，请将它们放在函数根目录的 `lib` 目录中。 放置在 `lib` 目录中的依赖项在运行时添加到系统类加载程序。

默认情况下，在类路径上提供 `com.microsoft.azure.functions:azure-functions-java-library` 依赖项，并且不需要包含在 `lib` 目录中。 此外， [azure 函数-java 辅助角色](https://github.com/Azure/azure-functions-java-worker)将[此处](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies)列出的依赖项添加到类路径。

## <a name="data-type-support"></a>数据类型支持

您可以使用普通旧 Java 对象（Pojo）、在 `azure-functions-java-library`中定义的类型或基元数据类型（如 String 和 Integer）来绑定到输入或输出绑定。

### <a name="pojos"></a>Pojo

若要将输入数据转换为[POJO，请](https://github.com/Azure/azure-functions-java-worker)使用[gson](https://github.com/google/gson)库。 用作函数输入的 POJO 类型应是 `public`。

### <a name="binary-data"></a>二进制数据

通过将函数 json 中的 `dataType` 字段设置为 `binary`，将二进制输入或输出绑定到 `byte[]`：

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

如果需要空值，请使用 `Optional<T>`。

## <a name="bindings"></a>绑定

输入和输出绑定提供从代码内连接到数据的声明性方式。 一个函数可以有多个输入和输出绑定。

### <a name="input-binding-example"></a>输入绑定示例

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

使用 HTTP 请求调用此函数。 
- HTTP 请求负载作为参数的 `String` 传递 `inputReq`。
- 从表存储中检索一个条目，并将其作为 `TestInputData` 传递到参数 `inputData`。

若要接收一批输入，可以绑定到 `String[]`、`POJO[]`、`List<String>`或 `List<POJO>`。

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

每当配置的事件中心内出现新数据时，就会触发此函数。 由于 `cardinality` 设置为 `MANY`，因此该函数从事件中心接收一批消息。 从事件中心 `EventData` 转换为函数执行的 `TestEventData`。

### <a name="output-binding-example"></a>输出绑定示例

您可以使用 `$return`将输出绑定绑定到返回值。 

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

在 HttpRequest 上调用此函数。 它将多个值写入队列存储。

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage 和 HttpResponseMessage

 这些是在 `azure-functions-java-library`中定义的。 它们是帮助程序类型，可以使用 HttpTrigger 函数。

| 专用类型      |       确定目标        | 典型用法                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP 触发器     | 获取方法、标头或查询 |
| `HttpResponseMessage` | HTTP 输出绑定 | 返回200以外的状态   |

## <a name="metadata"></a>元数据

少量的触发器会连同输入数据一起发送[触发器元数据](/azure/azure-functions/functions-triggers-bindings)。 您可以使用批注 `@BindingName` 绑定到触发器元数据。


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
在前面的示例中，`queryValue` 绑定到 http 请求 URL 中的查询字符串参数 `name`，`http://{example.host}/api/metadata?name=test`。 下面是另一个示例，演示如何从队列触发器元数据绑定到 `Id`。

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
> 批注中提供的名称需要与元数据属性匹配。

## <a name="execution-context"></a>执行上下文

`ExecutionContext`（在 `azure-functions-java-library`中定义）包含与函数运行时通信的 helper 方法。

### <a name="logger"></a>记录器

使用 `ExecutionContext`中定义的 `getLogger`从函数代码写入日志。

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

您可以使用 Azure CLI 来流式传输 Java stdout 和 stderr 日志记录以及其他应用程序日志记录。 

下面介绍如何使用 Azure CLI 将 function app 配置为写入应用程序日志记录：

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

若要使用 Azure CLI 流式传输函数应用的日志记录输出，请打开新的命令提示符、Bash 或终端会话，并输入以下命令：

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
[Az webapp log tail](/cli/azure/webapp/log)命令具有使用 `--provider` 选项筛选输出的选项。 

若要使用 Azure CLI 将日志文件下载为单个 ZIP 文件，请打开新的命令提示符、Bash 或终端会话，并输入以下命令：

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

在运行此命令之前，必须已在 Azure 门户或 Azure CLI 中启用了文件系统日志记录。

## <a name="environment-variables"></a>环境变量

在 Functions 中，服务连接字符串等[应用设置](functions-app-settings.md)在执行过程中将公开为环境变量。 您可以使用 `System.getenv("AzureWebJobsStorage")`访问这些设置。

下面的示例获取[应用程序设置](functions-how-to-use-azure-function-app-settings.md#settings)，其密钥名为 `myAppSetting`：

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>后续步骤

有关 Azure Functions Java 开发的详细信息，请参阅以下资源：

* [Azure Functions 最佳实践](functions-best-practices.md)
* [Azure Functions 开发人员参考](functions-reference.md)
* [Azure Functions 触发器和绑定](functions-triggers-bindings.md)
* [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)、 [IntelliJ](functions-create-maven-intellij.md)和[Eclipse](functions-create-maven-eclipse.md)进行本地开发和调试
* [使用 Visual Studio Code 远程调试 Java Azure Functions](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [用于 Azure Functions 的 Maven 插件](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* 通过 `azure-functions:add` 目标简化函数创建，并为[ZIP 文件部署](deployment-zip-push.md)准备暂存目录。
