---
title: 快速入门 - 生成应用并将其部署到 Azure Spring Cloud
description: 介绍如何将应用部署到 Azure Spring Cloud。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: d368c938e805164b70c7d3a22f9229b5efd89dde
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92045391"
---
# <a name="quickstart-build-and-deploy-apps-to-azure-spring-cloud"></a>快速入门：生成应用并将其部署到 Azure Spring Cloud

::: zone pivot="programming-language-csharp"
在本快速入门中，你会使用 Azure CLI 生成微服务应用程序并将其部署到 Azure Spring Cloud。

## <a name="prerequisites"></a>必备知识

* 完成此系列中的前一个快速入门：

  * [预配 Azure Spring Cloud 服务](spring-cloud-quickstart-provision-service-instance.md)。
  * [设置 Azure Spring Cloud 配置服务器](spring-cloud-quickstart-setup-config-server.md)。

## <a name="download-the-sample-app"></a>下载示例应用

如果你到目前为止一直在使用 Azure Cloud Shell，请切换到本地命令提示符以执行以下步骤。

1. 创建一个新文件夹，并克隆示例应用存储库。

   ```console
   mkdir source-code
   ```

   ```console
   cd source-code
   ```

   ```console
   git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples
   ```

1. 导航到存储库目录中。

   ```console
   cd Azure-Spring-Cloud-Samples
   ```

## <a name="deploy-planetweatherprovider"></a>部署 PlanetWeatherProvider

1. 在 Azure Spring Cloud 实例中为 PlanetWeatherProvider 项目创建应用。

   ```azurecli
   az spring-cloud app create --name planet-weather-provider --runtime-version NetCore_31
   ```

   为了启用自动服务注册，为应用提供了与项目 appsettings.json 文件中的 `spring.application.name` 值相同的名称：

   ```json
   "spring": {
     "application": {
       "name": "planet-weather-provider"
     }
   }
   ```

   此命令可能需要几分钟才能运行。

1. 将目录切换到 `PlanetWeatherProvider` 项目文件夹。

   ```console
   cd steeltoe-sample/src/planet-weather-provider
   ```

1. 创建要部署的二进制文件和 .zip 文件。

   ```console
   dotnet publish -c release -o ./publish
   ```

   > [!TIP]
   > 项目文件包含以下 XML，以便在将二进制文件写入 ./publish 文件夹之后，将它们打包为 .zip 文件 ：
   >
   > ```xml
   > <Target Name="Publish-Zip" AfterTargets="Publish">
   >   <ZipDirectory SourceDirectory="$(PublishDir)" DestinationFile="$(MSBuildProjectDirectory)/publish-deploy-planet.zip" Overwrite="true" />
   > </Target>
   > ```

1. 部署到 Azure。

   运行以下命令之前，请确保命令提示符处于项目文件夹中。

   ```console
   az spring-cloud app deploy -n planet-weather-provider --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll --artifact-path ./publish-deploy-planet.zip
   ```

   `--main-entry` 选项指定从 .zip 文件的根文件夹到包含应用程序入口点的 .dll 文件的相对路径。 服务上传 .zip 文件之后，它会提取所有文件和文件夹，并尝试执行指定 .dll 文件中的入口点。

   此命令可能需要几分钟才能运行。

## <a name="deploy-solarsystemweather"></a>部署 SolarSystemWeather

1. 这次在 Azure Spring Cloud 实例中为 SolarSystemWeather 项目创建另一个应用：

   ```azurecli
   az spring-cloud app create --name solar-system-weather --runtime-version NetCore_31
   ```

   `solar-system-weather` 是在 `SolarSystemWeather` 项目的 appsettings.json 文件中指定的名称。

   此命令可能需要几分钟才能运行。

1. 将目录切换到 `SolarSystemWeather` 项目。

   ```console
   cd ../solar-system-weather
   ```

1. 创建要部署的二进制文件和 .zip 文件。

   ```console
   dotnet publish -c release -o ./publish
   ```

1. 部署到 Azure。

   ```console
   az spring-cloud app deploy -n solar-system-weather --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll --artifact-path ./publish-deploy-solar.zip
   ```
   
   此命令可能需要几分钟才能运行。

