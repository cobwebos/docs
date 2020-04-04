---
title: gitHub 操作& Azure 库伯奈斯服务（预览）
services: azure-dev-spaces
ms.date: 04/03/2020
ms.topic: conceptual
description: 使用 GitHub 操作和 Azure 开发空间直接在 Azure 库伯奈斯服务中查看和测试从拉取请求中的更改
keywords: Docker、Kubernetes、Azure、AKS、Azure 库伯奈斯服务、容器、GitHub 操作、Helm、服务网格、服务网格路由、库贝克特尔、k8s
manager: gwallace
ms.openlocfilehash: a83da0ef3958748831eb0eeda1aa5e91efa7ef2e
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637946"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>gitHub 操作& Azure 库伯奈斯服务（预览）

Azure 开发人员空间提供使用 GitHub 操作的工作流，允许您在拉取请求合并到存储库的主分支之前直接在 AKS 中测试从拉取请求中的更改。 拥有运行的应用程序来查看拉取请求的更改可以增强开发人员和团队成员的信心。 此正在运行的应用程序还可以帮助团队成员（如产品经理和设计人员）在开发的早期阶段成为审核过程的一部分。

本指南介绍如何：

* 在 Azure 中的托管 Kubernetes 群集上设置 Azure Dev Spaces。
* 将包含多个微服务的大型应用程序部署到开发空间。
* 使用 GitHub 操作设置 CI/CD。
* 在完整应用程序的上下文中的隔离开发空间内测试单个微服务。

> [!IMPORTANT]
> 此功能目前处于预览状态。 需同意[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free)。
* [已安装 Azure CLI][azure-cli-installed]。
* [已安装 Helm 3][helm-installed]。
* [启用 GitHub 操作的][github-actions-beta-signup]GitHub 帐户。
* 在 AKS 群集上运行的[Azure 开发人员空间自行车共享示例应用程序](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md)。

## <a name="create-an-azure-container-registry"></a>创建 Azure 容器注册表

创建 Azure 容器注册表 (ACR)：

```azurecli
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> ACR 的名称在 Azure 中必须是唯一的，并且包含 5-50 个字母数字字符。 您使用的任何字母都必须为小写字母。

从输出中保存*登录服务器*值，因为它在后面的步骤中使用。

## <a name="create-a-service-principal-for-authentication"></a>创建用于身份验证的服务主体

使用[az ad sp 创建为 rbac][az-ad-sp-create-for-rbac]创建服务主体。 例如：

```azurecli
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

保存 JSON 输出，因为它在后面的步骤中使用。

使用[az aks 显示][az-aks-show]显示 AKS 群集的*ID：*

```azurecli
az aks show -g MyResourceGroup -n MyAKS  --query id
```

使用[az acr 显示][az-acr-show]显示 ACR 的*ID：*

```azurecli
az acr show --name <acrName> --query id
```

使用[az 角色分配创建][az-role-assignment-create]向*参与者*授予对 AKS 群集的访问权限，并*授予 AcrPush*对 ACR 的访问权限。

```azurecli
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> 您必须同时拥有 AKS 群集和 ACR，才能授予服务主体对这些资源的访问权限。

## <a name="configure-your-github-action"></a>配置 GitHub 操作

> [!IMPORTANT]
> 您必须为存储库启用 GitHub 操作。 要为存储库启用 GitHub 操作，请在 GitHub 上导航到存储库，单击"操作"选项卡，然后选择为此存储库启用操作。

导航到分叉存储库，然后单击 *"设置*"。 单击左侧侧边栏中*的秘密*。 单击"*添加新机密*"以添加以下每个新机密：

1. *AZURE_CREDENTIALS*： 服务主体创建的整个输出。
1. *RESOURCE_GROUP*： AKS 群集的资源组，在此示例中为*MyResourceGroup*。
1. *CLUSTER_NAME*： AKS 群集的名称，在此示例中为*MyAKS*。
1. *CONTAINER_REGISTRY*： ACR 的*登录服务器*。
1. *HOST*： 开发空间的主机，它采用*的形式<MASTER_SPACE>.<APP_NAME>.<HOST_SUFFIX>，**在此示例中dev.bikesharingweb.fedcab0987.eus.azds.io*。
1. *IMAGE_PULL_SECRET：* 你想使用的秘密的名称，例如*演示机密*。
1. *MASTER_SPACE*： 父开发人员空间的名称，在此示例中是*dev*.
1. *REGISTRY_USERNAME*： 从服务主体创建的 JSON 输出的*客户端 Id。*
1. *REGISTRY_PASSWORD*： 来自服务主体创建的 JSON 输出的*客户端机密*。

> [!NOTE]
> 所有这些机密都由 GitHub 操作使用，并在[.github/工作流/bikes.yml][github-action-yaml]中配置。

或者，如果要在 PR 合并后更新主空间，请添加*GATEWAY_HOST*机密，该密名采用 *<MASTER_SPACE>.gateway.<HOST_SUFFIX>，**在此示例中dev.gateway.fedcab0987.eus.azds.io*。 将更改合并到分叉中的主分支后，将运行另一个操作，以在主开发空间中重新生成和运行整个应用程序。 在此示例中，主空间是*开发*。 此操作在[.github/工作流/自行车共享.yml][github-action-bikesharing-yaml]中配置。

此外，如果您希望 PR 中的更改在孙级空间中运行，请更新*MASTER_SPACE*和*HOST*机密。 例如，如果应用程序在*开发*中运行，使用子空间*开发/azureuser1，* 则让 PR 在*dev/azureuser1*的子空间中运行：

* 将*MASTER_SPACE*更新为父空间所需的子空间，在此示例中*为 azureuser1*。
* 在本*示例中，* 将*HOST*更新为 GRANDPARENT_SPACE *<>.<APP_NAME>.<HOST_SUFFIX>，dev.bikesharingweb.fedcab0987.eus.azds.io *。

## <a name="create-a-new-branch-for-code-changes"></a>为代码更改创建新分支

导航到`BikeSharingApp/`并创建一个名为*自行车图像*的新分支。

```cmd
cd dev-spaces/samples/BikeSharingApp/
git checkout -b bike-images
```

编辑[自行车/服务器.js][bikes-server-js]删除行 232 和 233：

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

该部分现在应如下所示：

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

保存文件，然后使用`git add``git commit`并暂存更改。

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>推送更改

用于`git push`将新分支推送到分叉存储库：

```cmd
git push origin bike-images
```

推送完成后，导航到 GitHub 上的分叉存储库，以与*自行车映像*分支相比，将分叉存储库中的*主*分支作为基本分支创建拉取请求。

打开拉取请求后，导航到 *"操作"* 选项卡。验证新操作已启动并正在构建*自行车*服务。

## <a name="view-the-child-space-with-your-changes"></a>使用更改查看子空间

操作完成后，您将看到一个注释，该注释包含指向新子空间的 URL，该注释基于拉取请求中的更改。

> [!div class="mx-imgBorder"]
> ![GitHub 操作 URL](../media/github-actions/github-action-url.png)

通过从注释中打开 URL 导航到*自行车共享 Web*服务。 选择*奥雷利亚布里格斯（客户）* 作为用户，然后选择自行车出租。 验证您不再看到自行车的占位符图像。

如果将更改合并到分叉中的*主*分支中，则将运行另一个操作，以在父开发空间中重新生成和运行整个应用程序。 在此示例中，父空间是*开发*。 此操作在[.github/工作流/自行车共享.yml][github-action-bikesharing-yaml]中配置。

## <a name="clean-up-your-azure-resources"></a>清理 Azure 资源

```azurecli
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
