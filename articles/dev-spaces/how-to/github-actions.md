---
title: Azure Kubernetes 服务 & GitHub 操作
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 10/24/2019
ms.topic: conceptual
description: 使用 GitHub 操作和 Azure Dev Spaces 直接在 Azure Kubernetes 服务中查看和测试拉取请求中的更改。
keywords: Docker，Kubernetes，Azure，AKS，Azure Kubernetes 服务，容器，GitHub 操作，Helm，服务网格，service 网格路由，kubectl，k8s
manager: gwallace
ms.openlocfilehash: 2638fe2cd12407e43d3b3b698cdfca5231362db4
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73065933"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>Azure Kubernetes Service （预览版） & GitHub 操作

Azure Dev Spaces 使用 GitHub 操作提供工作流，使你能够在拉取请求合并到存储库的主分支之前，直接在 AKS 中测试拉取请求中的更改。 让正在运行的应用程序检查拉取请求的更改会提高开发人员和团队成员的置信度。 此正在运行的应用程序还可以帮助团队成员（如产品经理和设计人员）在开发的早期阶段成为审核过程的一部分。

本指南介绍如何执行以下操作：

- 在 Azure 中的托管 Kubernetes 群集上设置 Azure Dev Spaces。
- 将包含多个微服务的大型应用程序部署到开发空间。
- 设置具有 GitHub 操作的 CI/CD。
- 在完整应用程序的上下文中的隔离开发空间内测试单个微服务。

> [!IMPORTANT]
> 此功能目前处于预览状态。 需同意[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

## <a name="prerequisites"></a>必备组件

- Azure 订阅。 如果没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free)。
- [已安装 Azure CLI][azure-cli-installed]。
- [已安装 Helm 2.13 或更高版本][helm-installed]。
- [已启用 Github 操作][github-actions-beta-signup]的 github 帐户。

## <a name="create-an-azure-kubernetes-service-cluster"></a>创建 Azure Kubernetes 服务群集

必须在[支持的区域][supported-regions]中创建 AKS 群集。 以下命令创建名为 *MyResourceGroup* 的资源组，以及名为 *MyAKS* 的 AKS 群集。

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>在 AKS 群集上启用 Azure Dev Spaces

使用 `use-dev-spaces` 命令在 AKS 群集上启用 Dev Spaces，然后按提示操作。 以下命令在 *MyResourceGroup* 组中的 *MyAKS* 群集上启用 Dev Spaces，并创建名为 *dev* 的开发空间。

> [!NOTE]
> `use-dev-spaces` 命令还将安装 Azure Dev Spaces CLI（如果尚未安装）。 无法在 Azure Cloud Shell 中安装 Azure Dev Spaces CLI。

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="create-an-azure-container-registry"></a>创建 Azure 容器注册表

创建 Azure 容器注册表（ACR）：

```cmd
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> 该名称在 Azure 中必须唯一，并且包含5-50 个字母数字字符。 使用的任何字母都必须为小写。

保存输出的*loginServer*值，因为稍后的步骤中会用到它。

## <a name="create-a-service-principal-for-authentication"></a>创建用于身份验证的服务主体

使用[az ad sp 创建-rbac][az-ad-sp-create-for-rbac]创建服务主体。 例如：

```cmd
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

保存 JSON 输出，因为后面的步骤中会用到它。


使用[az aks show][az-aks-show]显示 aks 群集的*id* ：

```cmd
az aks show -g MyResourceGroup -n MyAKS  --query id
```

使用[az acr show][az-acr-show]显示 acr 的*id* ：

```cmd
az acr show --name <acrName> --query id
```

使用[az role 分配 create][az-role-assignment-create]为*参与者*授予对你的 AKS 群集的访问权限，并*AcrPush*对你的 ACR 的访问权限。

```cmd
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> 你必须是 AKS 群集和 ACR 的所有者，才能让你的服务主体访问这些资源。

## <a name="get-sample-application-code"></a>获取示例应用程序代码

本文介绍如何使用[Azure Dev Spaces 自行车共享示例应用程序][bike-sharing-gh]来演示如何将 Azure Dev Spaces 与 GitHub 操作结合使用。

分叉 Azure Dev Spaces 示例存储库，然后导航到分叉的存储库。 单击 "*操作*" 选项卡，然后选择启用此存储库的操作。

克隆分叉存储库并导航到其目录：

```cmd
git clone https://github.com/USERNAME/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>检索 *dev* 的 HostSuffix

使用 `azds show-context` 显示 *dev* 的 HostSuffix。

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>使用 HostSuffix 更新 Helm 图表

打开 [charts/values.yaml][bike-sharing-values-yaml]，并将 `<REPLACE_ME_WITH_HOST_SUFFIX>` 的所有实例替换为前面检索到的 HostSuffix 值。 保存更改并关闭该文件。

## <a name="run-the-sample-application-in-kubernetes"></a>在 Kubernetes 中运行示例应用程序

用于在 Kubernetes 上运行示例应用程序的命令是现有过程的一部分，不依赖于 Azure Dev Spaces 工具。 在本例中，Helm 是用于运行此示例应用程序的工具，但可以使用其他工具在群集内的命名空间中运行整个应用程序。 Helm 命令针对前面创建的名为 *dev* 的开发空间，但此开发空间也是 Kubernetes 命名空间。 因此，其他工具可以针对与其他命名空间相同的开发空间。

当应用程序在群集中运行时，无论使用何种工具进行部署，都可以使用 Azure Dev Spaces 进行开发。

