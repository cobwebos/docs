---
title: 使用 Java 和 Eclipse 创建 Azure 函数应用 | Microsoft Docs
description: 介绍如何使用 Java 和 Eclipse 创建简单的 HTTP 触发式无服务器应用并将其发布到 Azure Functions 的操作说明指南。
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
ms.openlocfilehash: 373a35ea7b93c7717cd251e276be60b14df0920f
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2018
ms.locfileid: "39400160"
---
# <a name="create-your-first-function-with-java-and-eclipse-preview"></a>使用 Java 和 Eclipse 创建第一个函数（预览版）

> [!NOTE] 
> 用于 Azure Functions 的 Java 当前为预览版。

本文介绍如何使用 Eclipse IDE 和 Apache Maven 创建[无服务器](https://azure.microsoft.com/overview/serverless-computing/)函数项目、对其进行测试和调试，然后将其部署到 Azure Functions。 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>设置开发环境

若要使用 Java 和 Eclipse 开发函数应用，必须安装以下软件：

-  [Java 开发人员工具包](https://www.azul.com/downloads/zulu/)，版本 8。
-  [Apache Maven](https://maven.apache.org) 3.0 或更高版本。
-  支持 Java 和 Maven 的 [Eclipse](https://www.eclipse.org/downloads/packages/)。
-  [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> JAVA_HOME 环境变量必须设置为 JDK 的安装位置，以完成本快速入门。

我们强烈建议安装 [Azure Functions Core Tools 版本 2](functions-run-local.md#v2)，它为运行和调试 Azure Functions 提供本地环境。 

## <a name="create-a-functions-project"></a>创建 Functions 项目

1. 在 Eclipse 中，依次选择“文件”菜单、“项目”。 
1. 在“新建项目”窗口中打开“Java 项目”文件夹，然后依次选择“Maven 项目”、“下一步”。
1. 接受“新建 Maven 项目”对话框中的默认设置，然后选择“下一步”。
1. 选择“添加原型”，并为 [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype) 添加条目。
    - 原型组 ID：com.microsoft.azure
    - 原型项目 ID：azure-functions-archetype
    - 版本：使用[中心存储库](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)中的最新版本
    ![Eclipse Maven 创建](media/functions-create-first-java-eclipse/functions-create-eclipse.png)  
1. 单击“确定”并输入当前项目的详细信息，最后单击“完成”。

Maven 在新文件夹中创建名为 artifactId 的项目文件。 项目中生成的代码是一个简单的回显触发 HTTP 请求正文的 [HTTP 触发](/azure/azure-functions/functions-bindings-http-webhook)函数。

## <a name="run-functions-locally-in-the-ide"></a>在 IDE 本地运行函数

> [!NOTE]
> 必须安装 [Azure Functions Core Tools 版本 2](functions-run-local.md#v2)，才能在本地运行和调试函数。

1. 右键单击生成的项目，然后选择“运行方式”和“Maven 生成”。
1. 在“编辑配置”对话框中的“目标”和“名称”字段内输入 `package`，然后选择“运行”。 这会生成并打包函数代码。
1. 生成完成后，使用 `azure-functions:run` 作为目标和名称，如上所述创建另一个“运行”配置。 选择“运行”，在 IDE 中运行函数。

完成函数测试后，在控制台窗口中终止运行时。 一次只能有一个函数主机处于活动状态并在本地运行。

### <a name="debug-the-function-in-eclipse"></a>在 Eclipse 中调试函数

在上一步骤的“运行方式配置设置”中，将 `azure-functions:run` 更改为 `mvn azure-functions:run -DenableDebug` 并运行更新的配置，以便在调试模式下启动函数应用。

选择“运行”菜单并打开“调试配置”。 选择“远程 Java 应用程序”并创建新配置。 为配置命名，并填写设置。 端口应与函数主机打开的调试端口一致，默认为 `5005`。 设置后，单击 `Debug` 开始调试。

![在 Eclipse 中调试函数](media/functions-create-first-java-eclipse/debug-configuration-eclipse.PNG)

使用 IDE 设置断点并检查函数中的对象。 完成后，停止调试程序和正在运行的函数主机。 一次只能有一个函数主机处于活动状态并在本地运行。

## <a name="deploy-the-function-to-azure"></a>将函数部署到 Azure

部署到 Azure Functions 的过程中会使用 Azure CLI 中的帐户凭据。 在继续使用计算机的命令提示符之前，请[使用 Azure CLI 进行登录](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)。

```azurecli
az login
```

使用新“运行方式”配置中的 `azure-functions:deploy` Maven 目标，将代码部署到新的函数应用。

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
