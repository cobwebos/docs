---
title: 使用 Jenkins Azure Functions 插件部署到 Azure Functions
description: 了解如何使用 Jenkins Azure Functions 插件部署到 Azure Functions
ms.service: jenkins
keywords: jenkins, azure, devops, java, azure functions
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 02/23/2019
ms.openlocfilehash: 1138af0e073f68842861df86acd4d9d6eb467782
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2019
ms.locfileid: "56824706"
---
# <a name="deploy-to-azure-functions-using-the-jenkins-azure-functions-plugin"></a>使用 Jenkins Azure Functions 插件部署到 Azure Functions

[Azure Functions](/azure/azure-functions/) 是一个无服务器计算服务。 使用 Azure Functions 可以按需运行代码，而无需预配或管理基础结构。 本教程演示如何使用 Azure Functions 插件将 Java 函数部署到 Azure Functions。

## <a name="prerequisites"></a>先决条件

- **Azure 订阅**：如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- **Jenkins 服务器**：如果未安装 Jenkins 服务器，请参阅文章[在 Azure 上创建 Jenkins 服务器](./install-jenkins-solution-template.md)。

 > [!TIP]
 > 本教程中使用的源代码位于 [Visual Studio China GitHub 存储库](https://github.com/VSChina/odd-or-even-function/blob/master/src/main/java/com/microsoft/azure/Function.java)。

## <a name="create-a-java-function"></a>创建 Java 函数

若要使用 Java 运行时堆栈创建 Java 函数，请使用 [Azure 门户](https://portal.azure.com)或 [Azure CLI](/cli/azure/?view=azure-cli-latest)。

以下步骤演示如何使用 Azure CLI 创建 Java 函数：

1. 创建资源组，将 &lt;resource_group> 占位符替换资源组名称。

    ```cli
    az group create --name <resource_group> --location eastus
    ```

1. 创建 Azure 存储帐户，将占位符替换为适当的值。
 
    ```cli
    az storage account create --name <storage_account> --location eastus --resource-group <resource_group> --sku Standard_LRS    
    ```

1. 创建测试函数应用，将占位符替换为适当的值。

    ```cli
    az functionapp create --resource-group <resource_group> --consumption-plan-location eastus --name <function_app> --storage-account <storage_account>
    ```
    
1. 更新到版本 2.x，将占位符替换为适当的值。

    ```cli
    az functionapp config appsettings set --name <function_app> --resource-group <resource_group> --settings FUNCTIONS_EXTENSION_VERSION=~2
    ```

## <a name="prepare-jenkins-server"></a>准备 Jenkins 服务器

以下步骤说明如何准备 Jenkins 服务器：

1. 在 Azure 上部署 [Jenkins 服务器](https://aka.ms/jenkins-on-azure)。 如果尚未安装 Jenkins 服务器的实例，则文章[在 Azure 上创建 Jenkins 服务器](./install-jenkins-solution-template.md)会指导你完成该过程。

1. 使用 SSH 登录 Jenkins 实例。

1. 在 Jenkins 实例中，使用以下命令安装 maven：

    ```terminal
    sudo apt install -y maven
    ```

1. 在 Jenkins 实例上，通过在终端提示处发出以下命令来安装 [Azure Functions Core Tools](/azure/azure-functions/functions-run-local)：

    ```terminal
    wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools
    ```

1. 在 Jenkins 仪表板中，安装以下插件：

    - Azure Function 插件
    - EnvInject 插件

1. Jenkins 需要 Azure 服务主体以进行身份验证和访问 Azure 资源。 有关分步说明，请参阅[部署到 Azure 应用服务](./tutorial-jenkins-deploy-web-app-azure-app-service.md)。

1. 使用 Azure 服务主体在 Jenkins 中添加“Microsoft Azure 服务主体”凭据类型。 请参阅[部署到 Azure 应用服务](./tutorial-jenkins-deploy-web-app-azure-app-service.md#add-service-principal-to-jenkins)教程。

## <a name="create-a-jenkins-pipeline"></a>创建 Jenkins 管道

在本部分中，将创建 [Jenkins 管道](https://jenkins.io/doc/book/pipeline/)。

1. 在 Jenkins 仪表板中，创建一个管道。

1. 启用“为运行准备环境”。

1. 在“内容属性”中添加以下环境变量，将占位符替换为环境的相应值：

    ```
    AZURE_CRED_ID=<service_principal_credential_id>
    RES_GROUP=<resource_group>
    FUNCTION_NAME=<function_name>
    ```
    
1. 在“管道”->“定义”部分中，选择“来自 SCM 的管道脚本”。

1. 使用提供的[脚本示例](https://github.com/VSChina/odd-or-even-function/blob/master/doc/resources/jenkins/JenkinsFile)输入 SCM 存储库 URL 和脚本路径。

## <a name="build-and-deploy"></a>生成并部署

现在可以运行 Jenkins 作业。

1. 首先，通过 [Azure Functions HTTP 触发器和绑定](/azure/azure-functions/functions-bindings-http-webhook#authorization-keys)一文中的说明获取授权密钥。

1. 在浏览器中，输入应用的 URL。 将占位符替换为适当的值并为 &lt;input_number> 指定数值作为 Java 函数的输入。

    ```
    https://<function_app>.azurewebsites.net/api/HttpTrigger-Java?code=<authorization_key>&number=<input_number>
    ```
1. 你会看到类似于以下示例输出的结果（在其中将奇数 365 用作测试）：

    ```output
    The number 365 is Odd.
    ```

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用程序，请按以下步骤删除创建的资源：

```cli
az group delete -y --no-wait -n <resource_group>
```

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure Functions 的详细信息，请参阅以下资源：
> [!div class="nextstepaction"]
> [Azure Functions 文档](/azure/azure-functions/)