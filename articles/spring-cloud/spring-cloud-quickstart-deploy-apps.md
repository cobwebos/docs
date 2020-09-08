---
title: 快速入门 - 生成应用并将其部署到 Azure Spring Cloud
description: 介绍如何将应用部署到 Azure Spring Cloud。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: 8931c22c3656cf9708756153268ab1d9d87b8343
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050822"
---
# <a name="quickstart-build-and-deploy-apps-to-azure-spring-cloud"></a>快速入门：生成应用并将其部署到 Azure Spring Cloud

本文档说明如何使用以下内容生成微服务应用程序并将其部署到 Azure Spring Cloud：
* Azure CLI
* Maven 插件
* Intellij

在使用 Azure CLI 或 Maven 进行部署之前，请完成[预配 Azure Spring Cloud 实例](spring-cloud-quickstart-provision-service-instance.md)和[设置配置服务器](spring-cloud-quickstart-setup-config-server.md)的示例。

## <a name="prerequisites"></a>先决条件

* [安装 JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [注册 Azure 订阅](https://azure.microsoft.com/free/)
* （可选）[安装 Azure CLI 版本 2.0.67 或更高版本](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)，并使用以下命令安装 Azure Spring Cloud 扩展：`az extension add --name spring-cloud`
* （可选）[安装 Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) 并[登录](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="deployment-procedures"></a>部署过程

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

### <a name="build-the-microservices-applications-locally"></a>在本地构建微服务应用程序

1. 将示例应用存储库克隆到你的 Azure 云帐户。  

    ```azurecli
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. 更改目录并生成项目。

    ```azurecli
    cd piggymetrics
    mvn clean package -DskipTests
    ```

编译项目大约需要 5 分钟时间。 完成后，相应的文件夹中应会包含每个服务的各个 JAR 文件。

### <a name="create-and-deploy-the-apps"></a>创建和部署应用

1. 使用以下命令设置默认的资源组名称和群集名称：

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

1. 使用上一步骤中生成的 JAR 文件创建 Azure Spring Cloud 微服务。 将创建三个应用：gateway、auth-service 和 account-service  。

    ```azurecli
    az spring-cloud app create --name gateway
    az spring-cloud app create --name auth-service
    az spring-cloud app create --name account-service
    ```

1. 我们需要将在上一步中创建的应用程序部署到 Azure。 使用以下命令部署所有三个应用程序：

    ```azurecli
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

### <a name="assign-public-endpoint-to-gateway"></a>将公共终结点分配到网关

我们需要在 Web 浏览器通过某种方式来访问该应用程序。 网关应用程序需要一个面向公众的终结点。

1. 使用以下命令分配终结点：

    ```azurecli
    az spring-cloud app update -n gateway --is-public true
    ```

2. 查询**网关**应用程序的公共 IP，这样就可以验证该应用程序是否正在运行：

    ```azurecli
    az spring-cloud app show --name gateway --query properties.url
    ```

#### <a name="maven"></a>[Maven](#tab/Maven)

### <a name="clone-and-build-the-sample-application-repository"></a>克隆并构建示例应用程序存储库

1. 通过运行以下命令，克隆 Git 存储库：

    ```
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. 通过运行以下命令更改目录并生成项目：

    ```
    cd piggymetrics
    mvn clean package -DskipTests
    ```

### <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>生成配置并将其部署到 Azure Spring Cloud

1. 通过在包含父 POM 的 PiggyMetrics 的根文件夹中运行以下命令来生成配置。 如果已使用 Azure CLI 登录，则该命令将自动提取凭据。 否则，它将提示你登录。 有关更多详细信息，请参阅我们的 [wiki 页面](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication)。

    ```
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.1.0:config
    ```
    
    系统会提示你选择：
    * 模块：选择 `gateway`、`auth-service` 和 `account-service`。
    * **订阅：** 这是用于创建 Azure Spring Cloud 实例的订阅。
    * **服务实例：** 这是 Azure Spring Cloud 实例的名称。
    * **公共终结点：** 在提供的项目列表中，输入与 `gateway` 对应的数字。  这使其具有公共访问权限。

1. POM 现包含插件依赖项和配置。 使用以下命令部署应用。 

    ```
    mvn azure-spring-cloud:deploy
    ```

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

### <a name="import-sample-project-in-intellij"></a>导入 IntelliJ 中的示例项目

1. 下载本教程的源存储库并将其解压缩，或使用 Git: `git clone https://github.com/Azure-Samples/piggymetrics` 克隆它 

1. 打开 IntelliJ 的“欢迎”对话框，选择“导入项目”以打开导入向导 。

1. 选择 `piggymetric` 文件夹。

    ![导入项目](media/spring-cloud-intellij-howto/revision-import-project-1.png)

### <a name="deploy-gateway-app-to-azure-spring-cloud"></a>将网关应用部署到 Azure Spring Cloud
若要部署到 Azure，必须通过 Azure Toolkit for IntelliJ 使用 Azure 帐户登录，然后选择你的订阅。 有关登录的详细信息，请参阅[安装和登录](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)。

1. 在 IntelliJ 项目资源管理器中右键单击你的项目，选择“Azure” -> “部署到 Azure Spring Cloud”。 

    ![部署到 Azure 1](media/spring-cloud-intellij-howto/revision-deploy-to-azure-1.png)

1. 在“名称”字段中，将“:gateway”追加到现有“名称”指示配置。
1. 在“项目”文本框中，选择“com.piggymetrics:gateway:1.0-SNAPSHOT”。
1. 在“订阅”文本框中，验证订阅。
1. 在“Spring Cloud”文本框中，选择你在[预配 Azure Spring Cloud 实例](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-provision-service-instance)中创建的 Azure Spring Cloud 实例。
1. 将“公共终结点”设置为“启用”。
1. 在“应用:”文本框中，选择“创建应用…” 。
1. 输入“网关”，然后单击“确定”。

    ![部署到 Azure - 确定](media/spring-cloud-intellij-howto/revision-deploy-to-azure-2.png)

1. 在对话框的“启动之前”部分中，双击“运行 Maven 目标”。
1. 在“工作目录”文本框中，导航到“piggymetrics/gateway”文件夹。
1. 在“命令行”文本框中，输入“package -DskipTests”。 单击 **“确定”** 。
1. 单击“部署 Azure Spring Cloud 应用”对话框底部的“运行”按钮，以开始部署 。 此插件会对 `gateway` 应用运行命令 `mvn package`，然后部署由 `package` 命令生成的 jar。

### <a name="deploy-auth-service-and-account-service-apps-to-azure-spring-cloud"></a>将 auth-service 和 account-service 应用部署到 Azure Spring Cloud
可以重复上述步骤，将 `auth-service` 和 `account-service` 应用部署到 Azure Spring Cloud：

1. 修改“名称”和“项目”以标识 `auth-service` 应用 。
1. 在“应用:”文本框中，选择“创建应用…”以创建 `auth-service` 应用 。
1. 验证“公用终结点”选项是否设置为“禁用”。
1. 在对话框的“启动之前”部分中，将“工作目录”切换到“piggymetrics/auth-service”文件夹 。
1. 单击“部署 Azure Spring Cloud 应用”对话框底部的“运行”按钮，以开始部署 。 
1. 重复上述步骤来配置和部署 `account-service`。
---

导航到前面步骤的输出中提供的 URL，以访问 PiggyMetrics 应用程序。 例如，`https://<service instance name>-gateway.azuremicroservices.io`

![访问 PiggyMetrics](media/spring-cloud-quickstart-launch-app-cli/launch-app.png)

还可以导航 Azure 门户以查找 URL。 
1. 导航到服务
2. 选择“应用”
3. 选择“网关”

    ![导航应用](media/spring-cloud-quickstart-launch-app-cli/navigate-app1.png)
    
4. 在“网关 | 概述”页上查找 URL

    ![第二个导航应用](media/spring-cloud-quickstart-launch-app-cli/navigate-app2-url.png)

## <a name="clean-up-resources"></a>清理资源
在前面的步骤中，你在资源组中创建了 Azure 资源。 如果认为将来不需要这些资源，请通过门户删除资源组，或在 Cloud Shell 中运行以下命令来这样做：
```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```
在前面的步骤中，还设置了默认资源组名称。 若要清除该默认设置，请在 Cloud Shell 中运行以下命令：
```azurecli
az configure --defaults group=
```
## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [日志、指标和跟踪](spring-cloud-quickstart-logs-metrics-tracing.md)
