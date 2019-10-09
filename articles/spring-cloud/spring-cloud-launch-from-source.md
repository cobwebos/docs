---
title: 快速入门：从源代码启动春季 Cloud 应用程序
description: 了解如何直接从源代码启动 Azure 春季云应用程序
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 9/27/2019
ms.author: jeconnoc
ms.openlocfilehash: 445cac1494828362d54a8c15e68d27f01b165841
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170538"
---
# <a name="launch-your-spring-cloud-application-from-source-code"></a>从源代码启动春季 Cloud 应用程序

使用 Azure 弹簧 Cloud，可以直接从 java 源代码或预生成的 JAR 启动应用程序。 本文将指导你完成所需的步骤。

## <a name="prerequisites"></a>先决条件

>[!Note]
> 开始本快速入门之前，请确保 Azure 订阅有权访问 Azure 春季 Cloud。  作为预览服务，我们要求你联系我们，以便我们可以将你的订阅添加到我们的允许列表。  如果你想要探索 Azure 春季云的功能，请 @no__t，请参阅此窗体 @ no__t-1。

在开始之前，请确保你的 Azure 订阅具有所需的依赖项：

1. [安装 Git](https://git-scm.com/)
2. [安装 JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [安装 Maven 3.0 或更高版本](https://maven.apache.org/download.cgi)
4. [安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [注册 Azure 订阅](https://azure.microsoft.com/free/)

> [!TIP]
> Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。  它预安装了常见的 Azure 工具，包括最新版本的 Git、JDK、Maven 和 Azure CLI。 如果已登录到 Azure 订阅，请从 shell.azure.com 启动[Azure Cloud Shell](https://shell.azure.com) 。  若要了解有关 Azure Cloud Shell 的详细信息，请[阅读我们的文档](../cloud-shell/overview.md)

## <a name="install-the-azure-cli-extension"></a>安装 Azure CLI 扩展

通过以下命令为 Azure CLI 安装 Azure 春季云扩展

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>使用 Azure CLI 预配服务实例

登录到 Azure CLI 并选择活动订阅。 请确保选择适用于 Azure 春季云的活动订阅

```Azure CLI
az login
az account list -o table
az account set --subscription
```

打开 Azure CLI 窗口，并运行以下命令来预配 Azure 春季 Cloud 的实例。 请注意，我们还会告诉 Azure 春季 Cloud 在此分配公共域。

```azurecli
    az spring-cloud create -n <resource name> -g <resource group name> --is-public true
```

部署服务实例需要大约五分钟。

使用以下命令设置默认资源组名称和群集名称：

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
> 项目只能在 `target` 的 @no__t 中生成一个具有 @no__t 0 条目的 JAR 文件（适用于 Maven 部署或 @no__t 3 （对于 Gradle 部署）。  具有 @no__t 项的多个 JAR 文件将导致部署失败。

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

## <a name="assign-a-public-endpoint-to-gateway"></a>向网关分配公共终结点

1. 打开 "**应用程序仪表板**" 页。
2. 选择 `gateway` 应用程序以显示**应用程序详细信息**页。
3. 选择 "**分配域**" 将公共终结点分配到网关。 这可能要花费几分钟时间。 
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
> [准备要部署的 Azure 春季 Cloud 应用程序](spring-cloud-tutorial-prepare-app-deployment.md)
