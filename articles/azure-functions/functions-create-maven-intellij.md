---
title: 使用 Java 和 IntelliJ 创建 Azure 函数应用 | Microsoft Docs
description: 介绍如何使用 Java 和 IntelliJ 创建简单的 HTTP 触发式无服务器应用并将其发布到 Azure Functions 的操作说明指南。
services: functions
documentationcenter: na
author: jeffhollan
manager: jpconnock
keywords: azure functions, functions, 事件处理, 计算, 无服务器体系结构, java
ms.service: functions
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: e926bfb023fe3edfd564aa6389e21f6594bec169
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39117057"
---
# <a name="create-your-first-function-with-java-and-intellij-preview"></a>使用 Java 和 IntelliJ 创建你的第一个函数（预览版）

> [!NOTE] 
> 用于 Azure Functions 的 Java 当前为预览版。

本文介绍如何使用 IntelliJ IDEA 和 Apache Maven 创建[无服务器](https://azure.microsoft.com/overview/serverless-computing/)函数项目，在你自己的计算机上通过 IDE 对其进行测试和调试，并将其部署到 Azure Functions。 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>设置开发环境

若要使用 Java 和 IntelliJ 开发函数应用，必须安装以下软件：

-  [Java 开发人员工具包](https://www.azul.com/downloads/zulu/)，版本 8。
-  [Apache Maven](https://maven.apache.org) 3.0 或更高版本。
-  带有 Maven 工具的 [IntelliJ IDEA](https://www.jetbrains.com/idea/download) 社区版或旗舰版。
-  [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> JAVA_HOME 环境变量必须设置为 JDK 的安装位置，以完成本快速入门。

另强烈建议安装 [Azure Functions Core Tools 版本 2](functions-run-local.md#v2)，它为编写、运行和调试 Azure Functions 提供本地开发环境。 


## <a name="create-a-functions-project"></a>创建 Functions 项目

1. 在 IntelliJ IDEA 中单击“Create New Project”（创建新项目）。  
1. 选择通过“Maven”创建
1. 为 [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype) 选中“Create from archetype”（通过 archetype 创建）和“Add Archetype”（添加 Archetype）的复选框。
    - GroupId：com.microsoft.azure
    - ArtifactId：azure-functions-archetype
    - Version（版本）：使用[中央存储库](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    中的最新版本![IntelliJ Maven 创建](media/functions-create-first-java-intellij/functions-create-intellij.png)  
1. 单击“Next”（下一步）并输入当前项目的详细信息，最后单击“Finish”（完成）。

Maven 在新文件夹中创建名为 artifactId 的项目文件。 项目中生成的代码是一个简单的回显触发 HTTP 请求正文的 [HTTP 触发](/azure/azure-functions/functions-bindings-http-webhook)函数。

## <a name="run-functions-locally-in-the-ide"></a>在 IDE 本地运行函数

> [!NOTE]
> 必须安装 [Azure Functions Core Tools 版本 2](functions-run-local.md#v2)，才能在本地运行和调试函数。

1. 选择导入更改或确保启用[自动导入](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html)。
1. 打开“Maven 项目”工具栏
1. 在“生命周期”下，双击“打包”以打包和生成解决方案并创建目标目录。
1. 在“插件”->“azure-functions”下，双击“azure-functions:run”以启动 Azure Functions 本地运行时。  
  ![Azure Functions 的 Maven 工具栏](media/functions-create-first-java-intellij/functions-intellij-java-maven-toolbar.png)  

完成函数测试后关闭运行对话框。 一次只能有一个函数主机处于活动状态并在本地运行。

### <a name="debug-the-function-in-intellij"></a>在 IntelliJ 中调试函数

可以在 IntelliJ 中调试函数，方法是在启动后将函数附加到函数主机。  使用上述步骤在本地运行 Azure Function，然后在“Run”（运行）菜单中选择“Attach to local process”（附加到本地进程）。  端口 5005 上应该会显示一个可用进程。  附加后，可以在函数应用内命中并调试断点。

完成后，停止调试器和正在运行的进程。 一次只能有一个函数主机处于活动状态并在本地运行。

## <a name="deploy-the-function-to-azure"></a>将函数部署到 Azure

部署到 Azure Functions 的过程中会使用 Azure CLI 中的帐户凭据。 在继续使用计算机的命令提示符之前，请[使用 Azure CLI 进行登录](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)。

```azurecli
az login
```

使用 `azure-functions:deploy` Maven 目标将代码部署到新的函数应用中，或者在“Maven 项目”窗口中选择 azure-functions:deploy 选项。

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

## <a name="next-steps"></a>后续步骤

- 有关开发 Java 函数的详细信息，请查看 [Java 函数开发人员指南](functions-reference-java.md)。
- 使用 `azure-functions:add` Maven 目标将具有不同触发器的其他函数添加到你的项目。
