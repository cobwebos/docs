---
title: 使用 Azure 容器注册表中的 Helm 存储库
description: 了解如何使用 Azure 容器注册表中的 Helm 存储库来存储你的应用程序的图表
services: container-registry
author: iainfoulds
ms.service: container-registry
ms.topic: article
ms.date: 09/24/2018
ms.author: iainfou
ms.openlocfilehash: ba0e1386d67e920f1805d244f9042044bb462ec9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "62109845"
---
# <a name="use-azure-container-registry-as-a-helm-repository-for-your-application-charts"></a>使用 Azure 容器注册表作为应用程序图表的 Helm 存储库

若要快速地管理和部署适用于 Kubernetes 的应用程序，可以使用[开放源代码 Helm 包管理器][helm]。 使用 Helm，应用程序定义为存储在 Helm 图表存储库中的*图表*。 这些图表定义配置和依赖项，并可在整个应用程序生命周期内进行版本控制。 Azure 容器注册表可以用作 Helm 图表存储库的主机。

使用 Azure 容器注册表，你便具有安全的专用 Helm 图表存储库，可以与生成管道或其他 Azure 服务集成。 Azure 容器注册表中的 Helm 图表存储库包括异地复制功能，以让图表接近部署以及进行冗余。 你只需对图表所用的存储进行付费，便适用于所有 Azure 容器注册表价格层。

本文介绍如何使用 Azure 容器注册表中存储的 Helm 图表存储库。

> [!IMPORTANT]
> 此功能目前处于预览状态。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

## <a name="before-you-begin"></a>开始之前

若要完成本文中的步骤，必须满足以下先决条件：

- Azure 容器注册表  - 在 Azure 订阅中创建容器注册表。 例如，使用 [Azure 门户](container-registry-get-started-portal.md)或 [Azure CLI](container-registry-get-started-azure-cli.md)。
- Helm 客户端版本 2.11.0（而不是 RC 版本）或更高版本  - 运行 `helm version` 以查找当前版本。 还需要在 Kubernetes 群集中初始化的一个 Helm 服务器 (Tiller)。 如果需要，可以[创建 Azure Kubernetes 服务群集][aks-quickstart]。 有关如何安装和升级 Helm 的详细信息，请参阅[安装 Helm][helm-install]。
- Azure CLI 版本 2.0.46 或更高版本  - 运行 `az --version` 查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

## <a name="add-a-repository-to-helm-client"></a>将一个存储库添加到 Helm 客户端

Helm 存储库是一个 HTTP 服务器，可以存储 Helm 图表。 Azure 容器注册表可以为 Helm 图表提供此存储，并在向存储库中添加和删除图表时管理索引定义。

若要将 Azure 容器注册表添加为 Helm 图表存储库，请使用 Azure CLI。 使用此方法，便会用 Azure 容器注册表提供支持的存储库的 URI 和凭据更新 Helm 客户端。 比如，不必手动指定此存储库信息，以便凭据不会在命令历史记录中公开。

如果需要，请登录到 Azure CLI 并按照提示进行操作：

```azurecli
az login
```

借助 [az configure][az-configure] 命令，用 Azure 容器注册表的名称配置 Azure CLI 默认值。 在以下示例中，请将 `<acrName>` 替换为注册表的名称：

```azurecli
az configure --defaults acr=<acrName>
```

现在，使用 [az acr helm repo add][az-acr-helm-repo-add] 命令将 Azure 容器注册表 Helm 图表存储库添加到 Helm 客户端。 此命令将为 Helm 客户端使用的 Azure 容器注册表获取身份验证令牌。 身份验证令牌的有效期为 1 小时。 类似于 `docker login`，可以在以后的 CLI 会话中运行此命令，以对 Helm 客户端的 Azure 容器注册表 Helm 图表存储库进行身份验证：

```azurecli
az acr helm repo add
```

## <a name="add-a-chart-to-the-repository"></a>将图表添加到存储库中

在本文中，让我们从公共 Helm 稳定  存储库中获取现有 Helm 图表。 稳定  存储库是组织有序的公共存储库，包括常见的应用程序图表。 包维护人员可以将其图表提交到稳定  存储库中，就像 Docker Hub 向常见容器映像提供公共注册表一样。 然后，可以将从公共稳定  存储库下载的图表推送到专用 Azure 容器注册表存储库。 在大多数情况下，你将为自己开发的应用生成并上传自己的图表。 有关如何生成你自己的 Helm 图表的详细信息，请参阅[开发 Helm 图表][develop-helm-charts]。

首先，在 ~/acr-helm  创建一个目录 ，然后下载现有的 stable/wordpress  图表：

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm fetch stable/wordpress
```

列出已下载的图表，并记下文件名中包含的 Wordpress 版本。 `helm fetch stable/wordpress` 命令未指定特定版本，因此会获取最新  版本。 所有 Helm 图表会在符合 [SemVer 2][semver2] 标准的文件名中包含一个版本号。 在以下示例输出中，Wordpress 图表的版本是 2.1.10  ：

```
$ ls

wordpress-2.1.10.tgz
```

现在，使用 Azure CLI [az acr helm push][az-acr-helm-push] 命令将图表推送到 Azure 容器注册表中的 Helm 图表存储库。 指定在上一步骤中下载的 Helm 图表的名称，例如 wordpress 2.1.10.tgz  ：

```azurecli
az acr helm push wordpress-2.1.10.tgz
```

几分钟后，Azure CLI 将报告已保存图表，如以下示例输出中所示：

```
$ az acr helm push wordpress-2.1.10.tgz

