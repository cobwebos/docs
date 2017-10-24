---
title: "在 Azure 中通过 Java 和 Maven 创建你的第一个函数 | Microsoft Docs"
description: "通过 Java 和 Maven 创建一个简单的 HTTP 触发函数，并将其发布到 Azure。"
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: "azure functions, functions, 事件处理, 计算, 无服务器体系结构"
ms.service: functions
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/03/2017
ms.author: routlaw, glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: dba73992c22dced8574e9d98de15a7b5713c5b46
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-function-with-java-and-maven-preview"></a>通过 Java 和 Maven 创建你的第一个函数（预览版）

本快速入门可指导通过 Maven 创建无服务器的函数项目，在本地对其进行测试，并将其部署到 Azure Functions。 完成后，可在 Azure 中运行 HTTP 触发的函数应用。

 ![通过 cURL 从命令行中访问 Hello World 函数](media/functions-create-java-maven/hello-azure.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件
若要通过 Java 开发函数应用，必须安装以下软件：

-  最新版本的 [.NET Core](https://www.microsoft.com/net/core)。
-  [Java 开发人员工具包](https://www.azul.com/downloads/zulu/) 1.8 版。
-  [Azure CLI](https://docs.microsoft.com/cli/azure)
-  [Apache Maven](https://maven.apache.org) 3.0 或更高版本。
-  [Node.js](https://nodejs.org/download/) 8.6 或更高版本。

> [!IMPORTANT] 
> JAVA_HOME 环境变量必须设置为 JDK 的安装位置，以完成本快速入门。

## <a name="install-the-azure-functions-core-tools"></a>安装 Azure Functions Core Tools

[Azure Functions 核心工具 2.0](https://www.npmjs.com/package/azure-functions-core-tools) 为编写、运行和调试 Azure Functions 提供了本地开发环境。 将此工具与 [npm](https://www.npmjs.com/) 一起安装，包括 [Node.js](https://nodejs.org/)。

```
npm install -g azure-functions-core-tools@core
```

> [!NOTE]
> 如果在安装 Azure Functions 核心工具版本 2.0 时遇到问题，请参阅[版本 2.x 运行时](/azure/azure-functions/functions-run-local#version-2x-runtime)。

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

Maven 会提示你提供所需的值以完成项目的生成。 有关 groupId、artifactId 和 version 值，请参阅 [Maven 命名约定](https://maven.apache.org/guides/mini/guide-naming-conventions.html)参考。 AppName 值在 Azure 中必须唯一，以便 Maven 基于以前输入的 artifactId 生成默认应用名称。 PackageName 值确定所生成函数代码的 Java 包。

```Output
Define value for property 'groupId': com.fabrikam.functions
Define value for property 'artifactId' : fabrikam-functions
Define value for property 'version' 1.0-SNAPSHOT : 
Define value for property 'package': com.fabrikam.functions
Define value for property 'appName' fabrikam-functions-20170927220323382:
Confirm properties configuration: Y
```

Maven 在新文件夹中创建名为 artifactId 的项目文件。 项目中生成的代码是一个简单的回显请求正文的 [HTTP 触发](/azure/azure-functions/functions-bindings-http-webhook)函数：

```java
public class Function {
    @FunctionName("hello")
    public String hello(@HttpTrigger(name = "req", methods = {"get", "post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req,
                        ExecutionContext context) {
        return String.format("Hello, %s!", req);
    }
}
```

## <a name="run-the-function-locally"></a>在本地运行函数

将目录更改为新创建的项目文件夹，并通过 Maven 生成和运行此函数：

```
cd fabrikam-function
mvn clean package 
mvn azure-functions:run
```

运行函数时，将显示以下输出：

```Output
Listening on http://localhost:7071
Hit CTRL-C to exit...

Http Functions:

   hello: http://localhost:7071/api/hello
```

使用 curl 在新的终端中从命令行触发函数：

```
curl -w '\n' -d LocalFunction http://localhost:7071/api/hello
```

```Output
Hello LocalFunction!
```

在终端中使用 `Ctrl-C` 停止函数代码。

## <a name="deploy-the-function-to-azure"></a>将函数部署到 Azure

部署到 Azure Functions 的过程中会使用 Azure CLI 中的帐户凭据。 [使用 Azure CLI 登录](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)，然后使用 `azure-functions:deploy` Maven 目标将代码部署到新的函数应用。

```
az login
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

使用 curl 测试在 Azure 上运行的函数应用：

```
curl -w '\n' https://fabrikam-function-20170920120101928.azurewebsites.net/api/hello -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="next-steps"></a>后续步骤

你已使用简单的 HTTP 触发器创建 Java 函数应用，并将其部署到 Azure Functions。

- 有关开发 Java 函数的详细信息，请查看 [Java 函数开发人员指南](functions-reference-java.md)。
- 使用 `azure-functions:add` Maven 目标将具有不同触发器的其他函数添加到你的项目。
- 使用 Visual Studio Code 在本地调试函数。 安装 [Java 扩展包](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)且在 Visual Studio Code 中打开 Functions 项目后，[将调试器附加](https://code.visualstudio.com/Docs/editor/debugging#_launch-configurations)到端口 5005。 然后，在编辑器中设置断点，并在本地运行函数时触发函数：![在 Visual Studio Code 中调试函数](media/functions-create-java-maven/vscode-debug.png)



