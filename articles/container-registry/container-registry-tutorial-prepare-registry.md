---
title: 教程 - 创建异地复制注册表
description: 创建 Azure 容器注册表，配置异地复制，准备 Docker 映像，并将该映像部署到注册表。 由三个部分构成的系列教程的第一部分。
ms.topic: tutorial
ms.date: 04/30/2017
ms.custom: seodec18, mvc
ms.openlocfilehash: 70dc664d27fde3b7cf9fe4e5e3a99c041236ac16
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "79222144"
---
# <a name="tutorial-prepare-a-geo-replicated-azure-container-registry"></a>教程：准备异地复制的 Azure 容器注册表

Azure 容器注册表是部署在 Azure 中的专用 Docker 注册表，能使部署尽量靠近网络。 本套教程由三篇文章构成，介绍如何使用异地复制将 Linux 容器中运行的 ASP.NET Core Web 应用程序部署到两个[用于容器的 Web 应用](../app-service/containers/index.yml)实例。 在其中可以了解 Azure 如何通过最靠近的异地复制存储库将映像部署到每个 Web 应用实例。

在这套由三个部分构成的系列教程中，第一部分的内容包括：

> [!div class="checklist"]
> * 创建异地复制的 Azure 容器注册表
> * 克隆 GitHub 中的应用程序源代码
> * 基于应用程序源代码生成 Docker 容器映像
> * 将容器映像推送到注册表

后续教程将会介绍如何将容器从专用注册表部署到在两个 Azure 区域中运行的 Web 应用。 然后，可以更新应用程序中的代码，通过一条 `docker push` 命令将两个 Web 应用实例更新到注册表。

## <a name="before-you-begin"></a>开始之前

本教程需要本地安装 Azure CLI 2.0.31 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

