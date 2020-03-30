---
title: 使用云本机构建包构建映像
description: 使用 az acr pack 生成命令从应用生成容器映像，然后推送到 Azure 容器注册表，而无需使用 Dockerfile。
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: c42bde6bbab5973094302a2d41f004d7600bdf9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087070"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>使用云本机构建包从应用生成和推送图像

Azure CLI`az acr pack build`命令使用[`pack`](https://github.com/buildpack/pack)来自[Buildpack](https://buildpacks.io/)的 CLI 工具来构建应用并将其映像推送到 Azure 容器注册表中。 此功能提供了一个选项，用于从 Node.js、Java 和其他语言中的应用程序源代码快速构建容器映像，而无需定义 Dockerfile。

可以使用 Azure 云外壳或 Azure CLI 的本地安装来运行本文中的示例。 如果您想在本地使用它，则需要版本 2.0.70 或更高版本。 运行 `az --version` 即可查找版本。 如果需要安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

> [!IMPORTANT]
> 此功能目前处于预览状态。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

## <a name="use-the-build-command"></a>使用生成命令

要使用云本机构建包构建和推送容器映像，请使用[az acr 包生成][az-acr-pack-build]命令。 az [acr 生成][az-acr-build]命令从 Dockerfile 源和相关代码生成和推送图像，`az acr pack build`而您直接指定应用程序源树。

至少，在运行`az acr pack build`： 时指定以下内容：

* 运行命令的 Azure 容器注册表
* 生成的图像的图像名称和标记
* ACR 任务（如本地目录、GitHub 存储库或远程 tarball）[支持的上下文位置](container-registry-tasks-overview.md#context-locations)之一
* 适合您的应用程序的构建器生成器映像的名称。 Azure 容器注册表缓存生成器映像，例如`cloudfoundry/cnb:0.0.34-cflinuxfs3`为了加快生成速度。  

`az acr pack build`支持 ACR Task 命令的其他功能，包括流式处理并保存以供以后检索[的运行变量](container-registry-tasks-reference-yaml.md#run-variables)和[任务运行日志](container-registry-tasks-logs.md)。

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>示例：使用云铸造生成器构建 Node.js 映像

下面的示例使用`cloudfoundry/cnb:0.0.34-cflinuxfs3`生成器在[Azure-示例/nodejs-doc-hello-world](https://github.com/Azure-Samples/nodejs-docs-hello-world)样里库中从 Node.js 应用生成容器映像。 此生成器由 Azure 容器注册表缓存，因此不需要`--pull`参数：

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:0.0.34-cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

本示例使用`1.0`标记`node-app`生成映像，并将其推送到*myREGISTRY*容器注册表。 在此示例中，目标注册表名称显式预置到映像名称。 如果未指定，注册表登录服务器名称将自动预告到映像名称。

命令输出显示映像的生成和推送进度。 

成功生成映像后，如果安装了 Docker，则可以使用 Docker 运行映像。 首次登录注册表：

```azurecli
az acr login --name myregistry
```

运行映像：

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

浏览到`localhost:1337`您最喜爱的浏览器以查看示例 Web 应用。 按`[Ctrl]+[C]`以停止容器。

## <a name="example-build-java-image-with-heroku-builder"></a>示例：使用 Heroku 生成器构建 Java 映像

下面的示例使用`heroku/buildpacks:18`生成器在[buildpack/示例 java 应用](https://github.com/buildpack/sample-java-app)回购中从 Java 应用生成容器映像。 参数`--pull`指定命令应拉最新的生成器映像。 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

本示例生成使用`java-app`命令的运行 ID 标记的映像，并将其推送到*myREGISTRY*容器注册表。

命令输出显示映像的生成和推送进度。 

成功生成映像后，如果安装了 Docker，则可以使用 Docker 运行映像。 首次登录注册表：

```azurecli
az acr login --name myregistry
```

运行图像，将图像标记替换为*runid*：

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

浏览到`localhost:8080`您最喜爱的浏览器以查看示例 Web 应用。 按`[Ctrl]+[C]`以停止容器。


## <a name="next-steps"></a>后续步骤

使用`az acr pack build`生成和推送容器映像后，可以像将任何映像一样部署到您选择的目标。 Azure 部署选项包括在[应用服务](../app-service/containers/tutorial-custom-docker-image.md)或[Azure 库伯奈斯服务](../aks/tutorial-kubernetes-deploy-cluster.md)中运行它，等等。

有关 ACR 任务功能的详细信息，请参阅[使用 ACR 任务自动生成和维护容器映像](container-registry-tasks-overview.md)。


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
