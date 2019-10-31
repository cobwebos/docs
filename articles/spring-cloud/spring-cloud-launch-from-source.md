---
title: 快速入门：从源代码启动春季云应用程序
description: 了解如何直接从源代码启动 Azure 春季云应用程序
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: jeconnoc
ms.openlocfilehash: 573baa242c06868326568a82bc358e136f1ece2c
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177974"
---
# <a name="launch-your-spring-cloud-application-from-source-code"></a>从源代码启动春季 Cloud 应用程序

使用 Azure Spring Cloud 可以在 Azure 上轻松运行基于 Spring Cloud 的微服务应用程序。

使用 Azure 弹簧 Cloud，可以直接从 java 源代码或预生成的 JAR 启动应用程序。 本文将指导你完成所需的步骤。

本快速入门介绍如何执行以下操作：

> [!div class="checklist"]
> * 预配服务实例
> * 为实例设置配置服务器
> * 在本地构建微服务应用程序
> * 部署每个微服务
> * 为应用程序分配公共终结点

## <a name="prerequisites"></a>必备组件

>[!Note]
> 在开始本快速入门之前，请确保 Azure 订阅可以访问 Azure Spring Cloud。  由于此服务为预览版服务，我们要求用户先与我们联系，然后我们才能将其订阅添加到允许列表中。  若要探索 Azure Spring Cloud 的功能，请[填写此表单](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u)。  尽管 Azure 春季云处于预览阶段，但 Microsoft 提供的支持不受 SLA 限制。  有关预览过程中的支持的详细信息，请参阅此[支持常见问题](https://azure.microsoft.com/support/faq/)。

在开始之前，请确保你的 Azure 订阅具有所需的依赖项：

1. [安装 Git](https://git-scm.com/)
2. [安装 JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [安装 Maven 3.0 或更高版本](https://maven.apache.org/download.cgi)
4. [安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [注册 Azure 订阅](https://azure.microsoft.com/free/)

> [!TIP]
> Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。  它预装有常用的 Azure 工具，其中包括最新版的 Git、JDK、Maven 和 Azure CLI。 如果你已登录到 Azure 订阅，请从 shell.azure.com 启动 [Azure Cloud Shell](https://shell.azure.com)。  若要详细了解 Azure Cloud Shell，可[阅读我们的文档](../cloud-shell/overview.md)

## <a name="install-the-azure-cli-extension"></a>安装 Azure CLI 扩展

通过以下命令为 Azure CLI 安装 Azure 春季云扩展

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>使用 Azure CLI 预配服务实例

登录到 Azure CLI 并选择你的有效订阅。 请务必选择已列入 Azure Spring Cloud 白名单的有效订阅

```Azure CLI
az login
az account list -o table
az account set --subscription
```

打开 Azure CLI 窗口，运行以下命令预配 Azure Spring Cloud 的实例。 请注意，我们还会告诉 Azure 春季 Cloud 在此分配公共域。

```azurecli
    az spring-cloud create -n <resource name> -g <resource group name> --is-public true
```

部署服务实例需要大约五分钟。

使用以下命令设置默认的资源组名称和群集名称：

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

## <a name="create-the-spring-cloud-application"></a>创建弹簧 Cloud 应用程序

以下命令在你的订阅中创建一个春季云应用程序。  这会创建一个空的弹簧云服务，我们可以将其上传到应用程序。

```azurecli
az spring-cloud app create -n <app-name>
```

## <a name="deploy-your-spring-cloud-application"></a>部署春季云应用程序

你可以从预生成的 JAR 或从 Gradle 或 Maven 存储库部署你的应用程序。  为以下每种情况查找说明。

### <a name="deploy-a-built-jar"></a>部署生成的 JAR

若要从在本地计算机上构建的 JAR 进行部署，请确保生成产生[胖 JAR](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven)。

将 fat JAR 部署到活动部署

```azurecli
az spring-cloud app deploy -n <app-name> --jar-path <path-to-fat-JAR>
```

将 fat JAR 部署到特定部署

```azurecli
az spring-cloud app deployment create --app <app-name> -n <deployment-name> --jar-path <path-to-built-jar>
```

### <a name="deploy-from-source-code"></a>从源代码部署

Azure 春季 Cloud 使用[kpack](https://github.com/pivotal/kpack)生成项目。  你可以使用 Azure CLI 上传源代码，使用 kpack 生成项目，并将其部署到目标应用程序。

> [!WARNING]
> 项目只能在 `target` 的 `MANIFEST.MF` 中生成一个具有 `main-class` 条目的 JAR 文件（适用于 Maven 部署或 `build/libs` （适用于 Gradle 部署）。  具有 `main-class` 项的多个 JAR 文件将导致部署失败。

对于单模块 Maven/Gradle 项目：

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name>
```

对于包含多个模块的 Maven/Gradle 项目，请对每个模块重复以下操作：

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name> --target-module <relative-path-to-module>
```

### <a name="show-deployment-logs"></a>显示部署日志

使用以下命令查看 kpack 生成日志：

```azurecli
az spring-cloud app show-deploy-log -n <app-name> [-d <deployment-name>]
```

> [!NOTE]
> 如果部署是使用 kpack 从源生成的，则 kpack 日志将仅显示最新部署。

## <a name="assign-a-public-endpoint-to-gateway"></a>将公共终结点分配到网关

1. 打开“应用程序仪表板”页。
2. 选择 `gateway` 应用程序以显示“应用程序详细信息”页。
3. 选择“分配域”，将一个公共终结点分配到网关。 这可能需要花费几分钟时间。 
4. 在浏览器中输入分配的公共 IP 以查看正在运行的应用程序。

## <a name="next-steps"></a>后续步骤

在此快速入门中，读者学习了如何：

> [!div class="checklist"]
> * 预配服务实例
> * 为实例设置配置服务器
> * 在本地构建微服务应用程序
> * 部署每个微服务
> * 编辑应用程序的环境变量
> * 为应用程序网关分配公共 IP

> [!div class="nextstepaction"]
> [准备好要部署的 Azure Spring Cloud 应用程序](spring-cloud-tutorial-prepare-app-deployment.md)