要求熟悉 Docker 的核心概念，如容器、容器映像和基本的 Docker CLI 命令。 有关容器的入门基础知识，请参阅 [Docker 入门]( https://docs.docker.com/get-started/)。

要完成本教程，需要本地安装 Docker。 Docker 提供适用于 [macOS](https://docs.docker.com/docker-for-mac/)[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 系统的安装说明。

Azure Cloud Shell 不包含完成本教程每个步骤所需的 Docker 组件。 因此，我们建议在本地安装 Azure CLI 和 Docker 开发环境。

## <a name="create-a-container-registry"></a>创建容器注册表

登录 [Azure 门户](https://portal.azure.com)。

选择“创建资源” > “容器” > “Azure 容器注册表”。

![在 Azure 门户中创建容器注册表][tut-portal-01]

使用以下设置配置新注册表：

* **注册表名称**：创建在 Azure 中全局唯一的、包含 5-50 个字母数字字符的注册表名称
* **资源组**：**新建** > `myResourceGroup`
* **位置**：`West US`
* **管理员用户**：`Enable`（用于容器的 Web 应用需使用此帐户来提取映像）
* **SKU**：`Premium`（异地复制需要此项设置）

选择“创建”，部署 ACR 实例  。

![在 Azure 门户中创建容器注册表][tut-portal-02]

本教程的余下部分使用 `<acrName>` 作为所选**容器注册表名称**的占位符。

> [!TIP]
> 由于 Azure 容器注册表通常是在多个容器主机上使用的长期生存的资源，因此我们建议在注册表自身所在的资源组中创建该注册表。 配置异地复制注册表和 Webhook 时，这些附加资源会放置在同一个资源组中。
>

## <a name="configure-geo-replication"></a>配置异地复制

获取高级注册表后，可以配置异地复制。 Web 应用（在下一篇教程中，会将其配置为在两个区域中运行）可从最靠近的注册表中提取其容器映像。

在 Azure 门户中导航到新的容器注册表，选择“服务”下面的“复制项”：  

![Azure 门户容器注册表 UI 中的副本][tut-portal-03]

此时会出现一幅地图，其中显示了绿色的六边形，表示支持异地复制的 Azure 区域：

 ![Azure 门户中的区域地图][tut-map-01]

选择注册表对应的绿色六边形将它复制到“美国东部”区域，然后选择“创建复制项”下面的“创建”：  

 ![Azure 门户中的“创建副本”UI][tut-portal-04]

完成复制后，门户会显示两个区域的“就绪”状态。  使用“刷新”按钮刷新复制状态；创建并同步副本可能需要大约一分钟时间。 

![Azure 门户中的复制状态 UI][tut-portal-05]

## <a name="container-registry-login"></a>容器注册表登录

配置异地复制后，生成一个容器映像并将其推送到注册表。 在将映像推送到 ACR 实例之前，必须先登录到 ACR 实例。

使用 [az acr login](https://docs.microsoft.com/cli/azure/acr#az-acr-login) 命令进行身份验证，并缓存注册表的凭据。 将 `<acrName>` 替换为之前创建的注册表的名称。

```azurecli
az acr login --name <acrName>
```

该命令在完成时会返回 `Login Succeeded`。

## <a name="get-application-code"></a>获取应用程序代码

本教程中的示例包括使用 [ASP.NET Core][aspnet-core] 生成的小型 Web 应用程序。 该应用提供一个 HTML 页面，其中显示了 Azure 容器注册表已从中部署映像的区域。

![显示在浏览器中的教程应用][tut-app-01]

使用 git 将示例下载到某个本地目录，并执行 `cd` 切换到该目录：

```bash
git clone https://github.com/Azure-Samples/acr-helloworld.git
cd acr-helloworld
```

如果没有安装 `git`，可直接从 GitHub [下载 ZIP 存档][acr-helloworld-zip]。

## <a name="update-dockerfile"></a>更新 Dockerfile

示例中包含的 Dockerfile 演示如何生成容器。 它首先创建一个正式的 [aspnetcore][dockerhub-aspnetcore] 映像，将应用程序文件复制到容器，安装依赖项，使用正式的 [aspnetcore-build][dockerhub-aspnetcore-build] 映像编译输出，最后生成优化的 aspnetcore 映像。

在克隆的源中，[Dockerfile][dockerfile] 位于 `./AcrHelloworld/Dockerfile`。

```Dockerfile
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

*acr-helloworld* 映像中的应用程序尝试通过在 DNS 中查询有关注册表登录服务器的信息，确定其容器的部署源区域。 必须在 Dockerfile 中的 `DOCKER_REGISTRY` 环境变量内指定注册表登录服务器的完全限定的域名 (FQDN)。

首先，使用 `az acr show` 命令获取注册表的登录服务器。 将 `<acrName>` 替换为在前面步骤中创建的注册表的名称。

```azurecli
az acr show --name <acrName> --query "{acrLoginServer:loginServer}" --output table
```

输出：

```bash
AcrLoginServer
-----------------------------
uniqueregistryname.azurecr.io
```

接下来，使用注册表登录服务器的 FQDN 更新 `ENV DOCKER_REGISTRY` 行。 本示例体现了示例注册表名称，uniqueregistryname  ：

```Dockerfile
ENV DOCKER_REGISTRY uniqueregistryname.azurecr.io
```

## <a name="build-container-image"></a>生成容器映像

使用注册表登录服务器的 FQDN 更新 Dockerfile 之后，可以使用 `docker build` 来创建容器映像。 运行以下命令生成映像，并使用标记将它包含在专用注册表的 URL 中；同样，请将 `<acrName>` 替换为自己的注册表的名称：

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

生成 Docker 映像时，会显示多个输出行（此处的显示内容已截断）：

```bash
Sending build context to Docker daemon  523.8kB
Step 1/18 : FROM microsoft/aspnetcore:2.0 AS base
2.0: Pulling from microsoft/aspnetcore
3e17c6eae66c: Pulling fs layer

[...]

Step 18/18 : ENTRYPOINT dotnet AcrHelloworld.dll
 ---> Running in 6906d98c47a1
 ---> c9ca1763cfb1
Removing intermediate container 6906d98c47a1
Successfully built c9ca1763cfb1
Successfully tagged uniqueregistryname.azurecr.io/acr-helloworld:v1
```

使用 `docker images` 查看生成和标记的映像：

```console
$ docker images
REPOSITORY                                      TAG    IMAGE ID        CREATED               SIZE
uniqueregistryname.azurecr.io/acr-helloworld    v1     01ac48d5c8cf    About a minute ago    284MB
[...]
```

## <a name="push-image-to-azure-container-registry"></a>向 Azure 容器注册表推送映像

然后，使用 `docker push` 命令将 *acr-helloworld* 映像推送到注册表。 将 `<acrName>` 替换为注册表的名称。

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

由于已经为异地复制配置了注册表，因此，使用这一条 `docker push` 命令，即可将映像自动复制到“美国西部”和“美国东部”区域。

```console
$ docker push uniqueregistryname.azurecr.io/acr-helloworld:v1
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
cd54739c444b: Pushed
d6803756744a: Pushed
b7b1f3a15779: Pushed
a89567dff12d: Pushed
59c7b561ff56: Pushed
9a2f9413d9e4: Pushed
a75caa09eb1f: Pushed
v1: digest: sha256:0799014f91384bda5b87591170b1242bcd719f07a03d1f9a1ddbae72b3543970 size: 1792
```

## <a name="next-steps"></a>后续步骤

在本教程中，我们创建了一个专用的异地复制容器注册表，生成了容器映像，然后将该图像推送到了该注册表。

请前往下一教程，了解如何将容器部署到多个用于容器的 Web 应用实例，并使用异地复制在本地提供映像。

> [!div class="nextstepaction"]
> [从 Azure 容器注册表部署 Web 应用](container-registry-tutorial-deploy-app.md)

<!-- IMAGES -->
[tut-portal-01]: ./media/container-registry-tutorial-prepare-registry/tut-portal-01.png
[tut-portal-02]: ./media/container-registry-tutorial-prepare-registry/tut-portal-02.png
[tut-portal-03]: ./media/container-registry-tutorial-prepare-registry/tut-portal-03.png
[tut-portal-04]: ./media/container-registry-tutorial-prepare-registry/tut-portal-04.png
[tut-portal-05]: ./media/container-registry-tutorial-prepare-registry/tut-portal-05.png
[tut-app-01]: ./media/container-registry-tutorial-prepare-registry/tut-app-01.png
[tut-map-01]: ./media/container-registry-tutorial-prepare-registry/tut-map-01.png

<!-- LINKS - External -->
[acr-helloworld-zip]: https://github.com/Azure-Samples/acr-helloworld/archive/master.zip
[aspnet-core]: https://dot.net
[dockerhub-aspnetcore]: https://hub.docker.com/r/microsoft/aspnetcore/
[dockerhub-aspnetcore-build]: https://store.docker.com/community/images/microsoft/aspnetcore-build
[dockerfile]: https://github.com/Azure-Samples/acr-helloworld/blob/master/AcrHelloworld/Dockerfile