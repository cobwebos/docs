---
title: 在 Azure 中通过 Java 和 Maven 创建你的第一个函数 | Microsoft Docs
description: 通过 Java 和 Maven 创建一个简单的 HTTP 触发函数，并将其发布到 Azure。
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: azure functions, functions, 事件处理, 计算, 无服务器体系结构
ms.service: functions
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/10/2018
ms.author: routlaw, glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: 3840d8d1e18e73c1f08c450859032c07e441cff2
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2018
ms.locfileid: "43699169"
---
# <a name="create-your-first-function-with-java-and-maven-preview"></a>通过 Java 和 Maven 创建你的第一个函数（预览版）

[!INCLUDE [functions-java-preview-note](../../includes/functions-java-preview-note.md)]

本快速入门可指导通过 Maven 创建[无服务器](https://azure.microsoft.com/overview/serverless-computing/)函数项目，在本地对其进行测试，并将其部署到 Azure Functions。 完成后，可在 Azure 中运行 HTTP 触发的函数应用。

![通过 cURL 从命令行中访问 Hello World 函数](media/functions-create-java-maven/hello-azure.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件
若要通过 Java 开发函数应用，必须安装以下软件：

-  [Java 开发人员工具包](https://www.azul.com/downloads/zulu/)，版本 8。
-  [Apache Maven](https://maven.apache.org) 3.0 或更高版本。
-  [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> JAVA_HOME 环境变量必须设置为 JDK 的安装位置，以完成本快速入门。

## <a name="install-the-azure-functions-core-tools"></a>安装 Azure Functions Core Tools

Azure Functions Core Tools 为从终端或命令提示符编写、运行和调试 Azure Functions 提供了本地开发环境。 

在继续操作前，请在本地计算机上安装 [Core Tools 版本 2](functions-run-local.md#v2)。

## <a name="generate-a-new-functions-project"></a>生成新的 Functions 项目

在空的文件夹中，运行以下命令以从 [Maven archetype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html) 生成 Functions 项目。

### <a name="linuxmacos"></a>Linux/MacOS

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

### <a name="windows-cmd"></a>Windows (CMD)
```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-archetype
```

Maven 会请求你提供所需的值以完成项目的生成。 有关 groupId、artifactId 和 version 值，请参阅 [Maven 命名约定](https://maven.apache.org/guides/mini/guide-naming-conventions.html)参考。 AppName 值在 Azure 中必须唯一，以便 Maven 基于以前输入的 artifactId 生成默认应用名称。 PackageName 值确定所生成函数代码的 Java 包。

`appRegion` 值指定要在其中运行已部署的函数应用的 [Azure 区域](https://azure.microsoft.com/global-infrastructure/regions/)。 可以通过 Azure CLI 中的 `az account list-locations` 命令获取区域名称值的列表。 `resourceGroup` 值指定将在其中创建函数应用的 Azure 资源组。

下面的 `com.fabrikam.functions` 和 `fabrikam-functions` 标识符用作示例，目的是使本快速入门中后面的步骤更易读。 建议你在此步骤中向 Maven 提供你自己的值。

```Output
Define value for property 'groupId': com.fabrikam.functions
Define value for property 'artifactId' : fabrikam-functions
Define value for property 'version' 1.0-SNAPSHOT : 
Define value for property 'package': com.fabrikam.functions
Define value for property 'appName' fabrikam-functions-20170927220323382:
Define value for property 'appRegion' westus : 
Define value for property 'resourceGroup' java-functions-group: 
Confirm properties configuration: Y
```

在此示例 `fabrikam-functions` 中，Maven 在新文件夹中创建名为 artifactId 的项目文件 项目中生成的可以运行的代码是一个简单的在“Hello, ”字符串回显请求正文的 [HTTP 触发](/azure/azure-functions/functions-bindings-http-webhook)函数。

```java
public class Function {
    @FunctionName("HttpTrigger-Java")
    public HttpResponseMessage HttpTriggerJava(
    @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");

        // Parse query parameter
        String query = request.getQueryParameters().get("name");
        String name = request.getBody().orElse(query);

        if (name == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST).body("Please pass a name on the query string or in the request body").build();
        } else {
            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        }
    }
}
```

## <a name="run-the-function-locally"></a>在本地运行函数

将目录更改为新创建的项目文件夹，并通过 Maven 生成和运行此函数：

```
cd fabrikam-functions
mvn clean package 
mvn azure-functions:run
```

> [!NOTE]
> 如果在使用 Java 9 时遇到 `javax.xml.bind.JAXBException` 异常，请参阅 [GitHub](https://github.com/jOOQ/jOOQ/issues/6477) 上的解决方法。

当函数在本地系统上运行并且做好响应 HTTP 请求的准备时，将显示以下输出：

```Output
Listening on http://0.0.0.0:7071/
Hit CTRL-C to exit...

Http Functions:

        HttpTrigger-Java: http://localhost:7071/api/HttpTrigger-Java
```

使用 curl 在新的终端窗口中从命令行触发函数：

```
curl -w '\n' -d LocalFunctionTest http://localhost:7071/api/HttpTrigger-Java
```

```Output
Hello, LocalFunctionTest
```

在终端中使用 `Ctrl-C` 停止函数代码。

## <a name="deploy-the-function-to-azure"></a>将函数部署到 Azure

部署到 Azure Functions 的过程中会使用 Azure CLI 中的帐户凭据。 在继续操作之前[使用 Azure CLI 登录](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)。

```azurecli
az login
```

使用 `azure-functions:deploy` Maven 目标将代码部署到新的函数应用。

```
mvn azure-functions:deploy
```

部署完成后，将显示可用于访问你的 Azure 函数应用的 URL：

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

使用 `cURL` 测试在 Azure 上运行的函数应用。 需更改以下示例中的 URL，使之与前一步骤中你自己的函数应用的已部署 URL 匹配。

```
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/HttpTrigger-Java
```

```Output
Hello, AzureFunctionsTest
```

## <a name="make-changes-and-redeploy"></a>进行更改并重新部署

编辑生成的项目中的 `src/main.../Function.java` 源文件来更改你的函数应用返回的文本。 更改以下行：

```java
return request.createResponse(200, "Hello, " + name);
```

更改为以下内容：

```java
return request.createResponse(200, "Hi, " + name);
```

保存更改，如以前一样通过从终端运行 `azure-functions:deploy` 进行重新部署。 函数应用将更新，并且以下请求：

```bash
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/HttpTrigger-Java
```

将具有更新的输出：

```Output
Hi, AzureFunctionsTest
```

## <a name="next-steps"></a>后续步骤

已使用简单的 HTTP 触发器创建了 Java 函数应用，并将其部署到了 Azure Functions。

- 有关开发 Java 函数的详细信息，请查看 [Java 函数开发人员指南](functions-reference-java.md)。
- 使用 `azure-functions:add` Maven 目标将具有不同触发器的其他函数添加到你的项目。
- 使用 [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)、[IntelliJ](functions-create-maven-intellij.md) 和 [Eclipse](functions-create-maven-eclipse.md) 在本地编写并调试函数。 
- 使用 Visual Studio Code 调试在 Azure 中部署的函数。 有关说明，请参阅 Visual Studio Code [无服务器 Java 应用程序](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)文档。
