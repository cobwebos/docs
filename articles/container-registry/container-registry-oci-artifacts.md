---
title: 将 OCI 项目推送到专用 Azure 容器注册表
description: 使用 Azure 中的专用容器注册表推送和拉取开放式容器计划 (OCI) 项目
services: container-registry
author: SteveLasker
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 08/30/2019
ms.author: stevelas
ms.custom: ''
ms.openlocfilehash: 69423f85aecdc3f8049a7e784888e1f71d0bc702
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182704"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>使用 Azure 容器注册表推送和拉取 OCI 项目

可以使用 Azure 容器注册表来存储和管理[开放式容器计划 (OCI) 项目](container-registry-image-formats.md#oci-artifacts), 以及 Docker 和 docker 兼容的容器映像。

为了演示此功能, 本文介绍了如何使用[OCI 注册表作为存储 (ORAS)](https://github.com/deislabs/oras)工具将示例项目-文本文件推送到 Azure 容器注册表。 然后, 从注册表中请求项目。 你可以使用适用于每个项目的不同命令行工具管理 Azure 容器注册表中的各种 OCI 项目。

## <a name="prerequisites"></a>先决条件

* **Azure 容器注册表** - 在 Azure 订阅中创建容器注册表。 例如，使用 [Azure 门户](container-registry-get-started-portal.md)或 [Azure CLI](container-registry-get-started-azure-cli.md)。
* **ORAS 工具**-从[GitHub](https://github.com/deislabs/oras/releases)存储库下载并安装适用于你的操作系统的当前 ORAS 版本。 该工具以压缩的 tarball (`.tar.gz`文件) 形式发布。 使用适用于你的操作系统的标准过程提取并安装该文件。
* **Azure Active Directory 服务主体 (可选)** -若要直接使用 ORAS 进行身份验证, 请创建[服务主体](container-registry-auth-service-principal.md)以访问注册表。 确保为服务主体分配了角色 (如 AcrPush), 以便它具有推送和拉取项目的权限。
* **Azure CLI (可选)** -若要使用单个标识, 需要 Azure CLI 的本地安装。 建议使用2.0.71 或更高版本。 运行`az --version `以查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。
* **Docker (可选)** -若要使用单个标识, 还必须在本地安装 Docker, 才能使用注册表进行身份验证。 Docker 提供的包可在任何 [macOS][docker-mac]、[Windows][docker-windows] 或 [Linux][docker-linux] 系统上轻松配置 Docker。


## <a name="sign-in-to-a-registry"></a>登录到注册表

本部分介绍了两个建议的工作流, 这些工作流根据所使用的标识登录到注册表。 选择适用于您的环境的方法。

### <a name="sign-in-with-oras"></a>用 ORAS 登录

使用具有推送权限的[服务主体](container-registry-auth-service-principal.md), 运行`oras login`命令, 使用服务主体应用程序 ID 和密码登录到注册表。 指定完全限定的注册表名称 (全部小写), 在本例中为*myregistry.azurecr.io*。 在环境变量`$SP_APP_ID`中传递服务主体应用程序 ID, 并在变量`$SP_PASSWD`中传递密码。

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

若要从 Stdin 中读取密码, `--password-stdin`请使用。

### <a name="sign-in-with-azure-cli"></a>使用 Azure CLI 登录

以你的身份[登录](/cli/azure/authenticate-azure-cli)到 Azure CLI, 以便从容器注册表推送和请求项目。

然后, 使用 Azure CLI 命令[az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login)来访问注册表。 例如, 若要对名为*myregistry*的注册表进行身份验证:

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login`使用 Docker 客户端在`docker.config`文件中设置 Azure Active Directory 令牌。 必须安装并运行 Docker 客户端才能完成单个身份验证流。

## <a name="push-an-artifact"></a>推送项目

用一些示例文本在本地工作目录中创建文本文件。 例如, 在 bash shell 中:

```bash
echo "Here is an artifact!" > artifact.txt
```

`oras push`使用命令将此文本文件推送到注册表。 下面的示例将示例文本文件推送到`samples/artifact`存储库。 注册表用完全限定的注册表名称*myregistry.azurecr.io* (全部小写) 标识。 项目已标记`1.0`。 默认情况下, 该项目具有未定义的类型, 该类型由文件名`artifact.txt`后面的*媒体类型*字符串标识。 有关其他类型, 请参阅[OCI 项目](https://github.com/opencontainers/artifacts)。 

```bash
oras push myregistry.azurecr.io/samples/artifact:1.0 \
    --manifest-config /dev/null:application/vnd.unknown.config.v1+json \
    ./artifact.txt:application/vnd.unknown.layer.v1+txt
```

成功推送的输出类似于以下内容:

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

若要在注册表中管理项目, 如果使用的是 Azure CLI, 请运行`az acr`用于管理映像的标准命令。 例如, 使用[az acr repository show][az-acr-repository-show]命令获取项目的属性:

```azurecli
az acr repository show \
    --name myregistry \
    --image samples/artifact:1.0
```

输出与下面类似：

```json
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2019-08-28T20:43:31.0001687Z",
  "digest": "sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx",
  "lastUpdateTime": "2019-08-28T20:43:31.0001687Z",
  "name": "1.0",
  "signed": false
}
```

## <a name="pull-an-artifact"></a>请求项目

`oras pull`运行命令, 从注册表中请求项目。

首先从本地工作目录中删除该文本文件:

```bash
rm artifact.txt
```

运行`oras pull`以请求项目, 并指定用于推送项目的媒体类型:

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

验证拉取是否成功:

```bash
$ cat artifact.txt
Here is an artifact!
```

## <a name="remove-the-artifact-optional"></a>删除项目 (可选)

若要从 Azure 容器注册表中删除项目, 请使用[az acr repository delete][az-acr-repository-delete]命令。 下面的示例删除存储在其中的项目:

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="next-steps"></a>后续步骤

* 了解有关[ORAS 库的](https://github.com/deislabs/oras/tree/master/docs)详细信息, 包括如何为项目配置清单
* 有关新项目类型的参考信息, 请访问[OCI 项目](https://github.com/opencontainers/artifacts)存储库



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