使用 `helm init` 和 `helm install` 命令在群集上设置和安装示例应用程序。

```cmd
cd charts/
helm init --wait
helm install -n bikesharing . --dep-up --namespace dev --atomic
```

> [!Note]
> **如果你使用的是启用 RBAC 的群集**，请务必[为 Tiller 配置服务帐户][tiller-rbac]。 否则 `helm` 命令将失败。

`helm install` 命令可能需要几分钟才能完成。 完成后，该命令的输出将显示由它部署到群集的所有服务的状态：

```cmd
$ cd charts/
$ helm init --wait
...
Happy Helming!

$ helm install -n bikesharing . --dep-up --namespace dev --atomic

Hang tight while we grab the latest from your chart repositories...
...
NAME               READY  UP-TO-DATE  AVAILABLE  AGE
bikes              1/1    1           1          4m32s
bikesharingweb     1/1    1           1          4m32s
billing            1/1    1           1          4m32s
gateway            1/1    1           1          4m32s
reservation        1/1    1           1          4m32s
reservationengine  1/1    1           1          4m32s
users              1/1    1           1          4m32s
```

在群集上安装示例应用程序后，由于已在群集上启用了 Dev Spaces，因此请使用 `azds list-uris` 命令来显示该示例应用程序在当前已选择的 *dev* 中的 URL。

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

通过 `azds list-uris` 命令打开公共 URL，导航到 *bikesharingweb* 服务。 在以上示例中，*bikesharingweb* 服务的公共 URL 为 `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`。 选择 " *Aurelia meets Briggs （客户）* " 作为用户，然后选择要出租的自行车。 验证是否显示自行车的占位符图像。

## <a name="configure-your-github-action"></a>配置 GitHub 操作

导航到分叉存储库，然后单击 "*设置*"。 单击左侧边栏中的 "*机密*"。 单击 "*添加新机密*" 添加下面的每个新机密：

1. *AZURE_CREDENTIALS*：服务主体创建的整个输出。
1. *RESOURCE_GROUP*： AKS 群集的资源组，在本例中为*MyResourceGroup*。
1. *CLUSTER_NAME*： AKS 群集的名称，此示例中为*MyAKS*。
1. *CONTAINER_REGISTRY*： ACR 的*loginServer* 。
1. *Host*：用于开发人员空间的主机，其形式为 *< MASTER_SPACE > < APP_NAME >。 < HOST_SUFFIX >* ，在本例中为*dev.bikesharingweb.fedcab0987.eus.azds.io*。
1. *IMAGE_PULL_SECRET*：要使用的机密的名称，例如，*演示密码*。
1. *MASTER_SPACE*：父 dev 空间的名称，在本示例中为*dev*。
1. *REGISTRY_USERNAME*：从服务主体创建的 JSON 输出开始的*clientId* 。
1. *REGISTRY_PASSWORD*：从服务主体创建的 JSON 输出的*clientSecret* 。

> [!NOTE]
> GitHub 操作使用所有这些机密，并在[github/工作流/docker-compose.override.yml][github-action-yaml]中进行配置。

## <a name="create-a-new-branch-for-code-changes"></a>为代码更改创建新分支

向后导航到 "`BikeSharingApp/`"，然后创建一个名为 "*自行车-映像*" 的新分支。

```cmd
cd ..
git checkout -b bike-images
```

编辑[自行车/node.js][bikes-server-js]以删除行232和233：

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

此部分现在应如下所示：

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

保存文件，然后使用 `git add` 和 `git commit` 来暂存更改。

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>推送更改

使用 `git push` 将新分支推送到分叉存储库：

```cmd
git push origin bike-images
```

推送完成后，在 GitHub 上导航到分叉存储库，使用分叉存储库中的*主*分支创建拉取请求，并将其与 "*自行车-映像*" 分支进行比较。

在拉取请求打开后，导航到 "*操作*" 选项卡。验证是否已开始新的操作并生成*自行车*服务。

## <a name="view-the-child-space-with-your-changes"></a>查看具有更改的子区域

操作完成后，你将看到一个注释，其中包含新子空间的 URL，并基于拉取请求中的更改。

> [!div class="mx-imgBorder"]
> ![GitHub 操作 Url](../media/github-actions/github-action-url.png)

通过从注释打开 URL，导航到*bikesharingweb*服务。 选择 " *Aurelia meets Briggs （客户）* " 作为用户，然后选择要出租的自行车。 验证是否不再显示自行车的占位符图像。

## <a name="clean-up-your-azure-resources"></a>清理 Azure 资源

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>后续步骤

了解 Azure Dev Spaces 如何帮助开发跨多个容器的更复杂应用程序，以及如何通过在不同的空间中使用不同的代码版本或分支来简化协作式开发。

> [!div class="nextstepaction"]
> [Azure Dev Spaces 中的团队开发][team-quickstart]

[azure-cli-installed]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[bikes-server-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L232-L233
[bike-sharing-gh]: https://github.com/Azure/dev-spaces/
[bike-sharing-values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml
[github-actions-beta-signup]: https://github.com/features/actions
[github-action-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikes.yml
[github-action-bikesharing-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikesharing.yml
[helm-installed]: https://helm.sh/docs/using_helm/#installing-helm
[tiller-rbac]: https://helm.sh/docs/using_helm/#role-based-access-control
[supported-regions]: ../about.md#supported-regions-and-configurations
[sp-acr]: ../../container-registry/container-registry-auth-service-principal.md
[sp-aks]: ../../aks/kubernetes-service-principal.md
[team-quickstart]: ../quickstart-team-development.md