---
title: 使用 Azure 门户启动 Azure Spring Cloud 应用程序
description: 使用 Azure 门户将示例应用程序部署到 Azure Spring Cloud。
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/04/2019
ms.author: v-vasuke
ms.openlocfilehash: 74a47bc5fc6dbcadef5e1a0da88eb93056334703
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244881"
---
# <a name="quickstart-launch-an-azure-spring-cloud-application-using-the-azure-portal"></a>快速入门：使用 Azure 门户启动 Azure Spring Cloud 应用程序

使用 Azure Spring Cloud 可以在 Azure 上轻松运行基于 Spring Cloud 的微服务应用程序。

本快速入门介绍如何将现有的 Spring Cloud 应用程序部署到 Azure。 [此处](https://github.com/Azure-Samples/PiggyMetrics)提供了本教程中使用的示例应用程序代码的链接。 完成后，可以在线访问所提供的示例应用程序，并可通过 Azure 门户对其进行管理。

本快速入门介绍如何执行以下操作：

> [!div class="checklist"]
> * 预配服务实例
> * 为实例设置配置服务器
> * 在本地构建微服务应用程序
> * 部署每个微服务
> * 为应用程序分配公共终结点

## <a name="prerequisites"></a>先决条件

>[!Note]
> 在开始本快速入门之前，请确保你的 Azure 订阅有权访问 Azure Spring Cloud。  由于此服务为预览版服务，我们要求客户先与我们联系，然后我们才能将其订阅添加到允许列表中。  若要探索 Azure Spring Cloud 的功能，请通过电子邮件 (azure-spring-cloud@service.microsoft.com) 联系我们。

>[!TIP]
> Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。  它预装有常用的 Azure 工具，其中包括最新版的 Git、JDK、Maven 和 Azure CLI。 如果你已登录到 Azure 订阅，请从 shell.azure.com 启动 [Azure Cloud Shell](https://shell.azure.com)。  若要详细了解 Azure Cloud Shell，请[阅读我们的文档](../cloud-shell/overview.md)

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

## <a name="provision-a-service-instance-on-the-azure-portal"></a>在 Azure 门户中预配服务实例

1. 在 Web 浏览器中，打开 [Azure 门户](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=AppPlatformExtension)并登录到你的帐户。

1. 搜索 **Azure Spring Cloud** 并将其选中，转到概述页。 选择“创建”按钮开始预配。 

1. 根据以下指导填写表单：
    - 服务名称：指定服务实例的名称。  该名称必须为 4 到 32 个字符，只能包含小写字母、数字及连字符。  服务名称的第一个字符必须是字母，最后一个字符必须是字母或数字。
    - 订阅：选择要在其中收取此资源费用的订阅。  请确保此订阅已添加到 Azure Spring Cloud 的允许列表。
    - 资源组：最佳做法是为新资源创建新的资源组。
    - 位置：选择服务实例的位置。 目前支持的位置包括：美国东部、美国西部 2、西欧和东南亚。
    
部署服务大约需要 5 分钟的时间。  部署后，会显示服务实例的“概述”页。 

## <a name="set-up-your-configuration-server"></a>设置配置服务器

1. 转到服务的“概览”页，选择“配置服务器”。  

1. 在“默认存储库”部分，将“URI”设置为“https://github.com/Azure-Samples/piggymetrics”，将“标签”设置为“config”，然后选择“应用”以保存更改。    

## <a name="build-and-deploy-microservice-applications"></a>生成并部署微服务应用程序

1. 打开命令窗口，运行以下命令，将示例应用存储库克隆到本地计算机。

    ```cli
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

1. 运行以下命令来生成项目。

    ```cli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

1. 登录到 Azure CLI 并活动的订阅。

    ```cli
    # Login to Azure CLI
    az login

    # List all subscriptions
    az account list -o table

    # Set active subscription
    az account set --subscription <target subscription ID>
    ```

1. 为资源组和服务指定名称。 请务必将以下占位符替换为之前在本教程中预配的资源组名称和服务名称。

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

1. 创建 `gateway` 应用程序并部署 JAR 文件。

    ```azurecli
    az spring-cloud app create -n gateway
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    ```

1. 遵循相同的模式创建 `account-service` 和 `auth-service` 应用程序，并部署其 JAR 文件。

    ```cli
    az spring-cloud app create -n account-service
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app create -n auth-service
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

1. 完成应用程序部署需要几分钟的时间。 若要确认是否已部署这些应用程序，请在 Azure 门户中转到“应用”边栏选项卡。  应会看到，这三个应用程序各占一行。

## <a name="assign-a-public-endpoint-to-gateway"></a>将公共终结点分配到网关

1. 打开“应用程序仪表板”页。 
2. 选择 `gateway` 应用程序以显示“应用程序详细信息”页。 
3. 选择“分配域”，将一个公共终结点分配到网关。  这可能需要花费几分钟时间。 
4. 在浏览器中输入分配的公共 IP 以查看正在运行的应用程序。


## <a name="next-steps"></a>后续步骤

在此快速入门中，读者学习了如何：

> [!div class="checklist"]
> * 预配服务实例
> * 为实例设置配置服务器
> * 在本地构建微服务应用程序
> * 部署每个微服务
> * 为应用程序网关分配公共终结点

> [!div class="nextstepaction"]
> [准备好要部署的 Azure Spring Cloud 应用程序](spring-cloud-tutorial-prepare-app-deployment.md)
