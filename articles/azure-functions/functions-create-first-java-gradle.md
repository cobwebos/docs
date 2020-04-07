---
title: 使用 Java 和 Gradle 将函数发布到 Azure
description: 使用 Java 和 Gradle 创建 HTTP 触发的函数并将其发布到 Azure。
author: KarlErickson
ms.author: karler
ms.topic: how-to
ms.date: 08/10/2018
ms.openlocfilehash: 1a24569a89755a33a80b7f884b803bd36f38ca3f
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757122"
---
# <a name="quickstart-use-java-and-gradle-to-create-and-publish-a-function-to-azure"></a>快速入门：使用 Java 和 Gradle 创建函数并将其发布到 Azure

本文介绍如何使用 Gradle 命令行工具将 Java 函数项目构建并发布到 Azure 函数。 完成后，函数代码会在 Azure 的[无服务器托管计划](functions-scale.md#consumption-plan)中运行，并由 HTTP 请求触发。 

您还可以使用[Maven 原型](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java)从命令行构建和发布 Java 函数项目。

## <a name="prerequisites"></a>先决条件

若要使用 Java 开发函数，必须安装以下软件：

- [Java 开发人员工具包](https://aka.ms/azure-jdks)版本 8
- [Azure CLI]
- [Azure Functions Core Tools](./functions-run-local.md#v2) 版本 2.6.666 或更高版本
- [格拉德尔](https://gradle.org/)， 版本 4.10 及以上

还需要一个有效的 Azure 订阅。 [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> JAVA_HOME 环境变量必须设置为 JDK 的安装位置，以完成本快速入门。

## <a name="prepare-a-functions-project"></a>准备功能项目

使用以下命令克隆示例项目：

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

打开`build.gradle`下一节中`appName`的将更改为唯一名称，以避免在部署到 Azure 时出现域名冲突。 

```gradle
azurefunctions {
    resourceGroup = 'java-functions-group'
    appName = 'azure-functions-sample-demo'
    pricingTier = 'Consumption'
    region = 'westus'
    runtime {
      os = 'windows'
    }
    localDebug = "transport=dt_socket,server=y,suspend=n,address=5005"
}
```

在文本编辑器中打开 *src/main/java* 路径中的新 Function.java 文件，查看生成的代码。 该代码是一个 [HTTP 触发的](functions-bindings-http-webhook.md)函数，用于回显请求的正文。 

> [!div class="nextstepaction"]
> [我遇到了问题](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=generate-project)

## <a name="run-the-function-locally"></a>在本地运行函数

运行以下命令以生成然后运行函数项目：

```bash
gradle jar --info
gradle azureFunctionsRun
```
在本地运行项目时，会看到 Azure Functions Core Tools 的如下所示的输出：

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
</pre>

使用新终端窗口中的以下 cURL 命令从命令行触发函数：

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

预期输出如下：

<pre>
Hello AzureFunctions!
</pre>

在本地运行时，不需要[功能键](functions-bindings-http-webhook-trigger.md#authorization-keys)。  
在终端中使用 `Ctrl+C` 停止函数代码。

> [!div class="nextstepaction"]
> [我遇到了问题](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=local-run)

## <a name="deploy-the-function-to-azure"></a>将函数部署到 Azure

首先部署函数应用时，会在 Azure 中创建函数应用和相关资源。 在部署之前，必须使用 [az login](/cli/azure/authenticate-azure-cli) Azure CLI 命令登录到 Azure 订阅。 

```azurecli
az login
```

> [!TIP]
> 如果帐户可以访问多个订阅，请使用 [az account set](/cli/azure/account#az-account-set) 设置此会话的默认订阅。 

使用以下命令将项目部署到新的函数应用。 

```bash
gradle azureFunctionsDeploy
```

这将根据 build.gradle 文件中的值在 Azure 中创建以下资源：

+ 资源组。 使用你提供的 _resourceGroup_ 命名。
+ 存储帐户。 Functions 所需。 此名称根据存储帐户名称要求随机生成。
+ 应用服务计划。 在指定的_应用区域_中托管函数应用的无服务器消耗计划。 此名称随机生成。
+ 函数应用。 函数应用是函数的部署和执行单元。 此名称是 _appName_ 追加一个随机生成的数字。 

此部署还会在启用“从包运行”模式的情况下，使用 [zip deployment](functions-deployment-technologies.md#zip-deploy) 将项目文件打包并部署到新函数应用。

由于我们发布的 HTTP 触发器使用 `authLevel = AuthorizationLevel.FUNCTION`，因此你需要获取函数密钥，以便通过 HTTP 调用函数终结点。 若要获取函数密钥，最简单的方法是使用 [Azure 门户]。

> [!div class="nextstepaction"]
> [我遇到了问题](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=deploy)

## <a name="get-the-http-trigger-url"></a>获取 HTTP 触发器 URL

可以从 Azure 门户获取通过函数密钥触发函数所需的 URL。 

1. 浏览到 [Azure 门户]，登录，在页面顶部将函数应用的 _appName_ 键入“搜索”栏，**** 然后按 Enter。
 
1. 在函数应用中展开“函数(只读)”，选择你的函数，然后选择右上角的“</> 获取函数 URL”。******** 

    ![从 Azure 门户复制函数 URL](./media/functions-create-java-maven/get-function-url-portal.png)

1. 选择“默认(函数密钥)”，然后选择“复制”********。 

现在可以使用复制的 URL 来访问函数。

## <a name="verify-the-function-in-azure"></a>在 Azure 中验证函数

若要使用 `cURL` 验证在 Azure 上运行的函数应用，请将以下示例中的 URL 替换为从门户复制的 URL。

```console
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpExample?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

这会向函数终结点发送一个 POST 请求，请求正文中包含 `AzureFunctions`。 会看到以下响应。

<pre>
Hello AzureFunctions!
</pre>

> [!div class="nextstepaction"]
> [我遇到了问题](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=verify-deployment)

## <a name="next-steps"></a>后续步骤

我们已使用 HTTP 触发的函数创建 Java 函数项目，在本地计算机上运行该项目，并将其部署到 Azure。 现在，通过以下方式扩展函数

> [!div class="nextstepaction"]
> [添加 Azure 存储队列输出绑定](functions-add-output-binding-storage-queue-java.md)


[Azure CLI]: /cli/azure
[Azure 门户]: https://portal.azure.com
