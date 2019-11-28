---
title: 将 Java 函数连接到 Azure 存储
description: 了解如何使用队列存储输出绑定将 HTTP 触发的 Java 函数连接到 Azure 存储。
ms.date: 10/14/2019
ms.topic: quickstart
ms.openlocfilehash: f9c3445efbe14c27cad9de62f5b46f50965e3145
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231204"
---
# <a name="connect-your-java-function-to-azure-storage"></a>将 Java 函数连接到 Azure 存储

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

本文介绍如何将[前一篇快速入门文章](functions-create-first-java-maven.md)中创建的函数与 Azure 存储队列相集成。 添加到此函数的输出绑定会将 HTTP 请求中的数据写入到队列中的消息。

大多数绑定都需要一个存储的连接字符串，函数将使用该字符串来访问绑定的服务。 为便于建立此连接，请使用连同函数应用一起创建的存储帐户。 与此帐户建立的连接已存储在名为 `AzureWebJobsStorage` 的应用设置中。  

## <a name="prerequisites"></a>先决条件

在开始学习本文之前，请完成 [Java 快速入门第 1 部分](functions-create-first-java-maven.md)中的步骤。

## <a name="download-the-function-app-settings"></a>下载函数应用设置

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>启用扩展捆绑包

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

现在，可将存储输出绑定添加到项目。

## <a name="add-an-output-binding"></a>添加输出绑定

在 Java 项目中，绑定被定义为函数方法上的绑定注释。 然后根据这些注释自动生成 *function.json* 文件。

浏览到函数代码在 _src/main/java_ 下的位置，打开 *Function.java* 项目文件，然后将以下参数添加到 `run` 方法定义：

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

`msg` 参数为 [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) 类型，表示函数完成时作为消息写入到输出绑定的字符串集合。 在这种情况下，输出是名为的 `outqueue` 存储队列。 存储帐户的连接字符串由 `connection` 方法设置。 请传递包含存储帐户连接字符串的应用程序设置，而不是传递连接字符串本身。

`run` 方法定义现在应如以下示例所示：  

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION)  
        HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    ...
}
```

## <a name="add-code-that-uses-the-output-binding"></a>添加使用输出绑定的代码

现在可以使用新的 `msg` 参数，从函数代码写入到输出绑定。 将以下代码行添加到成功响应之前，以便将 `name` 的值添加到 `msg` 输出绑定。

```java
msg.setValue(name);
```

使用输出绑定时，无需使用 Azure 存储 SDK 代码进行身份验证、获取队列引用或写入数据。 Functions 运行时和队列输出绑定将为你执行这些任务。

`run` 方法现在应如以下示例所示：

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    context.getLogger().info("Java HTTP trigger processed a request.");

    // Parse query parameter
    String query = request.getQueryParameters().get("name");
    String name = request.getBody().orElse(query);

    if (name == null) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST).body("Please pass a name on the query string or in the request body").build();
    } else {
        // Write the name to the message queue. 
        msg.setValue(name);

        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
    }
}
```

## <a name="update-the-tests"></a>更新测试

由于原型还创建一组测试，因此需更新这些测试，以便处理 `run` 方法签名中的新 `msg` 参数。  

浏览到测试代码在 _src/test/java_ 下的位置，打开 *Function.java* 项目文件，将 `//Invoke` 下的代码行替换为以下代码。

```java
@SuppressWarnings("unchecked")
final OutputBinding<String> msg = (OutputBinding<String>)mock(OutputBinding.class);

// Invoke
final HttpResponseMessage ret = new Function().run(req, msg, context);
``` 

现在可以在本地试用新输出绑定了。

## <a name="run-the-function-locally"></a>在本地运行函数

如前所述，使用以下命令在本地生成项目并启动 Functions 运行时：

```bash
mvn clean package 
mvn azure-functions:run
```

> [!NOTE]  
> 由于已在 host.json 中启用扩展捆绑包，因此在启动期间已下载并安装[存储绑定扩展](functions-bindings-storage-blob.md#packages---functions-2x)以及其他 Microsoft 绑定扩展。

如前所述，使用 cURL 在新的终端窗口中从命令行触发函数：

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

这一次，输出绑定还会在存储帐户中创建名为 `outqueue` 的队列，并添加包含此字符串的消息。

接下来，使用 Azure CLI 查看新队列，并检查是否已添加了一条消息。 也可以使用 [Microsoft Azure 存储资源管理器][Azure Storage Explorer]或 [Azure 门户](https://portal.azure.com)查看队列。

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>重新部署项目 

若要更新已发布的应用，请再次运行以下命令：  

```azurecli
mvn azure-functions:deploy
```

同样，可以使用 cURL 来测试已部署的函数。 如前所述，将 POST 请求正文中的值 `AzureFunctions` 传递到 URL，如以下示例所示：

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

可以再次[检查存储队列消息](#query-the-storage-queue)，以验证输出绑定是否按预期在队列中生成新消息。

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>后续步骤

现已更新 HTTP 触发的函数，使其将数据写入存储队列。 若要详细了解如何使用 Java 开发 Azure Functions，请参阅 [Azure Functions Java 开发人员指南](functions-reference-java.md)及 [Azure Functions 触发器和绑定](functions-triggers-bindings.md)。 有关 Java 中完整 Function 项目的示例，请参阅 [Java Functions 示例](/samples/browse/?products=azure-functions&languages=Java)。 

接下来，应为函数应用启用 Application Insights 监视：

> [!div class="nextstepaction"]
> [启用 Application Insights 集成](functions-monitoring.md#manually-connect-an-app-insights-resource)


[Azure Storage Explorer]: https://storageexplorer.com/