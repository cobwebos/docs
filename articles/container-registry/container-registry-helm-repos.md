---
title: 存储 Helm 图表
description: 了解如何使用 Azure 容器注册表中的存储库来为 Kubernetes 应用程序存储 Helm 图表
ms.topic: article
ms.date: 03/20/2020
ms.openlocfilehash: 04ba3aaf312188ab77c04a97ab960cf9b9af078f
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857613"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>将 Helm 图表推送和提取到 Azure 容器注册表

若要快速地管理和部署适用于 Kubernetes 的应用程序，可以使用[开放源代码 Helm 包管理器][helm]。 在使用 Helm 的情况下，应用程序包会定义为[图表](https://helm.sh/docs/topics/charts/)，这些图表会收集并存储到 [Helm 图表存储库](https://helm.sh/docs/topics/chart_repository/)中。

本文介绍如何使用 Helm 3 或 Helm 2 安装，在 Azure 容器注册表中托管 Helm 图表存储库。 在许多情况下，你需要为你开发的应用程序生成并上传自己的图表。 有关如何生成自己的 Helm 图表的详细信息，请参阅[图表模板开发人员指南][develop-helm-charts]。 还可以从另一个 Helm 存储库存储现有的 Helm 图表。

> [!IMPORTANT]
> 对 Azure 容器注册表中的 Helm 图表的支持目前以预览版提供。 需同意补充的[使用条款][terms-of-use]才能使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

## <a name="helm-3-or-helm-2"></a>要使用 Helm 3 还是 Helm 2？

若要存储、管理和安装 Helm 图表，请使用 Helm 客户端和 Helm CLI。 Helm 客户端的主要版本包括 Helm 3 和 Helm 2。 Helm 3 支持新图表格式，且不再安装 Tiller 服务器端组件。 有关版本差异的详细信息，请参阅[版本常见问题解答](https://helm.sh/docs/faq/)。 如果以前已部署了 Helm 2 图表，请参阅[将 Helm v2 迁移到 v3](https://helm.sh/docs/topics/v2_v3_migration/)。

可以使用 Helm 3 或 Helm 2，通过特定于每个版本的工作流在 Azure 容器注册表中托管 Helm 图表：

* [Helm 3 客户端](#use-the-helm-3-client) - 使用 Helm CLI 中的 `helm chart` 命令，在注册表中将图表作为 [OCI 项目](container-registry-image-formats.md#oci-artifacts)进行管理
* [Helm 2 客户端](#use-the-helm-2-client) - 在 Azure CLI 中使用 [az acr helm][az-acr-helm] 命令，将容器注册表作为 Helm 图表存储库进行添加并管理

### <a name="additional-information"></a>其他信息

* 在大多数情况下，我们建议结合本机 `helm chart` 命令使用 Helm 3 工作流，将图表作为 OCI 项目进行管理。
* 从 Helm 3 开始，支持[az acr Helm][az-acr-helm]命令，以便兼容 Helm 2 客户端和图表格式。 目前尚未计划将来开发这些命令。 请参阅[产品路线图](https://github.com/Azure/acr/blob/master/docs/acr-roadmap.md#acr-helm-ga)。
* 不能使用 Azure 门户查看或管理 Helm 2 图表。

## <a name="use-the-helm-3-client"></a>使用 Helm 3 客户端

### <a name="prerequisites"></a>先决条件

- Azure 订阅中有一个 Azure 容器注册表。  如果需要，请使用 [Azure 门户](container-registry-get-started-portal.md)或 [Azure CLI](container-registry-get-started-azure-cli.md) 创建一个注册表。
- Helm 客户端 3.1.0 或更高版本 - 运行 `helm version` 可查看当前版本。  有关如何安装和升级 Helm 的详细信息，请参阅[安装 Helm][helm-install]。
- 要在其中安装 Helm 图表的 Kubernetes 群集。  如果需要，请创建一个 [Azure Kubernetes 服务群集][aks-quickstart]。 
- Azure CLI 2.0.71 或更高版本 - 运行 `az --version` 可查看版本。  如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

### <a name="high-level-workflow"></a>高级工作流

使用 Helm 3 可以： 

* 在 Azure 容器注册表中创建一个或多个 Helm 存储库
* 将 Helm 3 图表作为 [OCI 项目](container-registry-image-formats.md#oci-artifacts)存储在注册表中。 目前，Helm 3 对 OCI 的支持是实验性的。 
* 使用 `helm registry login` 命令向注册表进行身份验证。
* 在 Helm CLI 中使用 `helm chart` 命令在注册表中推送、提取和管理 Helm 图表
* 使用 `helm install` 从本地存储库缓存将图表安装到 Kubernetes 群集。

有关示例，请参阅以下部分。

### <a name="enable-oci-support"></a>启用 OCI 支持

设置以下环境变量，以在 Helm 3 客户端中启用 OCI 支持。 此支持目前是实验性的。 

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="create-a-sample-chart"></a>创建示例图表

使用以下命令创建测试图表：

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

在此简单示例中，请将目录切换到 `templates` 文件夹，并先删除其中的内容：

```console
cd hello-world/templates
rm -rf *
```

在 `templates` 文件夹中，创建一个名为 `configmap.yaml` 的文件，其中包含以下内容：

```console
cat <<EOF > configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: hello-world-configmap
data:
  myvalue: "Hello World"
EOF
```

有关创建和运行此示例的详细信息，请参阅 Helm 文档中的[入门](https://helm.sh/docs/chart_template_guide/getting_started/)。

### <a name="save-chart-to-local-registry-cache"></a>将图表保存到本地注册表缓存

将目录切换到 `hello-world` 子目录。 然后，运行 `helm chart save` 以在本地保存图表的副本，并使用注册表的完全限定名称（全小写）以及目标存储库和标记创建别名。 

在以下示例中，注册表名称为 mycontainerregistry，目标存储库为 hello-world，目标图表标记为 v1，但要根据你的环境替换值：   

```console
cd ..
helm chart save . hello-world:v1
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:v1
```

运行 `helm chart list` 确认已将图表保存到本地注册表缓存中。 输出类似于：

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:v1                                           hello-world       0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:v1       hello-world       0.1.0   5899db0 3.2 KiB        2 minutes
```

### <a name="authenticate-with-the-registry"></a>向注册表进行身份验证

在 Helm 3 CLI 中运行 `helm registry login` 命令，以使用适合你的方案的凭据[向注册表进行身份验证](container-registry-authentication.md)。

例如，创建一个对注册表[拥有提取和推送权限的 Azure Active Directory 服务主体](container-registry-auth-service-principal.md#create-a-service-principal)（AcrPush 角色）。 然后向 `helm registry login` 提供服务主体凭据。 以下示例使用环境变量提供密码：

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

### <a name="push-chart-to-azure-container-registry"></a>向 Azure 容器注册表推送图表

在 Helm 3 CLI 中运行 `helm chart push` 命令，以将图表推送到完全限定的目标存储库：

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
```

成功推送后，输出将类似于：

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:v1
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

### <a name="list-charts-in-the-repository"></a>在存储库中列出图表

与 Azure 容器注册表中存储的映像一样，可以使用 [az acr repository][az-acr-repository] 命令来显示托管图表、图表标记和清单的存储库。 

例如，运行 [az acr repository show][az-acr-repository-show] 可以查看在上一步骤中创建的存储库的属性：

```azurecli
az acr repository show \
  --name mycontainerregistry \
  --repository helm/hello-world
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
  "createdTime": "2020-03-20T18:11:37.6701689Z",
  "imageName": "helm/hello-world",
  "lastUpdateTime": "2020-03-20T18:11:37.7637082Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

运行 [az acr repository show-manifests][az-acr-repository-show-manifests] 命令，以查看存储库中存储的图表的详细信息。 例如：

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

输出（在此示例中已缩略）显示 `configMediaType` 为 `application/vnd.cncf.helm.config.v1+json`：

```output
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2020-03-20T18:11:37.7167893Z",
    "digest": "sha256:0c03b71c225c3ddff53660258ea16ca7412b53b1f6811bf769d8c85a1f0663ee",
    "imageSize": 3301,
    "lastUpdateTime": "2020-03-20T18:11:37.7167893Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "v1"
    ]
```

### <a name="pull-chart-to-local-cache"></a>将图表提取到本地缓存

若要将某个 Helm 图表安装到 Kubernetes，该图表必须位于本地缓存中。 在此示例中，请先运行 `helm chart remove` 以删除名为 `mycontainerregistry.azurecr.io/helm/hello-world:v1` 的现有本地图表：

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

运行 `helm chart pull` 将 Azure 容器注册表中的图表下载到本地缓存：

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

### <a name="export-helm-chart"></a>导出 Helm 图表

若要进一步处理图表，请使用 `helm chart export` 将其导出到本地目录。 例如，将提取的图表导出到 `install` 目录：

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:v1 \
  --destination ./install
```

若要查看存储库中已导出的图表的信息，请在图表所导出到的目录中运行 `helm show chart` 命令。

```console
cd install
helm show chart hello-world
```

Helm 将返回有关最新版本的图表的详细信息，如以下示例输出中所示：

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

### <a name="install-helm-chart"></a>安装 Helm 图表

运行 `helm install` 来安装已提取到本地缓存且已导出的 Helm 图表。 指定版本名称（例如 myhelmtest）或传递 `--generate-name` 参数。  例如：

```console
helm install myhelmtest ./hello-world
```

成功安装图表后，输出将类似于：

```console
NAME: myhelmtest
LAST DEPLOYED: Fri Mar 20 14:14:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

若要验证安装，请运行 `helm get manifest` 命令。 该命令将返回 `configmap.yaml` 模板文件中的 YAML 数据。

运行 `helm uninstall` 以卸载群集上的图表版本：

```console
helm uninstall myhelmtest
```

### <a name="delete-a-helm-chart-from-the-repository"></a>从存储库删除 Helm 图表

若要从存储库中删除图表，请使用 [az acr repository delete][az-acr-repository-delete] 命令。 运行以下命令，并在出现提示时确认操作：

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="use-the-helm-2-client"></a>使用 Helm 2 客户端

### <a name="prerequisites"></a>先决条件

- Azure 订阅中有一个 Azure 容器注册表。  如果需要，请使用 [Azure 门户](container-registry-get-started-portal.md)或 [Azure CLI](container-registry-get-started-azure-cli.md) 创建一个注册表。
- Helm 客户端版本 2.11.0（而不是 RC 版本）或更高版本  - 运行 `helm version` 以查找当前版本。 还需要在 Kubernetes 群集中初始化的一个 Helm 服务器 (Tiller)。 如果需要，请创建一个 [Azure Kubernetes 服务群集][aks-quickstart]。 有关如何安装和升级 Helm 的详细信息，请参阅[安装 Helm][helm-install-v2]。
- Azure CLI 版本 2.0.46 或更高版本  - 运行 `az --version` 查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

### <a name="high-level-workflow"></a>高级工作流

使用 Helm 2 可以： 

* 将 Azure 容器注册表配置为单个 Helm 图表存储库。  当你在存储库中添加或删除图表时，Azure 容器注册表会管理索引定义。
* 通过 Azure CLI 向 Azure 容器注册表进行身份验证，然后，系统会使用注册表 URI 和凭据自动更新 Helm 客户端。 无需手动指定此注册表信息，以免凭据在命令历史记录中公开。
* 在 Azure CLI 中使用 [az acr helm][az-acr-helm] 命令可将 Azure 容器注册表添加为 Helm 图表存储库，并可推送和管理图表。 这些 Azure CLI 命令包装 Helm 2 客户端命令。
* 将 Azure 容器注册表中的图表存储库添加到本地 Helm 存储库索引，以支持图表搜索。
* 使用 `helm install` 从本地存储库缓存将图表安装到 Kubernetes 群集。

有关示例，请参阅以下部分。

### <a name="add-repository-to-helm-client"></a>将存储库添加到 Helm 客户端

使用 [az acr helm repo add][az-acr-helm-repo-add] 命令将 Azure 容器注册表 Helm 图表存储库添加到 Helm 客户端。 此命令将为 Helm 客户端使用的 Azure 容器注册表获取身份验证令牌。 身份验证令牌的有效期为 3 小时。 类似于 `docker login`，可以在以后的 CLI 会话中运行此命令，以对 Helm 客户端的 Azure 容器注册表 Helm 图表存储库进行身份验证：

```azurecli
az acr helm repo add --name mycontainerregistry
```

### <a name="add-a-sample-chart-to-the-repository"></a>将示例图表添加到存储库

首先，在 ~/acr-helm  中创建一个本地目录，然后下载现有的 stable/wordpress  图表：

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm repo update
helm fetch stable/wordpress
```

键入 `ls` 列出已下载的图表，并记下文件名中包含的 Wordpress 版本。 `helm fetch stable/wordpress` 命令未指定特定版本，因此会获取最新  版本。 在以下示例输出中，Wordpress 图表的版本为 8.1.0  ：

```output
wordpress-8.1.0.tgz
```

在 Azure CLI 中使用 [az acr helm push][az-acr-helm-push] 命令将图表推送到 Azure 容器注册表中的 Helm 图表存储库。 指定在上一步骤中下载的 Helm 图表的名称，例如 wordpress-8.1.0.tgz  ：

```azurecli
az acr helm push --name mycontainerregistry wordpress-8.1.0.tgz
```

片刻之后，Azure CLI 将报告图表已保存，如以下示例输出中所示：

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

如果将图表存储在存储库中并在本地提供更新的索引，可以使用常规 Helm 客户端命令来搜索或安装。 若要查看存储库中的所有图表，请使用 `helm search` 命令，并提供自己的 Azure 容器注册表名称：

```console
helm search mycontainerregistry
```

会列出上一步中推送的 Wordpress 图表，如以下示例输出中所示：

```output
NAME                  CHART VERSION    APP VERSION    DESCRIPTION
helmdocs/wordpress    8.1.0           5.3.2          Web publishing platform for building blogs and websites.
```

此外，还可以通过 Azure CLI 使用 [az acr helm list][az-acr-helm-list] 命令列出图表：

```azurecli
az acr helm list --name mycontainerregistry
```

### <a name="show-information-for-a-helm-chart"></a>显示 Helm 图表的信息

若要查看存储库中特定图表的信息，可以使用 `helm inspect` 命令。

```console
helm inspect mycontainerregistry/wordpress
```

如果没有版本号，将使用最新  版本。 Helm 将返回有关图表的详细信息，如以下精简示例输出中所示：

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

此外，还可以使用 Azure CLI [az acr helm show][az-acr-helm-show] 命令显示图表的信息。 同样，默认情况下会返回图表的最新  版本。 可以追加 `--version` 以列出特定版本的图表，例如 8.1.0  ：

```azurecli
az acr helm show --name mycontainerregistry wordpress
```

### <a name="install-a-helm-chart-from-the-repository"></a>从存储库安装 Helm 图表

通过指定存储库名称和图表名称来安装存储库中的 Helm 图表。 使用 Helm 客户端安装 Wordpress 图表：

```console
helm install mycontainerregistry/wordpress
```

> [!TIP]
> 如果推送到 Azure 容器注册表 Helm 图表存储库，稍后在新的 CLI 会话中返回，本地 Helm 客户端需要更新的身份验证令牌。 若要获取新的身份验证令牌，请使用 [az acr helm repo add][az-acr-helm-repo-add] 命令。

会在安装过程中完成以下步骤：

- Helm 客户端将搜索本地存储库索引。
- 会从 Azure 容器注册表存储库下载相应的图表。
- 使用 Tiller 在 Kubernetes 群集中部署图表。

在安装过程中，请按照命令输出中的说明查看 WorPress URL 和凭据。 还可以运行 `kubectl get pods` 命令来查看通过 Helm 图表部署的 Kubernetes 资源：

```output
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>从存储库删除 Helm 图表

若要从存储库中删除图表，请使用 [az acr helm delete][az-acr-helm-delete] 命令。 指定图表的名称（例如 wordpress），以及要删除的版本（例如 8.1.0）   。

```azurecli
az acr helm delete --name mycontainerregistry wordpress --version 8.1.0
```

如果想要删除已命名图表的所有版本，请忽略 `--version` 参数。

运行 `helm search` 时，图表将继续返回。 同样，Helm 客户端不会自动更新存储库中可用图表的列表。 若要更新 Helm 客户端存储库索引，请再次使用 [az acr helm repo add][az-acr-helm-repo-add] 命令：

```azurecli
az acr helm repo add --name mycontainerregistry
```

## <a name="next-steps"></a>后续步骤

* 有关如何创建和部署 Helm 图表的详细信息，请参阅[开发 Helm 图表][develop-helm-charts]。
* 详细了解如何在 [Azure Kubernetes 服务 (AKS)](../aks/kubernetes-helm.md) 中使用 Helm 安装应用程序。
* Helm 图表可以用作容器生成过程的一部分。 有关详细信息，请参阅[使用 Azure 容器注册表任务][acr-tasks]。

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
