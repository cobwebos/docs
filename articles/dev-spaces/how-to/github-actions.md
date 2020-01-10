---
title: Azure Kubernetes 服务 & GitHub 操作
services: azure-dev-spaces
ms.date: 11/04/2019
ms.topic: conceptual
description: 使用 GitHub 操作和 Azure Dev Spaces 直接在 Azure Kubernetes 服务中查看和测试拉取请求中的更改
keywords: Docker，Kubernetes，Azure，AKS，Azure Kubernetes 服务，容器，GitHub 操作，Helm，服务网格，service 网格路由，kubectl，k8s
manager: gwallace
ms.openlocfilehash: 7d96726e829154847744d9aec07a9cb0938f75de
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771115"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>Azure Kubernetes Service （预览版） & GitHub 操作

Azure Dev Spaces 使用 GitHub 操作提供工作流，使你能够在拉取请求合并到存储库的主分支之前，直接在 AKS 中测试拉取请求中的更改。 让正在运行的应用程序检查拉取请求的更改会提高开发人员和团队成员的置信度。 此正在运行的应用程序还可以帮助团队成员（如产品经理和设计人员）在开发的早期阶段成为审核过程的一部分。

本指南介绍如何：

* 在 Azure 中的托管 Kubernetes 群集上设置 Azure Dev Spaces。
* 将包含多个微服务的大型应用程序部署到开发空间。
* 设置具有 GitHub 操作的 CI/CD。
* 在完整应用程序的上下文中的隔离开发空间内测试单个微服务。

> [!IMPORTANT]
> 此功能目前处于预览状态。 需同意[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free)。
* [已安装 Azure CLI][azure-cli-installed]。
* [已安装 Helm 3][helm-installed]。
* [已启用 Github 操作][github-actions-beta-signup]的 github 帐户。
* 在 AKS 群集上运行的[Azure Dev Spaces 自行车共享示例应用程序](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md)。

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

## <a name="configure-your-github-action"></a>配置 GitHub 操作

> [!IMPORTANT]
> 你必须为存储库启用 GitHub 操作。 若要为存储库启用 GitHub 操作，请导航到 GitHub 上的存储库，单击 "操作" 选项卡，然后选择启用此存储库的操作。

导航到分叉存储库，然后单击 "*设置*"。 单击左侧边栏中的 "*机密*"。 单击 "*添加新机密*" 添加下面的每个新机密：

1. *AZURE_CREDENTIALS*：服务主体创建的整个输出。
1. *RESOURCE_GROUP*： AKS 群集的资源组，在本示例中为*MyResourceGroup*。
1. *CLUSTER_NAME*： AKS 群集的名称，此示例中为*MyAKS*。
1. *CONTAINER_REGISTRY*： ACR 的*loginServer* 。
1. *Host*：开发人员空间的主机，其形式 *< MASTER_SPACE > <* APP_NAME > < HOST_SUFFIX >。
1. *HOST_SUFFIX*：开发人员空间的主机后缀，在本例中为*fedcab0987.eus.azds.io*。
1. *IMAGE_PULL_SECRET*：要使用的机密的名称，例如*演示密钥*。
1. *MASTER_SPACE*：父 dev 空间的名称，在本示例中为*dev*。
1. *REGISTRY_USERNAME*：从服务主体创建的 JSON 输出的*clientId* 。
1. *REGISTRY_PASSWORD*：从服务主体创建的 JSON 输出中的*clientSecret* 。

> [!NOTE]
> GitHub 操作使用所有这些机密，并在[github/工作流/docker-compose.override.yml][github-action-yaml]中进行配置。

## <a name="create-a-new-branch-for-code-changes"></a>为代码更改创建新分支

导航到 "`BikeSharingApp/`"，然后创建一个名为 "*自行车-映像*" 的新分支。

```cmd
cd dev-spaces/samples/BikeSharingApp/
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

推送完成后，请导航到 GitHub 上的分叉存储库，以将分叉存储库中的*主*分支作为基准分支与 "*自行车-映像*" 分支进行比较。

在拉取请求打开后，导航到 "*操作*" 选项卡。验证是否已开始新的操作并生成*自行车*服务。

## <a name="view-the-child-space-with-your-changes"></a>查看具有更改的子区域

操作完成后，你将看到一个注释，其中包含新子空间的 URL，并基于拉取请求中的更改。

> [!div class="mx-imgBorder"]
> ![GitHub 操作 Url](../media/github-actions/github-action-url.png)

通过从注释打开 URL，导航到*bikesharingweb*服务。 选择 " *Aurelia meets Briggs （客户）* " 作为用户，然后选择要出租的自行车。 验证是否不再显示自行车的占位符图像。

如果将所做的更改合并到分支中的*主*分支，则会运行另一个操作来重建和运行父开发人员空间中的整个应用程序。 在此示例中，父空间为*dev*。 此操作在[github/工作流/bikesharing.clients.core. docker-compose.override.yml][github-action-bikesharing-yaml]中进行配置。

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
[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[sp-acr]: ../../container-registry/container-registry-auth-service-principal.md
[sp-aks]: ../../aks/kubernetes-service-principal.md
[team-quickstart]: ../quickstart-team-development.md