## <a name="assign-public-endpoint"></a>分配公共终结点

若要测试应用程序，请通过浏览器将 HTTP GET 请求发送到 `solar-system-weather` 应用程序。  为此，需要一个公共终结点以用于请求。

1. 若要分配终结点，请运行以下命令。

   ```azurecli
   az spring-cloud app update -n solar-system-weather --is-public true
   ```

1. 若要获取终结点的 URL，请运行以下命令。

   Windows:

   ```azurecli
   az spring-cloud app show -n solar-system-weather -o table
   ```

   Linux：

   ```azurecli
   az spring-cloud app show --name solar-system-weather | grep url
   ```

## <a name="test-the-application"></a>测试应用程序

将 GET 请求发送到 `solar-system-weather` 应用。 在浏览器中，导航到末尾追加了 `/weatherforecast` 的公共 URL。 例如：

```
https://servicename-solar-system-weather.azuremicroservices.io/weatherforecast
```

输出为 JSON：

```json
[{"Key":"Mercury","Value":"very warm"},{"Key":"Venus","Value":"quite unpleasant"},{"Key":"Mars","Value":"very cool"},{"Key":"Saturn","Value":"a little bit sandy"}]
```

此响应显示两个微服务应用程序都在运行。 `SolarSystemWeather` 应用返回从 `PlanetWeatherProvider` 应用检索的数据。
::: zone-end

::: zone pivot="programming-language-java"
本文档说明如何使用以下内容生成微服务应用程序并将其部署到 Azure Spring Cloud：
* Azure CLI
* Maven 插件
* Intellij

在使用 Azure CLI 或 Maven 进行部署之前，请完成[预配 Azure Spring Cloud 实例](spring-cloud-quickstart-provision-service-instance.md)和[设置配置服务器](spring-cloud-quickstart-setup-config-server.md)的示例。

## <a name="prerequisites"></a>先决条件

* [安装 JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true)
* [注册 Azure 订阅](https://azure.microsoft.com/free/)
* （可选）[安装 Azure CLI 版本 2.0.67 或更高版本](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)，并使用以下命令安装 Azure Spring Cloud 扩展：`az extension add --name spring-cloud`
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
    git clone https://github.com/Azure-Samples/piggymetrics
    ```
  
1. 通过运行以下命令更改目录并生成项目：

    ```
    cd piggymetrics
    mvn clean package -DskipTests
    ```

### <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>生成配置并将其部署到 Azure Spring Cloud

1. 通过在包含父 POM 的 PiggyMetrics 的根文件夹中运行以下命令来生成配置。 如果已使用 Azure CLI 登录，则该命令将自动提取凭据。 否则，它将提示你登录。 有关详细信息，请参阅我们的 [wiki 页面](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication)。

    ```
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.3.0:config
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

1. 在“名称”字段中，将“:gateway”追加到现有“名称”。
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

导航到前面步骤的输出中提供的 URL，以访问 PiggyMetrics 应用程序。 例如： `https://<service instance name>-gateway.azuremicroservices.io`

![访问 PiggyMetrics](media/spring-cloud-quickstart-launch-app-cli/launch-app.png)

还可以导航 Azure 门户以查找 URL。 
1. 导航到服务
2. 选择“应用”
3. 选择“网关”

    ![导航应用](media/spring-cloud-quickstart-launch-app-cli/navigate-app1.png)
    
4. 在“网关 | 概述”页上查找 URL

    ![第二个导航应用](media/spring-cloud-quickstart-launch-app-cli/navigate-app2-url.png)

::: zone-end

## <a name="next-steps"></a>后续步骤

在此快速入门中，你创建了 Azure 资源，如果这些资源保留在订阅中，将继续产生费用。 如果不打算继续学习下一个快速入门，请参阅[清理资源](spring-cloud-quickstart-logs-metrics-tracing.md#clean-up-resources)。 否则，请继续学习下一个快速入门：

> [!div class="nextstepaction"]
> [日志、指标和跟踪](spring-cloud-quickstart-logs-metrics-tracing.md)
