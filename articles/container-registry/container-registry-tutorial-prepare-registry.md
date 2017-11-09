---
title: "Azure 容器注册表教程 - 准备异地复制的 Azure 容器注册表"
description: "创建 Azure 容器注册表，配置异地复制，准备 Docker 映像，并将该映像部署到注册表。 由三个部分构成的系列教程的第一部分。"
services: container-registry
documentationcenter: 
author: mmacy
manager: timlt
editor: mmacy
tags: acr, azure-container-registry, geo-replication
keywords: "Docker, 容器, 注册表, Azure"
ms.service: container-registry
ms.devlang: 
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/26/2017
ms.author: marsma
ms.custom: 
ms.openlocfilehash: 88feffc13690a3a33f757a43972c5ef1fe967b7f
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2017
---
# <a name="prepare-a-geo-replicated-azure-container-registry"></a>准备异地复制的 Azure 容器注册表

Azure 容器注册表是部署在 Azure 中的专用 Docker 注册表，能使部署尽量靠近网络。 本套教程由三篇文章构成，介绍如何使用异地复制将 Linux 容器中运行的 ASP.NET Core Web 应用程序部署到两个[用于容器的 Web 应用](../app-service/containers/index.yml)实例。 在其中可以了解 Azure 如何通过最靠近的异地复制存储库将映像部署到每个 Web 应用实例。

在这套由三个部分构成的系列教程中，第一部分的内容包括：

> [!div class="checklist"]
> * 创建异地复制的 Azure 容器注册表
> * 克隆 GitHub 中的应用程序源代码
> * 基于应用程序源代码生成 Docker 容器映像
> * 将容器映像推送到注册表

后续教程将会介绍如何将容器从专用注册表部署到在两个 Azure 区域中运行的 Web 应用。 然后，可以更新应用程序中的代码，通过一条 `docker push` 命令将两个 Web 应用实例更新到注册表。

## <a name="before-you-begin"></a>开始之前

本教程要求运行 Azure CLI 2.0.20 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

