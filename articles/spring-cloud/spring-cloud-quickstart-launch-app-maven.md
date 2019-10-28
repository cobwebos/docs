---
title: 快速入门：使用 Maven 启动应用程序 - Azure Spring Cloud
description: 使用 Maven 启动示例应用程序
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/05/2019
ms.author: v-vasuke
ms.openlocfilehash: e773b997cca3fa9a1f11fec2ac449e1fc11c5364
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554569"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-by-using-the-maven-plug-in"></a>快速入门：使用 Maven 插件启动 Azure Spring Cloud 应用

使用 Azure Spring Cloud Maven 插件，可以轻松地创建和更新 Azure Spring Cloud 服务应用程序。 通过预先定义配置，可以将应用程序部署到现有的 Azure Spring Cloud 服务。 在本文中，我们使用名为 PiggyMetrics 的示例应用程序演示此功能。

>[!Note]
> 在开始本快速入门之前，请确保你的 Azure 订阅有权访问 Azure Spring Cloud。 由于此服务为预览版服务，我们邀请你先与我们联系，然后我们才能将其订阅添加到允许列表中。 若要探索 Azure Spring Cloud 的功能，请填写并提交[Azure Spring Cloud（专用预览版）- 兴趣表](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u)。

>[!TIP]
> Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的命令。 它预装有常用的 Azure 工具，其中包括最新版的 Git、Java 开发工具包 (JDK)、Maven 和 Azure CLI。 如果已登录到 Azure 订阅，请启动 [Azure Cloud Shell](https://shell.azure.com)。 有关详细信息，请参阅 [Azure Cloud Shell 概述](../cloud-shell/overview.md)。

完成本快速入门教程需要：

1. [安装 Git](https://git-scm.com/)。
2. [安装 JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)。
3. [安装 Maven 3.0 或更高版本](https://maven.apache.org/download.cgi)。
4. [安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
5. [注册免费的 Azure 订阅](https://azure.microsoft.com/free/)。

## <a name="install-the-azure-cli-extension"></a>安装 Azure CLI 扩展

使用以下命令安装用于 Azure CLI 的 Azure Spring Cloud 扩展：

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>在 Azure 门户中预配服务实例

1. 在 Web 浏览器中，打开 [Azure 门户](https://portal.azure.com)并登录到你的帐户。

1. 搜索并选择“Azure Spring Cloud”  。 
1. 在概览页上选择“创建”，然后执行以下操作：   

    a. 在“服务名称”框中，指定服务实例的名称。  该名称必须为 4 到 32 个字符，只能包含小写字母、数字及连字符。 服务名称的第一个字符必须是字母，最后一个字符必须是字母或数字。  

    b. 在“订阅”下拉列表中，选择要在其中收取此资源费用的订阅。  请确保此订阅已添加到 Azure Spring Cloud 的允许列表。  

    c. 在“资源组”框中，创建新的资源组。  最佳做法是为新资源创建资源组。  

    d. 在“位置”下拉列表中，选择服务实例的位置。  目前支持的位置包括：美国东部、美国西部 2、西欧和东南亚。
    
部署服务大约需要 5 分钟的时间。 部署服务后，会显示服务实例的“概览”页。 

## <a name="set-up-your-configuration-server"></a>设置配置服务器

1. 在服务的“概览”页上，选择“配置服务器”。  
1. 在“默认存储库”部分，将“URI”设置为 **https://github.com/Azure-Samples/piggymetrics** ，将“标签”设置为“config”，然后选择“应用”以保存更改。     

## <a name="clone-and-build-the-sample-application-repository"></a>克隆并构建示例应用程序存储库

1. 通过运行以下命令，克隆 Git 存储库：

    ```azurecli
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. 通过运行以下命令更改目录并生成项目：

    ```azurecli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-and-deploy-the-azure-spring-cloud-configuration"></a>生成并部署 Azure Spring Cloud 配置

1. 若要使 Maven 能够与 Azure Spring Cloud 配合使用，请将以下代码添加到 *pom.xml* 或 *settings.xml* 文件。

    ```xml
    <pluginRepositories>
      <pluginRepository>
        <id>maven.snapshots</id>
        <name>Maven Central Snapshot Repository</name>
        <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
        <releases>
          <enabled>false</enabled>
        </releases>
        <snapshots>
          <enabled>true</enabled>
        </snapshots>
      </pluginRepository>
    </pluginRepositories>
    ```

1. 运行以下命令，生成一项配置：

    ```azurecli
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:config
    ```

    a. 选择模块 `gateway`、`auth-service` 和 `account-service`。

    b. 选择订阅和 Azure Spring Cloud 服务群集。

    c. 在提供的项目的列表中，输入与 `gateway` 对应的数字，允许对其进行公共访问。
    
    d. 确认配置。

1. 使用以下命令部署应用：

   ```azurecli
   mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:deploy
   ```

1. 可以使用在上一命令的输出中提供的 URL 来访问 PiggyMetrics。

## <a name="next-steps"></a>后续步骤

在本快速入门中，我们部署了一个来自 Maven 存储库的 Spring Cloud 应用程序。 若要详细了解 Azure Spring Cloud，请继续学习相关教程，了解如何对应用进行部署准备。

> [!div class="nextstepaction"]
> [准备好要部署的 Azure Spring Cloud 应用程序](spring-cloud-tutorial-prepare-app-deployment.md)