{
  "saved": true
}
```

## <a name="list-charts-in-the-repository"></a>在存储库中列出图表

Helm 客户端会维护远程存储库内容的本地缓存副本。 对远程存储库的更改不会自动更新 Helm 客户端本地已知的可用图表列表。 在存储库中搜索图表时，Helm 会使用它的本地缓存索引。 若要使用在上一步中上传的图表，必须更新本地 Helm 存储库索引。 可以为 Helm 客户端中的存储库重新创建索引，或使用 Azure CLI 更新存储库索引。 每次将图表添加到存储库时，必须完成此步骤：

```azurecli
az acr helm repo add
```

如果将图表存储在存储库中并在本地提供更新的索引，可以使用常规 Helm 客户端命令来搜索或安装。 若要查看存储库中的所有图表，请使用 `helm search <acrName>`。 提供你自己的 Azure 容器注册表名称：

```console
helm search <acrName>
```

会列出上一步中推送的 Wordpress 图表，如以下示例输出中所示：

```
$ helm search myacrhelm

NAME                CHART VERSION   APP VERSION DESCRIPTION
helmdocs/wordpress  2.1.10          4.9.8       Web publishing platform for building blogs and websites.
```

此外，还可以通过 Azure CLI 使用 [az acr helm list][az-acr-helm-list] 命令列出图表：

```azurecli
az acr helm list
```

## <a name="show-information-for-a-helm-chart"></a>显示 Helm 图表的信息

若要查看存储库中特定图表的信息，可以再次使用常规 Helm 客户端。 若要查看名为 *wordpress* 的图表的信息，请使用 `helm inspect`。

```console
helm inspect <acrName>/wordpress
```

如果没有版本号，将使用最新  版本。 Helm 将返回有关图表的详细信息，如以下精简示例输出中所示：

```
$ helm inspect myacrhelm/wordpress

appVersion: 4.9.8
description: Web publishing platform for building blogs and websites.
engine: gotpl
home: https://www.wordpress.com/
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
  name: bitnami-bot
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 2.1.10
[...]
```

此外，还可以使用 Azure CLI [az acr helm show][az-acr-helm-show] 命令显示图表的信息。 同样，默认情况下会返回图表的最新  版本。 可以附加 `--version` 以列出图表的特定版本，如 2.1.10  ：

```azurecli
az acr helm show wordpress
```

## <a name="install-a-helm-chart-from-the-repository"></a>从存储库安装 Helm 图表

通过指定存储库名称而后图表名称安装存储库中的 Helm 图表。 使用 Helm 客户端安装 Wordpress 图表：

```console
helm install <acrName>/wordpress
```

> [!TIP]
> 如果推送到 Azure 容器注册表 Helm 图表存储库，稍后在新的 CLI 会话中返回，本地 Helm 客户端需要更新的身份验证令牌。 若要获取新的身份验证令牌，请使用 [az acr helm repo add][az-acr-helm-repo-add] 命令。

会在安装过程中完成以下步骤：

- Helm 客户端将搜索本地存储库索引。
- 会从 Azure 容器注册表存储库下载相应的图表。
- 使用 Tiller 在 Kubernetes 群集中部署图表。

以下精简示例输出显示了通过 Helm 图表部署的 Kubernetes 资源：

```
$ helm install myacrhelm/wordpress

NAME:   irreverent-jaguar
LAST DEPLOYED: Thu Sep 13 21:44:20 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                                          READY  STATUS   RESTARTS  AGE
irreverent-jaguar-wordpress-7ff46d9b8c-b7v6m  0/1    Pending  0         1s
irreverent-jaguar-mariadb-0                   0/1    Pending  0         1s
[...]
```

## <a name="delete-a-helm-chart-from-the-repository"></a>从存储库删除 Helm 图表

若要从存储库中删除一个图表，请使用 [az acr helm delete][az-acr-helm-delete] 命令。 指定图表的名称，如 wordpress  ，以及要删除的版本，如 *2.1.10*。

```azurecli
az acr helm delete wordpress --version 2.1.10
```

如果想要删除已命名图表的所有版本，请忽略 `--version` 参数。

图表将继续在 `helm search <acrName>` 中返回。 同样，Helm 客户端不会自动更新存储库中可用图表的列表。 若要更新 Helm 客户端存储库索引，请再次使用 [az acr helm repo add][az-acr-helm-repo-add] 命令：

```azurecli
az acr helm repo add
```

## <a name="next-steps"></a>后续步骤

本文使用了从公共稳定  存储库中获取的现有 Helm 图表。 有关如何创建和部署 Helm 图表的详细信息，请参阅[开发 Helm 图表][develop-helm-charts]。

Helm 图表可以用作容器生成过程的一部分。 有关详细信息，请参阅[使用 Azure 容器注册表任务][acr-tasks]。

有关如何使用和管理 Azure 容器注册表的详细信息，请参阅[最佳做法][acr-bestpractices]。

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[develop-helm-charts]: https://docs.helm.sh/developing_charts/
[semver2]: https://semver.org/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm-repo-add]: /cli/azure/acr/helm/repo#az-acr-helm-repo-add
[az-acr-helm-push]: /cli/azure/acr/helm#az-acr-helm-push
[az-acr-helm-list]: /cli/azure/acr/helm#az-acr-helm-list
[az-acr-helm-show]: /cli/azure/acr/helm#az-acr-helm-show
[az-acr-helm-delete]: /cli/azure/acr/helm#az-acr-helm-delete
[acr-tasks]: container-registry-tasks-overview.md
