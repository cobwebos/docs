---
title: 推送和拉取 OCI 项目
description: 在 Azure 中使用专用容器注册表推送和拉取开放容器计划 (OCI) 项目
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 08/30/2019
ms.author: stevelas
ms.openlocfilehash: cb58a7ed51ae15d33ffdbb616c9b32ef03bcbfb7
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456258"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>使用 Azure 容器注册表推送和拉取 OCI 项目

可以使用 Azure 容器注册表来存储和管理[开放容器计划 (OCI) 项目](container-registry-image-formats.md#oci-artifacts)、Docker 以及与 Docker 兼容的容器映像。

为了演示此功能，本文介绍了如何使用 [OCI 注册表即存储 (ORAS)](https://github.com/deislabs/oras) 工具将示例项目（一个文本文件）推送到 Azure 容器注册表， 然后从注册表拉取项目。 可以使用适用于每个 OCI 项目的不同命令行工具，在 Azure 容器注册表中管理各种 OCI 项目。

## <a name="prerequisites"></a>先决条件

* **Azure 容器注册表** - 在 Azure 订阅中创建容器注册表。 例如，使用 [Azure 门户](container-registry-get-started-portal.md)或 [Azure CLI](container-registry-get-started-azure-cli.md)。
* **ORAS 工具** - 从 [GitHub 存储库](https://github.com/deislabs/oras/releases)下载并安装适合操作系统的最新 ORAS 版本。 此工具以压缩 tarball（`.tar.gz` 文件）形式发布。 使用适合操作系统的标准过程提取并安装该文件。
* **Azure Active Directory 服务主体（可选）** - 若要使用 ORAS 直接进行身份验证，请创建一个用于访问注册表的[服务主体](container-registry-auth-service-principal.md)。 请确保为服务主体分配一个角色（例如 AcrPush），使之有权推送和拉取项目。
* **Azure CLI（可选）** - 若要使用单个标识，需在本地安装 Azure CLI。 建议使用 2.0.71 或更高版本。 运行 `az --version `即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。
* **Docker（可选）** - 若要使用单个标识，还必须在本地安装 Docker，以便通过注册表进行身份验证。 Docker 提供的包可在任何 [macOS][docker-mac]、[Windows][docker-windows] 或 [Linux][docker-linux] 系统上轻松配置 Docker。


## <a name="sign-in-to-a-registry"></a>登录到注册表

此部分介绍两个建议用于登录注册表的工作流，具体取决于所用标识。 选择适合环境的方法。

### <a name="sign-in-with-oras"></a>使用 ORAS 登录

使用带推送权限的[服务主体](container-registry-auth-service-principal.md)时，请运行 `oras login` 命令，以便使用服务主体应用程序 ID 和密码登录到注册表。 指定完全限定的注册表名称（全部小写），在本例中为*myregistry.azurecr.io*。 服务主体应用程序 ID 将传入到环境变量 `$SP_APP_ID` 中，密码将传入到变量 `$SP_PASSWD` 中。

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

若要从 Stdin 读取密码，请使用 `--password-stdin`。

### <a name="sign-in-with-azure-cli"></a>使用 Azure CLI 登录

使用标识[登录](/cli/azure/authenticate-azure-cli)到 Azure CLI，以便通过容器注册表推送和拉取项目。

然后，使用 Azure CLI 命令 [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) 访问注册表。 例如，若要向名为 *myregistry* 的注册表进行身份验证，请执行以下命令：

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login` 使用 Docker 客户端在 `docker.config` 文件中设置 Azure Active Directory 令牌。 Docker 客户端必须已安装并处于运行状态，否则不能完成单个身份验证流。

## <a name="push-an-artifact"></a>推送项目

请使用一些示例文本在本地工作目录中创建一个文本文件。 例如，在 Bash shell 中执行以下代码：

```bash
echo "Here is an artifact!" > artifact.txt
```

使用 `oras push` 命令将该文本文件推送到注册表。 以下示例将示例文本文件推送到 `samples/artifact` 存储库。 注册表用完全限定的注册表名称*myregistry.azurecr.io* （全部小写）标识。 此项目标记为 `1.0`。 默认情况下，此项目有一个未定义的类型，该类型通过文件名  *后的媒体类型*`artifact.txt`字符串进行标识。 有关其他类型，请参阅 [OCI Artifacts](https://github.com/opencontainers/artifacts)（OCI 项目）。 

```bash
oras push myregistry.azurecr.io/samples/artifact:1.0 \
    --manifest-config /dev/null:application/vnd.unknown.config.v1+json \
    ./artifact.txt:application/vnd.unknown.layer.v1+txt
```

成功推送后，输出将如下所示：

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

如果使用 Azure CLI，则若要管理注册表中的项目，请运行标准的用于管理映像的 `az acr` 命令。 例如，使用 [az acr repository show][az-acr-repository-show] 命令获取项目的属性：

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

## <a name="pull-an-artifact"></a>拉取项目

请运行 `oras pull` 命令从注册表拉取项目。

首先，从本地工作目录中删除以下文本文件：

```bash
rm artifact.txt
```

运行 `oras pull` 来拉取项目，并指定用于推送项目的媒体类型：

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

验证拉取是否成功：

```bash
$ cat artifact.txt
Here is an artifact!
```

## <a name="remove-the-artifact-optional"></a>删除项目（可选）

若要从 Azure 容器注册表中删除项目，请使用 [az acr repository delete][az-acr-repository-delete] 命令。 以下示例删除存储在该处的项目：

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="next-steps"></a>后续步骤

* 详细了解 [ORAS 库](https://github.com/deislabs/oras/tree/master/docs)，包括如何为项目配置清单。
* 有关新项目类型的参考信息，请访问 [OCI 项目](https://github.com/opencontainers/artifacts)存储库



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
