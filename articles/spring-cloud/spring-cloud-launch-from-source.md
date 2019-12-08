---
title: 快速入门 - 从源代码启动 Spring Cloud 应用程序
description: 本快速入门介绍如何直接从源代码启动 Azure Spring Cloud 应用程序
author: jpconnock
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/30/2019
ms.author: jeconnoc
ms.openlocfilehash: 6e18ac6c0fa97125b89ea5760a5d7da6c1545393
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708892"
---
# <a name="quickstart-launch-your-spring-cloud-application-from-source-code"></a>快速入门：从源代码启动 Spring Cloud 应用程序

使用 Azure Spring Cloud 可以在 Azure 上轻松运行基于 Spring Cloud 的微服务应用程序。

使用 Azure Spring Cloud，可以直接从 java 源代码或预构建的 JAR 启动应用程序。 本文介绍所需的步骤。

本快速入门介绍如何执行以下操作：

> [!div class="checklist"]
> * 预配服务实例
> * 为实例设置配置服务器
> * 在本地构建微服务应用程序
> * 部署每个微服务
> * 为应用程序分配公共终结点

## <a name="prerequisites"></a>先决条件

>[!Note]
> Azure Spring Cloud 目前以公共预览版的形式提供。 使用公共预览版产品/服务，客户可以在产品/服务正式发布之前体验新功能。  公共预览功能和服务并非供生产使用。  有关预览期间支持的详细信息，请参阅[常见问题解答](https://azure.microsoft.com/support/faq/)或提交[支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。

在开始之前，请确保 Azure 订阅已安装所需的依赖项：

1. [安装 Git](https://git-scm.com/)
2. [安装 JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [安装 Maven 3.0 或更高版本](https://maven.apache.org/download.cgi)
4. [安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [注册 Azure 订阅](https://azure.microsoft.com/free/)

> [!TIP]
> Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。  它预装有常用的 Azure 工具，其中包括最新版的 Git、JDK、Maven 和 Azure CLI。 如果你已登录到 Azure 订阅，请从 shell.azure.com 启动 [Azure Cloud Shell](https://shell.azure.com)。  若要详细了解 Azure Cloud Shell，请[阅读我们的文档](../cloud-shell/overview.md)

## <a name="install-the-azure-cli-extension"></a>安装 Azure CLI 扩展

通过以下命令安装用于 Azure CLI 的 Azure Spring Cloud 扩展

```Azure CLI
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>使用 Azure CLI 预配服务实例

登录到 Azure CLI 并选择你的有效订阅。 请务必选择已列入 Azure Spring Cloud 白名单的有效订阅

```Azure CLI
az login
az account list -o table
az account set --subscription
```

打开 Azure CLI 窗口，运行以下命令预配 Azure Spring Cloud 的实例。 请注意，我们还会告知 Azure Spring Cloud 在此处分配公共域。

```azurecli
    az spring-cloud create -n <resource name> -g <resource group name> --is-public true
```

部署服务实例需要大约五分钟时间。

使用以下命令设置默认的资源组名称和群集名称：

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

## <a name="create-the-spring-cloud-application"></a>创建 Spring Cloud 应用程序

以下命令在订阅中创建 Spring Cloud 应用程序。  此操作将创建一个空的 Spring Cloud 服务，我们可以将其上传到应用程序。

```azurecli
az spring-cloud app create -n <app-name>
```

## <a name="deploy-your-spring-cloud-application"></a>部署 Spring Cloud 应用程序

可以从预构建的 JAR 或从 Gradle 或 Maven 存储库部署应用程序。  在下面找到每种情况的说明。

### <a name="deploy-a-built-jar"></a>部署预构建的 JAR

若要从在本地计算机上构建的 JAR 进行部署，请确保构建生成 [fat-JAR](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven)。

将 fat-JAR 部署到活动部署

```azurecli
az spring-cloud app deploy -n <app-name> --jar-path <path-to-fat-JAR>
```

将 fat-JAR 部署到特定部署

```azurecli
az spring-cloud app deployment create --app <app-name> -n <deployment-name> --jar-path <path-to-built-jar>
```

### <a name="deploy-from-source-code"></a>从源代码进行部署

Azure Spring Cloud 使用 [kpack](https://github.com/pivotal/kpack) 构建项目。  可以使用 Azure CLI 上传源代码，使用 kpack 构建项目，并将其部署到目标应用程序。

> [!WARNING]
> 项目只能生成一个 JAR 文件，该文件在 `target`（适用于 Maven 部署）或 `build/libs`（适用于 Gradle 部署）的 `MANIFEST.MF` 中具有 `main-class` 条目。  多个带有 `main-class` 条目的 JAR 文件将导致部署失败。

对于单模块 Maven/Gradle 项目：

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name>
```

对于包含多个模块的 Maven/Gradle 项目，请对每个模块重复以下步骤：

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
3. 选择“分配域”，将一个公共终结点分配到网关。  这可能需要花费几分钟时间。 
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
