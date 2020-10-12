---
title: 存储 Helm 图表
description: 了解如何使用 Azure 容器注册表中的存储库来为 Kubernetes 应用程序存储 Helm 图表
ms.topic: article
ms.date: 06/12/2020
ms.openlocfilehash: 69b16f35589586787e1c31a0e9755b9030af755d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86537861"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>将 Helm 图表推送和提取到 Azure 容器注册表

若要快速地管理和部署适用于 Kubernetes 的应用程序，可以使用[开放源代码 Helm 包管理器][helm]。 在使用 Helm 的情况下，应用程序包会定义为[图表](https://helm.sh/docs/topics/charts/)，这些图表会收集并存储到 [Helm 图表存储库](https://helm.sh/docs/topics/chart_repository/)中。

本文介绍如何使用 Helm 3 命令在 Azure 容器注册表中托管 Helm 图表存储库。 在许多情况下，你需要为你开发的应用程序生成并上传自己的图表。 有关如何生成自己的 Helm 图表的详细信息，请参阅[图表模板开发人员指南][develop-helm-charts]。 还可以从另一个 Helm 存储库存储现有的 Helm 图表。

> [!IMPORTANT]
> 对 Azure 容器注册表中的 Helm 图表的支持目前以预览版提供。 需同意补充的[使用条款][terms-of-use]才能使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

## <a name="helm-3-or-helm-2"></a>要使用 Helm 3 还是 Helm 2？

若要存储、管理和安装 Helm 图表，请使用 Helm 客户端和 Helm CLI。 Helm 客户端的主要版本包括 Helm 3 和 Helm 2。 有关版本差异的详细信息，请参阅[版本常见问题解答](https://helm.sh/docs/faq/)。 

应使用 Helm 3 在 Azure 容器注册表中托管 Helm 图表。 对于 Helm 3，你可以：

* 在 Azure 容器注册表中创建一个或多个 Helm 存储库
* 将 Helm 3 图表作为 [OCI 项目](container-registry-image-formats.md#oci-artifacts)存储在注册表中。 目前，Helm 3 对 OCI 的支持是实验性的。**
* 使用 `helm registry login` 命令向注册表进行身份验证。
* 在 Helm CLI 中使用 `helm chart` 命令在注册表中推送、提取和管理 Helm 图表
* 使用 `helm install` 从本地存储库缓存将图表安装到 Kubernetes 群集。
> [!NOTE]
> 从 Helm 3 开始，将使用 [az acr Helm][az-acr-helm] 命令与 Helm 2 client 一起使用。 请参阅[产品路线图](https://github.com/Azure/acr/blob/master/docs/acr-roadmap.md#acr-helm-ga)。 如果以前已部署了 Helm 2 图表，请参阅[将 Helm v2 迁移到 v3](https://helm.sh/docs/topics/v2_v3_migration/)。

## <a name="prerequisites"></a>必备条件

本文中的方案需要以下资源：

- Azure 订阅中有一个 Azure 容器注册表。**** 如果需要，请使用 [Azure 门户](container-registry-get-started-portal.md)或 [Azure CLI](container-registry-get-started-azure-cli.md) 创建一个注册表。
- Helm 客户端 3.1.0 或更高版本 - 运行 `helm version` 可查看当前版本。**** 有关如何安装和升级 Helm 的详细信息，请参阅[安装 Helm][helm-install]。
- 要在其中安装 Helm 图表的 Kubernetes 群集。**** 如果需要，请创建一个 [Azure Kubernetes 服务群集][aks-quickstart]。 
- Azure CLI 2.0.71 或更高版本 - 运行 `az --version` 可查看版本。**** 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

## <a name="enable-oci-support"></a>启用 OCI 支持

使用 `helm version` 命令验证是否已安装 Helm 3：

```console
helm version
```

设置以下环境变量，以在 Helm 3 客户端中启用 OCI 支持。 此支持目前是实验性的。 

```console
export HELM_EXPERIMENTAL_OCI=1
```

## <a name="create-a-sample-chart"></a>创建示例图表

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

`templates`通过运行以下命令，在文件夹中创建一个名为的文件 `configmap.yaml` ：

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

## <a name="save-chart-to-local-registry-cache"></a>将图表保存到本地注册表缓存

将目录切换到 `hello-world` 子目录。 然后，运行 `helm chart save` 以在本地保存图表的副本，并使用注册表的完全限定名称（全小写）以及目标存储库和标记创建别名。 

在以下示例中，注册表名称为 mycontainerregistry，目标存储库为 hello-world，目标图表标记为 v1，但要根据你的环境替换值：** ** **

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

## <a name="authenticate-with-the-registry"></a>向注册表进行身份验证

在 Helm 3 CLI 中运行 `helm registry login` 命令，以使用适合你的方案的凭据[向注册表进行身份验证](container-registry-authentication.md)。

例如，创建一个对注册表[拥有提取和推送权限的 Azure Active Directory 服务主体](container-registry-auth-service-principal.md#create-a-service-principal)（AcrPush 角色）。 然后向 `helm registry login` 提供服务主体凭据。 以下示例使用环境变量提供密码：

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

## <a name="push-chart-to-registry"></a>将图表推送到注册表

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

## <a name="list-charts-in-the-repository"></a>在存储库中列出图表

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

## <a name="pull-chart-to-local-cache"></a>将图表提取到本地缓存

若要将某个 Helm 图表安装到 Kubernetes，该图表必须位于本地缓存中。 在此示例中，请先运行 `helm chart remove` 以删除名为 `mycontainerregistry.azurecr.io/helm/hello-world:v1` 的现有本地图表：

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

运行 `helm chart pull` 将 Azure 容器注册表中的图表下载到本地缓存：

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

## <a name="export-helm-chart"></a>导出 Helm 图表

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

## <a name="install-helm-chart"></a>安装 Helm 图表

运行 `helm install` 来安装已提取到本地缓存且已导出的 Helm 图表。 指定版本名称（例如 myhelmtest）或传递 `--generate-name` 参数。** 例如：

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

若要验证安装，请运行 `helm get manifest` 命令。 

```console
helm get manifest myhelmtest
```

该命令将返回 `configmap.yaml` 模板文件中的 YAML 数据。

运行 `helm uninstall` 以卸载群集上的图表版本：

```console
helm uninstall myhelmtest
```

## <a name="delete-chart-from-the-registry"></a>从注册表中删除图表

若要从容器注册表中删除图表，请使用 [az acr repository delete][az-acr-repository-delete] 命令。 运行以下命令，并在出现提示时确认操作：

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="next-steps"></a>后续步骤

* 有关如何创建和部署 Helm 图表的详细信息，请参阅[开发 Helm 图表][develop-helm-charts]。
* 详细了解如何在 [Azure Kubernetes 服务 (AKS)](../aks/kubernetes-helm.md) 中使用 Helm 安装应用程序。
* Helm 图表可以用作容器生成过程的一部分。 有关详细信息，请参阅[使用 Azure 容器注册表任务][acr-tasks]。

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/
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
[acr-tasks]: container-registry-tasks-overview.md
