---
title: 从应用生成 Azure 容器注册表映像
description: 使用 az acr pack build 命令从应用生成容器映像, 并推送到 Azure 容器注册表, 无需使用 Dockerfile。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 08/06/2019
ms.author: danlep
ms.openlocfilehash: 4e41bcaff8faef2c4eaec9ae852955d4b7ce354b
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839912"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>使用云本机 Buildpack 从应用生成并推送映像

Azure CLI 命令`az acr pack build`使用[Buildpacks](https://buildpacks.io/)中[`pack`](https://github.com/buildpack/pack)的 CLI 工具来生成应用, 并将其映像推送到 Azure 容器注册表中。 此功能提供了一个选项, 可用于从 node.js、Java 和其他语言中的应用程序源代码快速构建容器映像, 而无需定义 Dockerfile。

您可以使用 Azure CLI 的 Azure Cloud Shell 或本地安装来运行本文中的示例。 如果要在本地使用, 则需要版本2.0.70 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

> [!IMPORTANT]
> 此功能目前处于预览状态。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

## <a name="use-the-build-command"></a>使用生成命令

若要使用云本机 Buildpacks 生成并推送容器映像, 请运行[az acr pack build][az-acr-pack-build]命令。 [Az acr build][az-acr-build]命令从 Dockerfile 源和相关代码生成并推送图像, 同时`az acr pack build`直接指定应用程序源树。

至少需要在运行`az acr pack build`时指定以下内容:

* 运行命令的 Azure 容器注册表
* 生成的映像的映像名称和标记
* ACR 任务[支持的上下文位置](container-registry-tasks-overview.md#quick-task)之一, 例如本地目录、GitHub 存储库或远程 tarball
* Buildpack 生成器图像的名称, 如`cloudfoundry/cnb:bionic`。  

`az acr pack build`支持 ACR 任务命令的其他功能, 包括流式传输并保存的[运行变量](container-registry-tasks-reference-yaml.md#run-variables)和[任务运行日志](container-registry-tasks-overview.md#view-task-logs), 以便以后检索。

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>例如：用 Cloud Foundry 生成器生成 node.js 映像

下面的示例使用`cloudfoundry/cnb:bionic`生成器在[Azure-Samples/nodejs-hello world](https://github.com/Azure-Samples/nodejs-docs-hello-world)存储库中从 node.js 应用生成容器映像:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --pull --builder cloudfoundry/cnb:bionic \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

此示例生成`node-app` `1.0`带有标记的图像, 并将其推送到*myregistry*容器注册表。 此处, 将目标注册表名称显式预置到映像名称。 如果未指定, 则会自动将注册表 URL 附加到映像名称之前。

`--pull`参数指定命令提取最新的生成器图像。

命令输出显示生成和推送映像的进度。 

成功生成映像后, 可以使用 Docker 运行它 (如果已安装)。 首先登录到注册表:

```azurecli
az acr login --name myregistry
```

运行映像:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

在你`localhost:1337`喜爱的浏览器中浏览到, 查看示例 web 应用。 按`[Ctrl]+[C]`以停止容器。

## <a name="example-build-java-image-with-heroku-builder"></a>例如：用 Heroku 生成器生成 Java 映像

下面的示例使用`heroku/buildpacks:18`生成器在[buildpack/sample](https://github.com/buildpack/sample-java-app) --应用存储库中从 java 应用生成容器映像:

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

此示例生成`java-app`用命令的运行 ID 标记的映像, 并将其推送到*myregistry*容器注册表。

`--pull`参数指定命令提取最新的生成器图像。

命令输出显示生成和推送映像的进度。 

成功生成映像后, 可以使用 Docker 运行它 (如果已安装)。 首先登录到注册表:

```azurecli
az acr login --name myregistry
```

运行图像, 将*runid*的图像标记替换为:

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

在你`localhost:8080`喜爱的浏览器中浏览到, 查看示例 web 应用。 按`[Ctrl]+[C]`以停止容器。


## <a name="next-steps"></a>后续步骤

使用`az acr pack build`构建容器映像并将其推送后, 可以像将任何映像一样部署到所选目标。 Azure 部署选项包括在[应用服务](../app-service/containers/tutorial-custom-docker-image.md)或[azure Kubernetes 服务](../aks/tutorial-kubernetes-deploy-cluster.md)中运行它, 等等。

有关 ACR 任务功能的详细信息, 请参阅[通过 Acr 任务自动生成和维护容器映像](container-registry-tasks-overview.md)。


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
