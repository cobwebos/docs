---
title: 使用 Azure 容器注册表中的 Helm 存储库
description: 了解如何使用 Azure 容器注册表中的 Helm 存储库来存储你的应用程序的图表
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 09/24/2018
ms.author: iainfou
ms.openlocfilehash: 3f0aed1c97acc5dd5c9a9abe1f9171fd3886d83b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212570"
---
# <a name="use-azure-container-registry-as-a-helm-repository-for-your-application-charts"></a>使用 Azure 容器注册表作为应用程序图表的 Helm 存储库

To quickly manage and deploy applications for Kubernetes, you can use the [open-source Helm package manager][helm]. 使用 Helm，应用程序定义为存储在 Helm 图表存储库中的*图表*。 这些图表定义配置和依赖项，并可在整个应用程序生命周期内进行版本控制。 Azure 容器注册表可以用作 Helm 图表存储库的主机。

使用 Azure 容器注册表，你便具有安全的专用 Helm 图表存储库，可以与生成管道或其他 Azure 服务集成。 Azure 容器注册表中的 Helm 图表存储库包括异地复制功能，以让图表接近部署以及进行冗余。 你只需对图表所用的存储进行付费，便适用于所有 Azure 容器注册表价格层。

本文介绍如何使用 Azure 容器注册表中存储的 Helm 图表存储库。

> [!IMPORTANT]
> 此功能目前处于预览状态。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

## <a name="before-you-begin"></a>开始之前

若要完成本文中的步骤，必须满足以下先决条件：

- Azure 容器注册表 - 在 Azure 订阅中创建容器注册表。 例如，使用 [Azure 门户](container-registry-get-started-portal.md)或 [Azure CLI](container-registry-get-started-azure-cli.md)。
- Helm 客户端版本 2.11.0（而不是 RC 版本）或更高版本 - 运行 `helm version` 以查找当前版本。 还需要在 Kubernetes 群集中初始化的一个 Helm 服务器 (Tiller)。 If needed, you can [create an Azure Kubernetes Service cluster][aks-quickstart]. For more information on how to install and upgrade Helm, see [Installing Helm][helm-install].
- Azure CLI 版本 2.0.46 或更高版本- 运行 `az --version` 查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

## <a name="add-a-repository-to-helm-client"></a>将一个存储库添加到 Helm 客户端

Helm 存储库是一个 HTTP 服务器，可以存储 Helm 图表。 Azure 容器注册表可以为 Helm 图表提供此存储，并在向存储库中添加和删除图表时管理索引定义。

若要将 Azure 容器注册表添加为 Helm 图表存储库，请使用 Azure CLI。 使用此方法，便会用 Azure 容器注册表提供支持的存储库的 URI 和凭据更新 Helm 客户端。 比如，不必手动指定此存储库信息，以便凭据不会在命令历史记录中公开。

如果需要，请登录到 Azure CLI 并按照提示进行操作：

```azurecli
az login
```

Configure the Azure CLI defaults with the name of your Azure Container Registry using the [az configure][az-configure] command. 在以下示例中，请将 `<acrName>` 替换为注册表的名称：

```azurecli
az configure --defaults acr=<acrName>
```

Now add your Azure Container Registry Helm chart repository to your Helm client using the [az acr helm repo add][az-acr-helm-repo-add] command. 此命令将为 Helm 客户端使用的 Azure 容器注册表获取身份验证令牌。 身份验证令牌的有效期为 1 小时。 类似于 `docker login`，可以在以后的 CLI 会话中运行此命令，以对 Helm 客户端的 Azure 容器注册表 Helm 图表存储库进行身份验证：

```azurecli
az acr helm repo add
```

## <a name="add-a-chart-to-the-repository"></a>将图表添加到存储库中

在本文中，让我们从公共 Helm 稳定存储库中获取现有 Helm 图表。 稳定存储库是组织有序的公共存储库，包括常见的应用程序图表。 包维护人员可以将其图表提交到稳定存储库中，就像 Docker Hub 向常见容器映像提供公共注册表一样。 然后，可以将从公共稳定存储库下载的图表推送到专用 Azure 容器注册表存储库。 在大多数情况下，你将为自己开发的应用生成并上传自己的图表。 For more information on how to build your own Helm charts, see [developing Helm charts][develop-helm-charts].

首先，在 ~/acr-helm 创建一个目录 ，然后下载现有的 stable/wordpress 图表：

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm fetch stable/wordpress
```

列出已下载的图表，并记下文件名中包含的 Wordpress 版本。 `helm fetch stable/wordpress` 命令未指定特定版本，因此会获取最新版本。 All Helm charts include a version number in the filename that follows the [SemVer 2][semver2] standard. 在以下示例输出中，Wordpress 图表的版本是 2.1.10：

```
$ ls

wordpress-2.1.10.tgz
```

Now push the chart to your Helm chart repository in Azure Container Registry using the Azure CLI [az acr helm push][az-acr-helm-push] command. 指定在上一步骤中下载的 Helm 图表的名称，例如 wordpress 2.1.10.tgz：

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

You can also list the charts with the Azure CLI, using [az acr helm list][az-acr-helm-list]:

```azurecli
az acr helm list
```

## <a name="show-information-for-a-helm-chart"></a>显示 Helm 图表的信息

若要查看存储库中特定图表的信息，可以再次使用常规 Helm 客户端。 若要查看名为 *wordpress* 的图表的信息，请使用 `helm inspect`。

```console
helm inspect <acrName>/wordpress
```

如果没有版本号，将使用最新版本。 Helm 将返回有关图表的详细信息，如以下精简示例输出中所示：

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

You can also show the information for a chart with the Azure CLI [az acr helm show][az-acr-helm-show] command. 同样，默认情况下会返回图表的最新版本。 可以附加 `--version` 以列出图表的特定版本，如 2.1.10：

```azurecli
az acr helm show wordpress
```

## <a name="install-a-helm-chart-from-the-repository"></a>从存储库安装 Helm 图表

通过指定存储库名称而后图表名称安装存储库中的 Helm 图表。 使用 Helm 客户端安装 Wordpress 图表：

```console
helm install <acrName>/wordpress
```

> [!TIP]
> 如果推送到 Azure 容器注册表 Helm 图表存储库，稍后在新的 CLI 会话中返回，本地 Helm 客户端需要更新的身份验证令牌。 To obtain a new authentication token, use the [az acr helm repo add][az-acr-helm-repo-add] command.

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

To delete a chart from the repository, use the [az acr helm delete][az-acr-helm-delete] command. 指定图表的名称，如 wordpress，以及要删除的版本，如 *2.1.10*。

```azurecli
az acr helm delete wordpress --version 2.1.10
```

如果想要删除已命名图表的所有版本，请忽略 `--version` 参数。

图表将继续在 `helm search <acrName>` 中返回。 同样，Helm 客户端不会自动更新存储库中可用图表的列表。 To update the Helm client repo index, use the [az acr helm repo add][az-acr-helm-repo-add] command again:

```azurecli
az acr helm repo add
```

## <a name="next-steps"></a>后续步骤

本文使用了从公共稳定存储库中获取的现有 Helm 图表。 For more information on how to create and deploy Helm charts, see [Developing Helm charts][develop-helm-charts].

Helm 图表可以用作容器生成过程的一部分。 For more information, see [use Azure Container Registry Tasks][acr-tasks].

For more information on how to use and manage Azure Container Registry, see the [best practices][acr-bestpractices].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[develop-helm-charts]: https://helm.sh/docs/topics/charts/
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
