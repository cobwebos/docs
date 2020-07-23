---
title: 将 Java 函数连接到 Azure 存储
description: 了解如何使用队列存储输出绑定将 HTTP 触发的 Java 函数连接到 Azure 存储。
author: KarlErickson
ms.author: karler
ms.date: 10/14/2019
ms.topic: quickstart
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: e5b1250170830af24ddc1f2e3b78965ebcea051e
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86540309"
---
# <a name="connect-your-java-function-to-azure-storage"></a>将 Java 函数连接到 Azure 存储

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

本文介绍如何将[前一篇快速入门文章](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-java&tabs=bash,browser)中创建的函数与 Azure 存储队列相集成。 添加到此函数的输出绑定会将 HTTP 请求中的数据写入到队列中的消息。

大多数绑定都需要一个存储的连接字符串，函数将使用该字符串来访问绑定的服务。 为便于建立此连接，请使用连同函数应用一起创建的存储帐户。 与此帐户建立的连接已存储在名为 `AzureWebJobsStorage` 的应用设置中。  

## <a name="prerequisites"></a>先决条件

在开始学习本文之前，请完成 [Java 快速入门第 1 部分](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-java&tabs=bash,browser)中的步骤。

## <a name="download-the-function-app-settings"></a>下载函数应用设置

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>启用扩展捆绑包

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

现在，可将存储输出绑定添加到项目。

## <a name="add-an-output-binding"></a>添加输出绑定

[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]

## <a name="add-code-that-uses-the-output-binding"></a>添加使用输出绑定的代码

[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]

现在可以在本地试用新输出绑定了。

## <a name="run-the-function-locally"></a>在本地运行函数

如前所述，使用以下命令在本地生成项目并启动 Functions 运行时：

# <a name="maven"></a>[Maven](#tab/maven)
```bash
mvn clean package 
mvn azure-functions:run
```
# <a name="gradle"></a>[Gradle](#tab/gradle) 
```bash
gradle jar --info
gradle azureFunctionsRun
```
---

> [!NOTE]  
> 由于已在 host.json 中启用扩展捆绑包，因此在启动期间已下载并安装[存储绑定扩展](functions-bindings-storage-blob.md#add-to-your-functions-app)以及其他 Microsoft 绑定扩展。

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

# <a name="maven"></a>[Maven](#tab/maven)  
```bash
mvn azure-functions:deploy
```
# <a name="gradle"></a>[Gradle](#tab/gradle)  
```bash
gradle azureFunctionsDeploy
```
---

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
