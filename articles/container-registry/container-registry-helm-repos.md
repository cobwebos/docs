---
title: 存储 Helm 图
description: 了解如何使用 Azure 容器注册表中的存储库来存储 Kubernetes 应用程序的 Helm 图表
ms.topic: article
ms.date: 01/28/2020
ms.openlocfilehash: 7969efe37558fffb26b983131c56ae11f3ef9368
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78398974"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>将 Helm 图表推送和拉取到 Azure 容器注册表

若要快速管理和部署 Kubernetes 的应用程序，可以使用[开源 Helm 程序包管理器][helm]。 使用 Helm，将应用程序包定义为[图表](https://helm.sh/docs/topics/charts/)，并将其收集并存储在[Helm 图表存储库](https://helm.sh/docs/topics/chart_repository/)中。

本文介绍如何使用 Helm 3 或 Helm 2 安装在 Azure 容器注册表中的存储库中托管 Helm 图表。 在此示例中，将从公共 Helm*稳定*存储库存储现有的 Helm 图表。 在许多情况下，您将为您开发的应用程序构建并上传您自己的图表。 有关如何生成自己的 Helm 图表的详细信息，请参阅[图表模板开发人员指南][develop-helm-charts]。

> [!IMPORTANT]
> Azure 容器注册表中的 Helm 图表支持目前以预览版提供。 如果你同意补充[使用条款][terms-of-use]，则会向你提供预览。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

## <a name="helm-3-or-helm-2"></a>Helm 3 或 Helm 2？

若要存储、管理和安装 Helm 图表，请使用 Helm 客户端和 Helm CLI。 Helm 客户端的主要版本包括 Helm 3 和 Helm 2。 Helm 3 支持新的图表格式，且不再安装 Tiller 服务器端组件。 有关版本差异的详细信息，请参阅[版本常见问题解答](https://helm.sh/docs/faq/)。 如果你之前已部署 Helm 2 图表，请参阅[将 Helm V2 迁移到 v3](https://helm.sh/docs/topics/v2_v3_migration/)。

你可以使用 Helm 3 或 Helm 2 来托管 Azure 容器注册表中的 Helm 图表，其中包含特定于每个版本的工作流：

* [Helm 3 客户](#use-the-helm-3-client)端-使用 `helm chart` 命令，将注册表中的图表作为[OCI 项目](container-registry-image-formats.md#oci-artifacts)管理
* [Helm 2 客户](#use-the-helm-2-client)端-在 Azure CLI 中使用[az acr Helm][az-acr-helm]命令将容器注册表作为 Helm 图表存储库添加和管理

### <a name="additional-information"></a>其他信息

* 建议结合使用 Helm 3 工作流和本机 `helm chart` 命令将图表作为 OCI 项目来管理。
* 可以在 Helm 3 客户端和图表中使用旧版[az acr helm][az-acr-helm] Azure CLI 命令和工作流。 但是，某些命令（如 `az acr helm list`）与 Helm 3 图表不兼容。
* 从 Helm 3 开始，支持[az acr Helm][az-acr-helm]命令，主要是为了与 Helm 2 客户端和图表格式兼容。 目前尚不打算开发这些命令。

## <a name="use-the-helm-3-client"></a>使用 Helm 3 客户端

### <a name="prerequisites"></a>必备条件

- Azure 订阅中**的 azure 容器注册表**。 如果需要，请使用[Azure 门户](container-registry-get-started-portal.md)或[Azure CLI](container-registry-get-started-azure-cli.md)创建注册表。
- **Helm client 版本3.0.0 或更高版本**-运行 `helm version` 以查找当前版本。 有关如何安装和升级 Helm 的详细信息，请参阅[安装 Helm][helm-install]。
- **Kubernetes 群集**，你将在其中安装 Helm 图表。 如果需要，创建[Azure Kubernetes 服务群集][aks-quickstart]。 
- **Azure CLI 版本2.0.71 或更高版本**-运行 `az --version` 查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

### <a name="high-level-workflow"></a>高级别工作流

在**Helm 3**中：

* 可以在 Azure 容器注册表中创建一个或多个 Helm 存储库
* 在注册表中将 Helm 3 图表存储为[OCI 项目](container-registry-image-formats.md#oci-artifacts)。 目前，Helm 3 对 OCI 的支持被视为*实验*性。
* 直接从 Helm CLI 使用 `helm chart` 命令来推送、请求和管理注册表中的 Helm 图表
* 通过 Azure CLI 向注册表进行身份验证，然后将通过注册表 URI 和凭据自动更新 Helm 客户端。 不需要手动指定此注册表信息，因此不会在命令历史记录中公开凭据。
* 使用 `helm install` 从本地存储库缓存将图表安装到 Kubernetes 群集。

有关示例，请参阅以下各节。

### <a name="enable-oci-support"></a>启用 OCI 支持

设置以下环境变量以启用 Helm 3 客户端中的 OCI 支持。 目前，此支持是实验性的。 

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="pull-an-existing-helm-package"></a>请求现有的 Helm 包

如果尚未添加 `stable` Helm 图表存储库，请运行 `helm repo add` 命令：

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com
```

从本地存储库中提取图表包 `stable`。 例如，创建一个本地目录（例如 *~/acr-helm*），然后下载现有的*稳定/wordpress*图表包。 （本文中的此示例和其他命令的格式适用于 Bash shell。）

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm pull stable/wordpress --untar
```

`helm pull stable/wordpress` 命令未指定特定版本，因此已在 `wordpress` 子目录中拉取并解压缩*最新*版本。

### <a name="save-chart-to-local-registry-cache"></a>将图表保存到本地注册表缓存

将目录更改为 `wordpress` 子目录，其中包含 Helm 图表文件。 然后，运行 `helm chart save` 在本地保存图表的副本，并使用注册表的完全限定名称和目标存储库和标记创建别名。 

在以下示例中，注册表名称为*mycontainerregistry*，目标存储库为*wordpress*，目标图表标记是*最新*的，但会将值替换为你的环境：

```console
cd wordpress
helm chart save . wordpress:latest
helm chart save . mycontainerregistry.azurecr.io/helm/wordpress:latest
```

运行 `helm chart list` 以确认是否已将图表保存在本地注册表缓存中。 输出类似于：

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
wordpress:latest                                         wordpress       8.1.0   5899db0 29.1 KiB        1 day 
mycontainerregistry.azurecr.io/helm/wordpress:latest     wordpress       8.1.0   5899db0 29.1 KiB        1 day 
```

### <a name="push-chart-to-azure-container-registry"></a>将图表推送到 Azure 容器注册表

在 Helm 3 CLI 中运行 `helm chart push` 命令，将 Helm 图表推送到 Azure 容器注册表中的存储库。 如果它不存在，则创建存储库。

首先，使用 Azure CLI 命令[az acr login][az-acr-login]来向注册表进行身份验证：

```azurecli
az acr login --name mycontainerregistry
```

将该图表推送到完全限定的目标存储库：

```console
helm chart push mycontainerregistry.azurecr.io/helm/wordpress:latest
```

成功推送后，输出类似于：

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/wordpress]
ref:     mycontainerregistry.azurecr.io/helm/wordpress:latest
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    29.1 KiB
name:    wordpress
version: 8.1.0
```

### <a name="list-charts-in-the-repository"></a>在存储库中列出图表

与存储在 Azure 容器注册表中的映像一样，你可以使用[az acr 存储库][az-acr-repository]命令来显示承载图表的存储库、图表标记和清单。 

例如，运行[az acr repository show][az-acr-repository-show]可查看在上一步中创建的存储库的属性：

```azurecli
az acr repository show \
  --name mycontainerregistry \
  --repository helm/wordpress
```

输出类似于：

```output
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2020-01-29T16:54:30.1514833Z",
  "imageName": "helm/wordpress",
  "lastUpdateTime": "2020-01-29T16:54:30.4992247Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

运行[az acr repository show-清单][az-acr-repository-show-manifests]命令以查看存储在存储库中的图表的详细信息。 例如：

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/wordpress --detail
```

在此示例中，Output 显示了 `application/vnd.cncf.helm.config.v1+json``configMediaType`：

```output
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2020-01-29T16:54:30.2382436Z",
    "digest": "sha256:xxxxxxxx51bc0807bfa97cb647e493ac381b96c1f18749b7388c24bbxxxxxxxxx",
    "imageSize": 29995,
    "lastUpdateTime": "2020-01-29T16:54:30.3492436Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "latest"
    ]
  }
]
```

### <a name="pull-chart-to-local-cache"></a>将图表拉入本地缓存

若要将 Helm 图安装到 Kubernetes，图表必须位于本地缓存中。 在此示例中，首先运行 `helm chart remove` 以删除名为 `mycontainerregistry.azurecr.io/helm/wordpress:latest`的现有本地图表：

```console
helm chart remove mycontainerregistry.azurecr.io/helm/wordpress:latest
```

运行 `helm chart pull` 将图表从 Azure 容器注册表下载到本地缓存：

```console
helm chart pull mycontainerregistry.azurecr.io/helm/wordpress:latest
```

### <a name="export-helm-chart"></a>导出 Helm 图表

若要进一步使用图表，请使用 `helm chart export`将其导出到本地目录。 例如，将提取的图表导出到 `install` 目录：

```console
helm chart export mycontainerregistry.azurecr.io/helm/wordpress:latest --destination ./install
```

若要查看存储库中导出的图表的信息，请在导出图表的目录中运行 `helm inspect chart` 命令。

```console
cd install
helm inspect chart wordpress
```

如果没有版本号，将使用最新版本。 Helm 返回有关图表的详细信息，如以下简洁输出所示：

```output
apiVersion: v1
appVersion: 5.3.2
dependencies:
- condition: mariadb.enabled
  name: mariadb
  repository: https://kubernetes-charts.storage.googleapis.com/
  tags:
  - wordpress-database
  version: 7.x.x
description: Web publishing platform for building blogs and websites.
home: http://www.wordpress.com/
icon: https://bitnami.com/assets/stacks/wordpress/img/wordpress-stack-220x234.png
keywords:
- wordpress
- cms
- blog
- http
- web
- application
- php
maintainers:
- email: containers@bitnami.com
  name: Bitnami
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 8.1.0
```

### <a name="install-helm-chart"></a>安装 Helm 图表

运行 `helm install` 以安装拉取到本地缓存并导出的 Helm 图表。 指定发布名称或传递 `--generate-name` 参数。 例如：

```console
helm install wordpress --generate-name
```

安装继续时，请按照命令输出中的说明进行操作，以查看 WorPress Url 和凭据。 你还可以运行 `kubectl get pods` 命令来查看通过 Helm 图表部署的 Kubernetes 资源：

```output
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>从存储库删除 Helm 图表

若要从存储库中删除图表，请使用[az acr repository delete][az-acr-repository-delete]命令。 运行以下命令，并在出现提示时确认操作：

```azurecli
az acr repository delete --name mycontainerregistry --image helm/wordpress:latest
```

## <a name="use-the-helm-2-client"></a>使用 Helm 2 客户端

### <a name="prerequisites"></a>必备条件

- Azure 订阅中**的 azure 容器注册表**。 如果需要，请使用[Azure 门户](container-registry-get-started-portal.md)或[Azure CLI](container-registry-get-started-azure-cli.md)创建注册表。
- Helm 客户端版本 2.11.0（而不是 RC 版本）或更高版本 - 运行 `helm version` 以查找当前版本。 还需要在 Kubernetes 群集中初始化的一个 Helm 服务器 (Tiller)。 如果需要，创建[Azure Kubernetes 服务群集][aks-quickstart]。 有关如何安装和升级 Helm 的详细信息，请参阅[安装 Helm][helm-install-v2]。
- Azure CLI 版本 2.0.46 或更高版本- 运行 `az --version` 查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

### <a name="high-level-workflow"></a>高级别工作流

对于**Helm 2** ，你：

* 将 Azure 容器注册表配置为*单一*Helm 图表存储库。 当你在存储库中添加或删除图表时，Azure 容器注册表将管理索引定义。
* 使用 Azure CLI 中的[az acr helm][az-acr-helm]命令将 Azure 容器注册表添加为 helm 图表存储库，并推送和管理图表。 这些 Azure CLI 命令包装 Helm 2 客户端命令。
* 将 Azure 容器注册表中的图表存储库添加到本地 Helm 存储库索引中，支持图表搜索
* 通过 Azure CLI 通过 Azure 容器注册表进行身份验证，然后将通过注册表 URI 和凭据自动更新 Helm 客户端。 不需要手动指定此注册表信息，因此不会在命令历史记录中公开凭据。
* 使用 `helm install` 从本地存储库缓存将图表安装到 Kubernetes 群集。

有关示例，请参阅以下各节。

### <a name="add-repository-to-helm-client"></a>将存储库添加到 Helm 客户端

使用[az acr Helm][az-acr-helm-repo-add]存储库添加命令将 Azure 容器注册表 Helm 图表存储库添加到你的 Helm 客户端。 此命令将为 Helm 客户端使用的 Azure 容器注册表获取身份验证令牌。 身份验证令牌的有效期为3小时。 类似于 `docker login`，可以在以后的 CLI 会话中运行此命令，以对 Helm 客户端的 Azure 容器注册表 Helm 图表存储库进行身份验证：

```azurecli
az acr helm repo add --name mycontainerregistry
```

### <a name="add-a-chart-to-the-repository"></a>将图表添加到存储库中

首先，在 *~/acr-helm*中创建一个本地目录，然后下载现有*稳定/wordpress*图表：

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm repo update
helm fetch stable/wordpress
```

键入 `ls` 以列出下载的图表，并记下文件名中包含的 Wordpress 版本。 `helm fetch stable/wordpress` 命令未指定特定版本，因此会获取最新版本。 在下面的示例输出中，Wordpress 图是版本*8.1.0*：

```output
wordpress-8.1.0.tgz
```

在 Azure CLI 中使用[az acr Helm push][az-acr-helm-push]命令将图表推送到 Azure 容器注册表中的 Helm 图表存储库。 指定在上一步中下载的 Helm 图表的名称，如*wordpress-8.1.0。 tgz*：

```azurecli
az acr helm push --name mycontainerregistry wordpress-8.1.0.tgz
```

几分钟后，Azure CLI 会报告你的图表已保存，如以下示例输出所示：

```output
{
  "saved": true
}
```

### <a name="list-charts-in-the-repository"></a>在存储库中列出图表

若要使用在上一步中上传的图表，必须更新本地 Helm 存储库索引。 可以为 Helm 客户端中的存储库重新创建索引，或使用 Azure CLI 更新存储库索引。 每次将图表添加到存储库时，必须完成此步骤：

```azurecli
az acr helm repo add --name mycontainerregistry
```

如果将图表存储在存储库中并在本地提供更新的索引，可以使用常规 Helm 客户端命令来搜索或安装。 若要查看存储库中的所有图表，请使用 `helm search` 命令，提供自己的 Azure 容器注册表名称：

```console
helm search mycontainerregistry
```

会列出上一步中推送的 Wordpress 图表，如以下示例输出中所示：

```output
NAME                CHART VERSION   APP VERSION DESCRIPTION
helmdocs/wordpress  8.1.0           5.3.2       Web publishing platform for building blogs and websites.
```

还可以使用[az acr helm list][az-acr-helm-list]，使用 Azure CLI 列出图表：

```azurecli
az acr helm list --name mycontainerregistry
```

### <a name="show-information-for-a-helm-chart"></a>显示 Helm 图表的信息

若要查看存储库中特定图表的信息，可以使用 `helm inspect` 命令。

```console
helm inspect mycontainerregistry/wordpress
```

如果没有版本号，将使用最新版本。 Helm 将返回有关图表的详细信息，如以下精简示例输出中所示：

```output
apiVersion: v1
appVersion: 5.3.2
description: Web publishing platform for building blogs and websites.
engine: gotpl
home: http://www.wordpress.com/
icon: https://bitnami.com/assets/stacks/wordpress/img/wordpress-stack-220x234.png
keywords:
- wordpress
- cms
- blog
- http
- web
- application
- php
maintainers:
- email: containers@bitnami.com
  name: Bitnami
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 8.1.0
[...]
```

你还可以使用 Azure CLI [az acr helm show][az-acr-helm-show]命令显示图表信息。 同样，默认情况下会返回图表的最新版本。 可以追加 `--version` 来列出特定版本的图表，如*8.1.0*：

```azurecli
az acr helm show --name mycontainerregistry wordpress
```

### <a name="install-a-helm-chart-from-the-repository"></a>从存储库安装 Helm 图表

存储库中的 Helm 图表是通过指定存储库名称和图表名称来安装的。 使用 Helm 客户端安装 Wordpress 图表：

```console
helm install mycontainerregistry/wordpress
```

> [!TIP]
> 如果推送到 Azure 容器注册表 Helm 图表存储库，稍后在新的 CLI 会话中返回，本地 Helm 客户端需要更新的身份验证令牌。 若要获取新的身份验证令牌，请使用[az acr helm][az-acr-helm-repo-add]存储库 add 命令。

会在安装过程中完成以下步骤：

- Helm 客户端将搜索本地存储库索引。
- 会从 Azure 容器注册表存储库下载相应的图表。
- 使用 Tiller 在 Kubernetes 群集中部署图表。

安装继续时，请按照命令输出中的说明进行操作，以查看 WorPress Url 和凭据。 你还可以运行 `kubectl get pods` 命令来查看通过 Helm 图表部署的 Kubernetes 资源：

```output
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>从存储库删除 Helm 图表

若要从存储库中删除图表，请使用[az acr helm delete][az-acr-helm-delete]命令。 指定图表名称（如*wordpress*）和要删除的版本，如*8.1.0*。

```azurecli
az acr helm delete --name mycontainerregistry wordpress --version 8.1.0
```

如果想要删除已命名图表的所有版本，请忽略 `--version` 参数。

运行 `helm search`时，将继续返回图表。 同样，Helm 客户端不会自动更新存储库中可用图表的列表。 若要更新 Helm 客户端存储库索引，请再次使用[az acr Helm][az-acr-helm-repo-add]存储库添加命令：

```azurecli
az acr helm repo add --name mycontainerregistry
```

## <a name="next-steps"></a>后续步骤

本文使用了从公共稳定存储库中获取的现有 Helm 图表。 有关如何创建和部署 Helm 图表的详细信息，请参阅[开发 Helm 图表][develop-helm-charts]。

Helm 图表可以用作容器生成过程的一部分。 有关详细信息，请参阅[使用 Azure 容器注册表任务][acr-tasks]。

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-v2]: https://v2.helm.sh/docs/using_helm/#installing-helm
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/
[semver2]: https://semver.org/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm]: /cli/azure/acr/helm
[az-acr-repository]: /cli/azure/acr/repository
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-helm-repo-add]: /cli/azure/acr/helm/repo#az-acr-helm-repo-add
[az-acr-helm-push]: /cli/azure/acr/helm#az-acr-helm-push
[az-acr-helm-list]: /cli/azure/acr/helm#az-acr-helm-list
[az-acr-helm-show]: /cli/azure/acr/helm#az-acr-helm-show
[az-acr-helm-delete]: /cli/azure/acr/helm#az-acr-helm-delete
[acr-tasks]: container-registry-tasks-overview.md
