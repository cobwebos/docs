---
title: 使用 Java 和 IntelliJ 创建 Azure 函数
description: 了解如何使用 Java 和 IntelliJ 创建简单的 HTTP 触发式无服务器应用并将其发布到 Azure。
author: jeffhollan
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: 46044d4c6dd65944291aff157229be0abc60d3d7
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "77913236"
---
# <a name="create-your-first-azure-function-with-java-and-intellij"></a>通过 Java 和 IntelliJ 创建第一个 Azure 函数

本文将介绍以下内容：
- 如何使用 IntelliJ IDEA 和 Apache Maven 创建[无服务器](https://azure.microsoft.com/overview/serverless-computing/)函数项目
- 在个人计算机上的集成开发环境 (IDE) 中测试和调试函数的步骤
- 关于将函数项目部署至 Azure Functions 的说明

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>设置开发环境

若要使用 Java 和 IntelliJ 开发函数，请安装以下软件：

- [Java 开发人员工具包](https://www.azul.com/downloads/zulu/) (JDK) 版本 8
- [Apache Maven](https://maven.apache.org) 3.0 或更高版本
- 带有 Maven 的 [IntelliJ IDEA](https://www.jetbrains.com/idea/download) 社区版或旗舰版
- [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT]
> JAVA_HOME 环境变量必须设置为 JDK 的安装位置，以完成本文中的步骤。

 我们建议安装 [Azure Functions Core Tools 版本 2](functions-run-local.md#v2)。 该工具为编写、运行和调试 Azure Functions 提供了本地开发环境。

## <a name="create-a-functions-project"></a>创建 Functions 项目

1. 在 IntelliJ IDEA 中选择“新建项目”。  
1. 在“新建项目”窗口中选择左窗格中的“Maven”。
1. 为 [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype) 选中“通过 archetype 创建”复选框，然后选择“添加 Archetype”。
1. 在“添加 Archetype”窗口中按以下说明填写各个字段：
    - _GroupId_：com.microsoft.azure
    - _ArtifactId_：azure-functions-archetype
    - _版本_：使用[中央存储库](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)中的最新**1.22**版
    ![在 IntelliJ 中从原型创建 Maven 项目](media/functions-create-first-java-intellij/functions-create-intellij.png)  
1. 选择“确定”，然后选择“下一步”。
1. 输入当前项目的详细信息，并选择“完成”。

Maven 在新文件夹中创建项目文件，文件名称与 _ArtifactId_ 值相同。 项目中生成的代码是一个简单的回显触发 HTTP 请求正文的 [HTTP 触发](/azure/azure-functions/functions-bindings-http-webhook)函数。

## <a name="run-functions-locally-in-the-ide"></a>在 IDE 本地运行函数

> [!NOTE]
> 若要在本地运行和调试函数，请确保安装 [Azure Functions Core Tools 版本 2](functions-run-local.md#v2)。

1. 手动导入更改或启用[自动导入](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html)。
1. 打开“Maven 项目”工具栏。
1. 展开“生命周期”然后打开“包”。 该解决方案是在新创建的目标目录中生成和打包的。
1. 展开“插件” > “azure-functions”，并打开“azure-functions:run”以启动 Azure Functions 本地运行时。  
  ![Azure Functions 的 Maven 工具栏](media/functions-create-first-java-intellij/functions-intellij-java-maven-toolbar.png)  

1. 完成函数测试后关闭运行对话框。 一次只能有一个函数主机处于活动状态并在本地运行。

## <a name="debug-the-function-in-intellij"></a>在 IntelliJ 中调试函数

1. 若要在调试模式下启动函数主机，请在运行函数时添加 **-DenableDebug** 作为参数。 可以更改 [Maven 目标](https://www.jetbrains.com/help/idea/maven-support.html#run_goal)中的配置或在终端窗口中运行以下命令：  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   此命令会让函数主机打开调试端口 5005。

1. 请在“运行”菜单中选择“编辑配置”。
1. 选择“(+)”添加“远程”。
1. 填写“名称”和“设置”字段，然后选择“确定”以保存配置。
1. 在设置后，选择“调试<远程配置名称>”或在键盘上按 Shift+F9 以启动调试。

1. 完成后，请停止调试器和正在运行的进程。 一次只能有一个函数主机处于活动状态并在本地运行。

## <a name="deploy-the-function-to-azure"></a>将函数部署到 Azure

1. 在将函数部署到 Azure 之前，必须[使用 Azure CLI 登录](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)。

   ``` azurecli
   az login
   ```

1. 使用 `azure-functions:deploy` Maven 目标将代码部署到新的函数。 也可以在“Maven 项目”窗口中选择“azure-functions:deploy”选项。

   ```
   mvn azure-functions:deploy
   ```

1. 成功部署函数后，在 Azure CLI 输出中找到函数的 URL。

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>后续步骤

- 有关开发 Java 函数的详细信息，请查看 [Java 函数开发人员指南](functions-reference-java.md)。
- 使用 `azure-functions:add` Maven 目标将具有不同触发器的其他函数添加到你的项目。
