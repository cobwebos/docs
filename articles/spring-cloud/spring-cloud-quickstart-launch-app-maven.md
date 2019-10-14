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
ms.openlocfilehash: 01140e94e8d0cc47570824970801bdd0043324d7
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166518"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-using-the-maven-plug-in"></a>快速入门：使用 Maven 插件启动 Azure Spring Cloud 应用

使用 Azure Spring Cloud 的 Maven 插件，可以轻松地创建和更新 Azure Spring Cloud 服务应用程序。 通过预先定义配置，可以将应用程序部署到现有的 Azure Spring Cloud 服务。 在本文中，我们使用名为 PiggyMetrics 的示例应用程序演示此功能。

>[!Note]
> 在开始本快速入门之前，请确保 Azure 订阅可以访问 Azure Spring Cloud。  由于此服务为预览版服务，因此客户必须先联系我们，然后我们才能将其订阅添加到我的允许列表中。  如果你希望试用 Azure Spring Cloud 的功能，请[填写此表](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u
)。

>[!TIP]
> Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。  它预安装有常用 Azure 工具，其中包括最新版的 Git、JDK、Maven 和 Azure CLI。 如果已登录到 Azure 订阅，请从 shell.azure.com 启动 [Azure Cloud Shell](https://shell.azure.com)。  若要详细了解 Azure Cloud Shell，可[阅读我们的文档](../cloud-shell/overview.md)

完成本快速入门教程需要：

1. [安装 Git](https://git-scm.com/)
2. [安装 JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [安装 Maven 3.0 或更高版本](https://maven.apache.org/download.cgi)
4. [安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [注册 Azure 订阅](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>安装 Azure CLI 扩展

使用以下命令安装用于 Azure CLI 的 Azure Spring Cloud 扩展

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>在 Azure 门户中预配一个服务实例

1. 在 Web 浏览器中，打开 [Azure 门户](https://portal.azure.com)并登录到帐户。

1. 搜索 **Azure Spring Cloud** 并将其选中，转到概览页。 选择“创建”按钮即可开始。 

1. 按照以下指南填写窗体：
    - 服务名称：指定服务实例的名称。  名称必须为 4 到 32 个字符，只能包含小写字母、数字及连字符。  服务名称的第一个字符必须是字母，最后一个字符必须是字母或数字。
    - 订阅：选择你希望我们针对此资源对其收费的订阅。  确保该订阅已添加到我们的 Azure Spring Cloud 允许列表。
    - 资源组：最佳做法是为新资源创建新资源组。
    - 位置：选择服务实例的位置。 目前支持的位置包括：美国东部、美国西部 2、西欧和东南亚。
    
部署服务大约需要 5 分钟的时间。  部署后，会显示服务实例的“概览”页。 

## <a name="set-up-your-configuration-server"></a>设置配置服务器

1. 转到服务的“概览”页，选择“配置服务器”。  
1. 在“默认存储库”部分，  将 **URI** 设置为“https://github.com/Azure-Samples/piggymetrics”，将 **LABEL** 设置为“config”，然后选择“应用”以保存所做的更改。 

## <a name="clone-and-build-the-sample-application-repository"></a>克隆并构建示例应用程序存储库

1. 通过运行以下命令克隆 git 存储库。

    ```azurecli
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. 通过运行以下命令更改目录并生成项目。

    ```azurecli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-and-deploy-the-azure-spring-cloud-configuration"></a>生成并部署 Azure Spring Cloud 配置

1. 将以下内容添加到 `pom.xml` 或 `settings.xml`，使 Maven 能够与 Azure Spring Cloud 配合使用。

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

1. 运行以下命令，生成一项配置。

    ```azurecli
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:config
    ```

    1. 选择模块 `gateway`、`auth-service` 和 `account-service`。

    1. 选择订阅和 Azure Spring Cloud 服务群集。

    1. 在提供的项目列表中，输入与 `gateway` 对应的数字，允许对其进行公共访问。
    
    1. 确认配置。

1. 使用以下命令部署应用：

   ```azurecli
   mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:deploy
   ```

1. 可以使用在上一命令的输出中提供的 URL 来访问 PiggyMetrics。

## <a name="next-steps"></a>后续步骤

在本快速入门中，我们部署了一个来自 Maven 存储库的 Spring Cloud 应用程序。  若要详细了解 Azure Spring Cloud，请继续学习相关教程，了解如何对应用进行部署准备。

> [!div class="nextstepaction"]
> [对 Azure Spring Cloud 应用程序进行部署准备](spring-cloud-tutorial-prepare-app-deployment.md)