本教程假定基本了解核心 Docker 的概念，如容器、容器映像和基本的 Docker 命令。 如需要，请参阅 [Docker 入门]( https://docs.docker.com/get-started/)，了解容器基本知识。

若要完成本教程，需要 Docker 开发环境。 Docker 提供的包可在任何 [Mac](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 或 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 系统上轻松配置 Docker。

Azure Cloud Shell 不包含完成本教程每个步骤所需的 Docker 组件。 因此，我们建议在本地安装 Azure CLI 和 Docker 开发环境。

> [!IMPORTANT]
> Azure 容器注册表中的异地复制功能当前位于“预览”中。 需同意[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)才可使用预览版。 在正式版推出之前，此功能的某些方面可能会有所更改。
>

## <a name="create-a-container-registry"></a>创建容器注册表

登录到 [Azure 门户](http://portal.azure.com)。

选择“新建” > “容器” > “Azure 容器注册表”。

![在 Azure 门户中创建容器注册表][tut-portal-01]

使用以下设置配置新注册表：

* **注册表名称**：创建在 Azure 中全局唯一的、包含 5-50 个字母数字字符的注册表名称
* **资源组**：**新建** > `myResourceGroup`
* **位置**：`West US`
* **管理员用户**：`Enable`（用于容器的 Web 应用需使用此帐户来提取映像）
* **SKU**：`Premium`（异地复制需要此项设置）

选择“创建”，部署 ACR 实例。

![在 Azure 门户中创建容器注册表][tut-portal-02]

本教程的余下部分使用 `<acrName>` 作为所选**容器注册表名称**的占位符。

> [!TIP]
> 由于 Azure 容器注册表通常是在多个容器主机上使用的长期生存的资源，因此我们建议在注册表自身所在的资源组中创建该注册表。 配置异地复制注册表和 Webhook 时，这些附加资源会放置在同一个资源组中。
>

## <a name="configure-geo-replication"></a>配置异地复制

获取高级注册表后，可以配置异地复制。 Web 应用（在下一篇教程中，会将其配置为在两个区域中运行）可从最靠近的注册表中提取其容器映像。

在 Azure 门户中导航到新的容器注册表，选择“服务”下面的“复制项”：

![Azure 门户容器注册表 UI 中的“复制项”][tut-portal-03]

此时会出现一幅地图，其中显示了绿色的六边形，表示支持异地复制的 Azure 区域：

 ![Azure 门户中的区域地图][tut-map-01]

选择注册表对应的绿色六边形将它复制到“美国东部”区域，然后选择“创建复制项”下面的“创建”：

 ![Azure 门户中的“创建复制项”UI][tut-portal-04]

完成复制后，门户会显示两个区域的“就绪”状态。 使用“刷新”按钮刷新复制状态；创建并同步副本可能需要大约一分钟时间。

![Azure 门户中的复制状态 UI][tut-portal-05]

## <a name="container-registry-login"></a>容器注册表登录

配置异地复制后，生成一个容器映像并将其推送到注册表。 在将映像推送到 ACR 实例之前，必须先登录到 ACR 实例。 通过[基本、标准和高级 SKU](container-registry-skus.md)，可以使用 Azure 标识进行身份验证。

使用 [az acr login](https://docs.microsoft.com/en-us/cli/azure/acr#az_acr_login) 命令进行身份验证，并缓存注册表的凭据。 将 `<acrName>` 替换为在前面步骤中创建的注册表的名称。

```azurecli
az acr login --name <acrName>
```

该命令在完成时会返回 `Login Succeeded`。

## <a name="get-application-code"></a>获取应用程序代码

本教程中的示例包括使用 [ASP.NET Core](http://dot.net) 生成的小型 Web 应用程序。 该应用提供一个 HTML 页面，其中显示了 Azure 容器注册表已从中部署映像的区域。

![显示在浏览器中的教程应用][tut-app-01]

使用 git 将示例下载到某个本地目录，并执行 `cd` 切换到该目录：

```bash
git clone https://github.com/Azure-Samples/acr-helloworld.git
cd acr-helloworld
```

## <a name="update-dockerfile"></a>更新 Dockerfile

示例中包含的 Dockerfile 演示如何生成容器。 它首先创建一个正式的 [aspnetcore](https://store.docker.com/community/images/microsoft/aspnetcore) 映像，将应用程序文件复制到容器，安装依赖项，使用正式的 [aspnetcore-build](https://store.docker.com/community/images/microsoft/aspnetcore-build) 映像编译输出，最后生成优化的 aspnetcore 映像。

在克隆的源中，Dockerfile 位于 `./AcrHelloworld/Dockerfile`。

```dockerfile
FROM microsoft/aspnetcore:2.0 AS base
# Update <acrName> with the name of your registry
# Example: uniqueregistryname.azurecr.io
ENV DOCKER_REGISTRY <acrName>.azurecr.io
WORKDIR /app
EXPOSE 80

FROM microsoft/aspnetcore-build:2.0 AS build
WORKDIR /src
COPY *.sln ./
COPY AcrHelloworld/AcrHelloworld.csproj AcrHelloworld/
RUN dotnet restore
COPY . .
WORKDIR /src/AcrHelloworld
RUN dotnet build -c Release -o /app

FROM build AS publish
RUN dotnet publish -c Release -o /app

FROM base AS production
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "AcrHelloworld.dll"]
```

*acr-helloworld* 映像中的应用程序尝试通过在 DNS 中查询有关注册表登录服务器的信息，确定其容器的部署源区域。 必须在 Dockerfile 中的 `DOCKER_REGISTRY` 环境变量内指定注册表的登录服务器 URL。

首先，使用 `az acr show` 命令获取注册表的登录服务器 URL。 将 `<acrName>` 替换为在前面步骤中创建的注册表的名称。

```azurecli
az acr show --name <acrName> --query "{acrLoginServer:loginServer}" --output table
```

输出：

```bash
AcrLoginServer
-----------------------------
uniqueregistryname.azurecr.io
```

接下来，使用注册表的登录服务器 URL 更新 `DOCKER_REGISTRY` 行。 在此示例中，我们更新了该行以反映示例注册表名称 *uniqueregistryname*：

```dockerfile
ENV DOCKER_REGISTRY uniqueregistryname.azurecr.io
```

## <a name="build-container-image"></a>生成容器映像

使用注册表的 URL 更新 Dockerfile 之后，可以使用 `docker build` 来创建容器映像。 运行以下命令生成映像，并使用标记将它包含在专用注册表的 URL 中；同样，请将 `<acrName>` 替换为自己的注册表的名称：

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

生成 Docker 映像时，会显示多个输出行（此处的显示内容已截断）：

```bash
Sending build context to Docker daemon  523.8kB
Step 1/18 : FROM microsoft/aspnetcore:2.0 AS base
2.0: Pulling from microsoft/aspnetcore
3e17c6eae66c: Pulling fs layer
...
Step 18/18 : ENTRYPOINT dotnet AcrHelloworld.dll
 ---> Running in 6906d98c47a1
 ---> c9ca1763cfb1
Removing intermediate container 6906d98c47a1
Successfully built c9ca1763cfb1
Successfully tagged uniqueregistryname.azurecr.io/acr-helloworld:v1
```

使用 `docker images` 命令查看生成的映像：

```bash
docker images
```

输出：

```bash
REPOSITORY                                     TAG                 IMAGE ID            CREATED              SIZE
uniqueregistryname.azurecr.io/acr-helloworld   v1                  c9ca1763cfb1        About a minute ago   285MB
...
```

## <a name="push-image-to-azure-container-registry"></a>向 Azure 容器注册表推送映像

最后，使用 `docker push` 命令将 *acr-helloworld* 映像推送到注册表。 将 `<acrName>` 替换为注册表的名称。

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

由于已经为异地复制配置了注册表，因此，使用这一条 `docker push` 命令，即可将映像自动复制到“美国西部”和“美国东部”区域。

输出：

```bash
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
9716cfe18412: Pushed
074867a942d5: Pushed
a77666945b96: Pushed
953ff32f2036: Pushed
aa2e77726d3c: Pushed
98b800c91d50: Pushed
a75caa09eb1f: Pushed
v1: digest: sha256:c515bcebf249b591b558318e2d0ec21d1320340dbf335730eb32372ff7d34255 size: 1792
```

## <a name="next-steps"></a>后续步骤

在本教程中，我们创建了一个专用的异地复制容器注册表，生成了容器映像，然后将该图像推送到了该注册表。 我们已遵循本教程中的步骤执行以下操作：

> [!div class="checklist"]
> * 创建了异地复制的 Azure 容器注册表
> * 克隆了 GitHub 中的应用程序源代码
> * 基于应用程序源代码生成了 Docker 容器映像
> * 将容器映像推送到了注册表

请继续学习下一教程，了解如何将容器部署到多个 Azure 应用服务实例，并使用异地复制在本地提供映像。

> [!div class="nextstepaction"]
> [将容器部署到 Azure 应用服务](container-registry-tutorial-deploy-app.md)

<!-- IMAGES -->
[tut-portal-01]: ./media/container-registry-tutorial-prepare-registry/tut-portal-01.png
[tut-portal-02]: ./media/container-registry-tutorial-prepare-registry/tut-portal-02.png
[tut-portal-03]: ./media/container-registry-tutorial-prepare-registry/tut-portal-03.png
[tut-portal-04]: ./media/container-registry-tutorial-prepare-registry/tut-portal-04.png
[tut-portal-05]: ./media/container-registry-tutorial-prepare-registry/tut-portal-05.png
[tut-app-01]: ./media/container-registry-tutorial-prepare-registry/tut-app-01.png
[tut-map-01]: ./media/container-registry-tutorial-prepare-registry/tut-map-01.png
