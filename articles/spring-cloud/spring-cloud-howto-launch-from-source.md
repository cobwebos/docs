---
title: 操作方法 - 从源代码启动 Spring Cloud 应用程序
description: 本快速入门介绍如何直接从源代码启动 Azure Spring Cloud 应用程序
author: MikeDodaro
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/03/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: ee64343a040f4ed3288f8c4addb64c1ef2437cc1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91326178"
---
# <a name="how-to-launch-your-spring-cloud-application-from-source-code"></a>如何从源代码启动 Spring Cloud 应用程序

本文适用于：✔️ Java

Azure Spring Cloud 在 Azure 上启用了基于 Spring Cloud 的微服务应用程序。

可直接从 java 源代码或预构建的 JAR 启动应用程序。 本文介绍部署过程。

本快速入门介绍如何：

> [!div class="checklist"]
> * 预配服务实例
> * 为实例设置配置服务器
> * 在本地构建微服务应用程序
> * 部署每个微服务
> * 为应用程序分配公共终结点

## <a name="prerequisites"></a>先决条件
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

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>使用 Azure CLI 预配服务实例

登录到 Azure CLI 并选择你的有效订阅。 

```azurecli
az login
az account list -o table
az account set --subscription
```

创建用于包含 Azure Spring Cloud 服务的资源组。 你可以详细了解 [Azure 资源组](../azure-resource-manager/management/overview.md)。

```azurecli
az group create --location eastus --name <resource group name>
```

运行以下命令以预配 Azure Spring Cloud 的实例。 准备 Azure Spring Cloud 服务的名称。 该名称必须为 4 到 32 个字符，只能包含小写字母、数字及连字符。 服务名称的第一个字符必须是字母，最后一个字符必须是字母或数字。

```azurecli
az spring-cloud create -n <resource name> -g <resource group name>
```

部署服务实例需要大约五分钟时间。

使用以下命令设置默认的资源组名称和 Azure Spring Cloud 实例名称：

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

## <a name="create-the-azure-spring-cloud-application"></a>创建 Azure Spring Cloud 应用程序

以下命令在订阅中创建 Azure Spring Cloud 应用程序。  此操作将创建一个空的服务，我们可以将其上传到应用程序。

```azurecli
az spring-cloud app create -n <app-name>
```

## <a name="deploy-your-spring-cloud-application"></a>部署 Spring Cloud 应用程序

可以从预构建的 JAR 或从 Gradle 或 Maven 存储库部署应用程序。  在下面找到每种情况的说明。

### <a name="deploy-a-built-jar"></a>部署预构建的 JAR

若要从在本地计算机上构建的 JAR 进行部署，请确保构建生成 [fat-JAR](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven)。

将 fat-JAR 部署到活动部署

```azurecli
az spring-cloud app deploy -n <app-name> --jar-path <path-to-fat-JAR e.g. "target\hellospring-0.0.1-SNAPSHOT.jar">
```

将 fat-JAR 部署到特定部署

```azurecli
az spring-cloud app deployment create --app <app-name> -n <deployment-name> --jar-path <path-to-fat-JAR e.g. "target\hellospring-0.0.1-SNAPSHOT.jar">
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

1. 打开“应用程序仪表板”页。****
2. 选择 `gateway` 应用程序以显示“应用程序详细信息”页。****
3. 选择“分配终结点”，将一个公共终结点分配到网关。 这可能需要几分钟的时间。 
4. 在浏览器中输入分配的公共 IP 以查看正在运行的应用程序。

> [!div class="nextstepaction"]
> [我遇到了问题](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=public-endpoint)

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
> [Spring Cloud 日志、指标、跟踪](spring-cloud-quickstart-logs-metrics-tracing.md)

GitHub 中提供了更多示例：[Azure Spring Cloud 示例](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql)。
