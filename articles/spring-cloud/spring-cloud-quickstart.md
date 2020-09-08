---
title: 快速入门 - 部署第一个 Azure Spring Cloud 应用程序
description: 在本快速入门中，我们需要将 Spring Cloud 应用程序部署到 Azure Spring Cloud。
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 245516e0a54865d3a6097c4bb566b850cb738ad6
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89260539"
---
# <a name="quickstart-deploy-your-first-azure-spring-cloud-application"></a>快速入门：部署第一个 Azure Spring Cloud 应用程序

本快速入门介绍如何部署简单的 Azure Spring Cloud 微服务应用程序，以便在 Azure 上运行。 

本教程中使用的应用程序代码是使用 Spring Initializr 生成的简单应用。 完成此示例后，你可以在线访问应用程序，并可通过 Azure 门户对其进行管理。

本快速入门介绍如何：

> [!div class="checklist"]
> * 生成基本 Spring Cloud 项目
> * 预配服务实例
> * 使用公共终结点生成并部署应用
> * 实时流式传输日志

## <a name="prerequisites"></a>先决条件

完成本快速入门教程需要：

* [安装 JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [注册 Azure 订阅](https://azure.microsoft.com/free/)
* （可选）[安装 Azure CLI 版本 2.0.67 或更高版本](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)，并使用以下命令安装 Azure Spring Cloud 扩展：`az extension add --name spring-cloud`
* （可选）[安装 Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) 并[登录](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="generate-a-spring-cloud-project"></a>生成 Spring Cloud 项目

首先，使用 [Spring Initializr](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.3.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin) 生成一个包含推荐的 Azure Spring Cloud 依赖项的示例项目。 下图显示了为此示例项目设置的 Initializr。
```url
https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.3.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin
```

  ![Initializr 页](media/spring-cloud-quickstart-java/initializr-page.png)

1. 设置好所有依赖项后，单击“生成”。 下载包并将其解压缩，然后按照如下所示添加 `src/main/java/com/example/hellospring/HelloController.java`，以便为简单的 Web 应用程序创建 Web 控制器：

    ```java
    package com.example.hellospring;
    
    import org.springframework.web.bind.annotation.RestController;
    import org.springframework.web.bind.annotation.RequestMapping;
    
    @RestController
    public class HelloController {
    
        @RequestMapping("/")
        public String index() {
            return "Greetings from Azure Spring Cloud!";
        }
    
    }
    ```
## <a name="provision-an-instance-of-azure-spring-cloud"></a>预配 Azure Spring Cloud 的实例

以下步骤使用 Azure 门户创建 Azure Spring Cloud 的实例。

1. 在新选项卡中，打开 [Azure 门户](https://ms.portal.azure.com/)。 

2. 在顶部搜索框中，搜索“Azure Spring Cloud”。

3. 从结果中选择“Azure Spring Cloud”。

    ![ASC 图标 - 启动](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. 在“Azure Spring Cloud”页上，单击“+ 添加”。

    ![ASC 图标 - 添加](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. 在 Azure Spring Cloud“创建”页中填写表单。  遵循以下指南：
    - 订阅：选择要在其中收取此资源费用的订阅。
    - 资源组：最佳做法是为新资源创建新的资源组。 后面的步骤会将其用作 \<resource group name\>。
    - **服务详细信息/名称**：指定 \<service instance name\>。  该名称必须为 4 到 32 个字符，只能包含小写字母、数字及连字符。  服务名称的第一个字符必须是字母，最后一个字符必须是字母或数字。
    - 位置：为服务实例选择区域。

    ![ASC 门户启动](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. 单击“审阅并创建”。

## <a name="build-and-deploy-the-app"></a>生成并部署应用
    
#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
以下步骤使用 Azure CLI 生成并部署应用程序。 在项目的根目录中，执行以下命令。

1. 使用 Maven 生成项目：

    ```console
    mvn clean package -DskipTests
    ```

1. （如果尚未安装）安装适用于 Azure CLI 的 Azure Spring Cloud 扩展：

    ```azurecli
    az extension add --name spring-cloud
    ```
    
1. 创建已分配公共终结点的应用：

    ```azurecli
    az spring-cloud app create -n hellospring -s <service instance name> -g <resource group name> --is-public
    ```

1. 为应用部署 Jar 文件：

    ```azurecli
    az spring-cloud app deploy -n hellospring -s <service instance name> -g <resource group name> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar
    ```
    
1. 完成应用程序部署需要几分钟时间。 若要确认它是否已经部署，请在 Azure 门户中转到“应用”边栏选项卡。 你应该会看到应用程序的状态。

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

以下步骤使用适用于 Azure Spring Cloud 的 IntelliJ 插件在 IntelliJ IDEA 中部署示例应用。  

### <a name="import-project"></a>导入项目

1. 打开 IntelliJ 的“欢迎”对话框，选择“导入项目”以打开导入向导 。
1. 选择 `hellospring` 文件夹。

    ![导入项目](media/spring-cloud-quickstart-java/intellij-new-project.png)

### <a name="deploy-the-app"></a>部署应用
若要部署到 Azure，必须使用你的 Azure 帐户登录，然后选择你的订阅。  有关登录的详细信息，请参阅[安装和登录](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)。

1. 在 IntelliJ 项目资源管理器中右键单击你的项目，选择“Azure” -> “部署到 Azure Spring Cloud”。 

    [ ![部署到 Azure 1](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png) ](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png#lightbox)

1. 在“名称”字段中接受应用的名称。 “名称”是指配置，不是应用名称。 用户通常不需更改它。
1. 在“项目”文本框中，选择 hellospring-0.0.1-SNAPSHOT.jar。
1. 在“订阅”文本框中，验证订阅。
1. 在“Spring Cloud”文本框中，选择你在[预配 Azure Spring Cloud 实例](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-provision-service-instance)中创建的 Azure Spring Cloud 实例。
1. 将“公共终结点”设置为“启用”。
1. 在“应用:”文本框中，选择“创建应用…” 。
1. 输入“hellospring”，然后单击“确定”。

    [ ![部署到 Azure - 确定](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png) ](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png#lightbox)

1. 单击“部署 Azure Spring Cloud 应用”对话框底部的“运行”按钮，以开始部署 。 此插件会对 `hellospring` 应用运行命令 `mvn package`，然后部署由 `package` 命令生成的 jar。
---

完成部署后，就可以通过 `https://<service instance name>-hellospring.azuremicroservices.io/` 访问应用。

  [ ![从浏览器访问应用](media/spring-cloud-quickstart-java/access-app-browser.png) ](media/spring-cloud-quickstart-java/access-app-browser.png#lightbox)

## <a name="streaming-logs-in-real-time"></a>实时流式处理日志

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

使用以下命令从应用获取实时日志。

```azurecli
az spring-cloud app logs -n hellospring -s <service instance name> -g <resource group name> --lines 100 -f

```
日志随即出现在结果中：

[ ![流式处理日志](media/spring-cloud-quickstart-java/streaming-logs.png) ](media/spring-cloud-quickstart-java/streaming-logs.png#lightbox)

>[!TIP]
> 使用 `az spring-cloud app logs -h` 浏览更多参数和日志流功能。

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

1. 选择“Azure 资源管理器”，然后选择“Spring Cloud”。
1. 右键单击正在运行的应用。
1. 从下拉列表中选择“流式处理日志”。
1. 选择实例。

    [ ![选择流式处理日志](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png) ](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png)

1. 流式处理日志将显示在输出窗口中。

    [ ![流式处理日志输出](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png) ](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png)
---

如需查看高级日志分析功能，请访问 [Azure 门户](https://portal.azure.com/)菜单中的“日志”选项卡。 这里日志会延迟几分钟。

[ ![日志分析](media/spring-cloud-quickstart-java/logs-analytics.png) ](media/spring-cloud-quickstart-java/logs-analytics.png#lightbox)

## <a name="clean-up-resources"></a>清理资源
在前面的步骤中，你在资源组中创建了 Azure 资源。 如果认为将来不需要这些资源，请通过门户删除资源组，或在 Cloud Shell 中运行以下命令来这样做：
```azurecli
az group delete --name <your resource group name; for example: hellospring-1558400876966-rg> --yes
```

在此快速入门中，读者学习了如何：

> [!div class="checklist"]
> * 生成基本 Azure Spring Cloud 项目
> * 预配服务实例
> * 使用公共终结点生成并部署应用
> * 实时流式处理日志
## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [生成并运行微服务](spring-cloud-quickstart-sample-app-introduction.md)

GitHub 中提供了更多示例：[Azure Spring Cloud 示例](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql)。
