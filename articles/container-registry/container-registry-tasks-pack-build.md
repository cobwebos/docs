---
title: 使用 Cloud Native Buildpack 生成映像
description: 在不使用 Dockerfile 的情况下，使用 az acr pack build 命令从应用生成容器映像并将其推送到 Azure 容器注册表。
ms.topic: article
ms.date: 10/24/2019
ms.custom: devx-track-js
ms.openlocfilehash: cad7d5c066dd8c8a7468833cb67e6085f067417a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91262104"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>使用 Cloud Native Buildpack 从应用生成映像并推送该映像

Azure CLI 命令 `az acr pack build` 使用 [`pack`](https://github.com/buildpack/pack) CLI 工具（来自 [Buildpack](https://buildpacks.io/)）生成应用，并将此应用的映像推送到 Azure 容器注册表中。 此功能提供一个选项，用于从以 Node.js、Java 和其他语言编写的应用程序源代码快速生成容器映像，而无需定义 Dockerfile。

您可以使用 Azure CLI 的 Azure Cloud Shell 或本地安装来运行本文中的示例。 若要在本地使用 Azure CLI，需要安装 2.0.70 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

> [!IMPORTANT]
> 此功能目前以预览版提供。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

## <a name="use-the-build-command"></a>使用 build 命令

若要使用 Cloud Native Buildpack 生成并推送容器映像，请运行 [az acr pack build][az-acr-pack-build] 命令。 [az acr build][az-acr-build] 命令从 Dockerfile 源和相关代码生成并推送映像，而使用 `az acr pack build` 可直接指定应用程序源树。

运行 `az acr pack build` 时，请至少指定以下各项：

* 在其中运行命令的 Azure 容器注册表
* 生成的映像的映像名称和标记
* ACR 任务的[受支持上下文位置](container-registry-tasks-overview.md#context-locations)之一，例如本地目录、GitHub 存储库或远程 tarball
* 适合你的应用程序的 Buildpack 生成器映像的名称。 为加快生成速度，Azure 容器注册表会缓存 `cloudfoundry/cnb:0.0.34-cflinuxfs3` 等生成器映像。  

`az acr pack build` 支持 ACR 任务命令的其他功能，包括 [run 变量](container-registry-tasks-reference-yaml.md#run-variables)以及[任务运行日志](container-registry-tasks-logs.md)（这些日志会进行流式传输，还会进行保存供以后检索）。

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>示例：使用 Cloud Foundry 生成器生成 Node.js 映像

以下示例使用 `cloudfoundry/cnb:0.0.34-cflinuxfs3` 生成器，从 [Azure-Samples/nodejs-docs-hello-world](https://github.com/Azure-Samples/nodejs-docs-hello-world) 存储库中的 Node.js 应用生成容器映像。 Azure 容器注册表会缓存此生成器，因此无需指定 `--pull` 参数：

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:0.0.34-cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

此示例生成具有 `1.0` 标记的 `node-app` 映像，并将其推送到 myregistry 容器注册表中  。 在此示例中，已将目标注册表名称显式追加到映像名称的前面。 如果未指定目标注册表名称，则会自动将注册表登录服务器名称追加到映像名称的前面。

命令输出显示了生成和推送映像的进度。 

成功生成映像后，可以使用 Docker（如果已安装）运行该映像。 首先登录到注册表：

```azurecli
az acr login --name myregistry
```

运行映像：

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

在你常用的浏览器中浏览到 `localhost:1337`，以查看示例 Web 应用。 按 `[Ctrl]+[C]` 停止容器。

## <a name="example-build-java-image-with-heroku-builder"></a>示例：使用 Heroku 生成器生成 Java 映像

以下示例使用 `heroku/buildpacks:18` 生成器从 [buildpack/sample-java-app](https://github.com/buildpack/sample-java-app) 存储库中的 Java 应用生成容器映像。 `--pull` 参数指定命令应提取最新的生成器映像。 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

此示例生成使用命令的运行 ID 标记的 `java-app` 映像，并将其推送到 myregistry 容器注册表中  。

命令输出显示了生成和推送映像的进度。 

成功生成映像后，可以使用 Docker（如果已安装）运行该映像。 首先登录到注册表：

```azurecli
az acr login --name myregistry
```

运行映像（请将 runid 替换为你的映像标记）  ：

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

在你常用的浏览器中浏览到 `localhost:8080`，以查看示例 Web 应用。 按 `[Ctrl]+[C]` 停止容器。


## <a name="next-steps"></a>后续步骤

使用 `az acr pack build` 生成并推送容器映像后，可以像部署任何其他映像一样将其部署到所选目标。 Azure 部署选项包括在 [应用服务](../app-service/tutorial-custom-container.md) 或 [azure Kubernetes 服务](../aks/tutorial-kubernetes-deploy-cluster.md)中运行它，等等。

有关 ACR 任务功能的详细信息，请参阅[使用 ACR 任务自动执行容器映像的生成和维护](container-registry-tasks-overview.md)。


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
