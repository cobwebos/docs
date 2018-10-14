---
title: 适用于 Azure Kubernetes 服务 (AKS) 的服务主体
description: 在 Azure Kubernetes 服务 (AKS) 中为群集创建和管理 Azure Active Directory 服务主体
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: get-started-article
ms.date: 09/26/2018
ms.author: iainfou
ms.openlocfilehash: ef3139c4b3f06644b219e177fad0c094ed600fb6
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2018
ms.locfileid: "47394584"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>使用 Azure Kubernetes 服务 (AKS) 的服务主体

AKS 群集需要 [Azure Active Directory (AD) 服务主体][aad-service-principal]才能与 Azure API 交互。 需要服务主体才能动态创建和管理其他 Azure 资源，例如 Azure 负载均衡器或容器注册表 (ACR)。

本文介绍如何创建和使用适用于 AKS 群集的服务主体。

## <a name="before-you-begin"></a>开始之前

若要创建 Azure AD 服务主体，必须具有相应的权限，能够向 Azure AD 租户注册应用程序，并将应用程序分配到订阅中的角色。 如果没有必需的权限，可能需要请求 Azure AD 或订阅管理员来分配必需的权限，或者预先创建一个可以与 AKS 群集配合使用的服务主体。

还需安装并配置 Azure CLI 2.0.46 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

## <a name="automatically-create-and-use-a-service-principal"></a>自动创建和使用服务主体

通过 Azure 门户或 [az aks create][az-aks-create] 命令创建 AKS 群集时，Azure 可以自动生成服务主体。

在下述 Azure CLI 示例中，尚未指定服务主体。 在此方案中，Azure CLI 为 AKS 群集创建一个服务主体。 若要成功完成此操作，Azure 帐户必须具有创建服务主体所需的相应权限。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --generate-ssh-keys
```

## <a name="manually-create-a-service-principal"></a>手动创建服务主体

若要通过 Azure CLI 手动创建服务主体，请使用 [az ad sp create-for-rbac][az-ad-sp-create] 命令。 在以下示例中，`--skip-assignment` 参数阻止系统分配更多的默认分配。

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

输出类似于以下示例。 记下你自己的 `appId`和 `password`。 在下一部分创建 AKS 群集时，会使用这些值。

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "azure-cli-2018-09-25-21-10-19",
  "name": "http://azure-cli-2018-09-25-21-10-19",
  "password": "e763725a-5eee-40e8-a466-dc88d980f415",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

## <a name="specify-a-service-principal-for-an-aks-cluster"></a>指定适用于 AKS 群集的服务主体

若要在通过 [az aks create][az-aks-create] 命令创建 AKS 群集时使用现有的服务主体，请使用 [az ad sp create-for-rbac][az-ad-sp-create] 命令的输出中的 `--service-principal` 和 `--client-secret` 参数来指定 `appId` 和 `password`：

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --service-principal <appId> \
    --client-secret <password>
```

如果使用 Azure 门户来部署 AKS 群集，请在“创建 Kubernetes 群集”对话框的“身份验证”页上选择“配置服务主体”。 选择“使用现有”并指定以下值：

- **服务主体客户端 ID** 是你的 *appId*
- **服务主体客户端机密**是密码值

![浏览到 Azure Vote 的图像](media/kubernetes-service-principal/portal-configure-service-principal.png)

## <a name="additional-considerations"></a>其他注意事项

使用 AKS 和 Azure AD 服务主体时，请牢记以下注意事项。

- Kubernetes 的服务主体是群集配置的一部分。 但是，请勿使用标识来部署群集。
- 每个服务主体都与一个 Azure AD 应用程序相关联。 Kubernetes 群集的服务主体可以与任何有效的 Azure AD 应用程序名称（例如 *https://www.contoso.org/example*）相关联。 应用程序的 URL 不一定是实际的终结点。
- 指定服务主体**客户端 ID** 时，请使用 `appId` 的值。
- 在 Kubernetes 群集的主 VM 和节点 VM 中，服务主体凭据存储在 `/etc/kubernetes/azure.json` 文件中
- 使用 [az aks create][az-aks-create] 命令自动生成服务主体时，会将服务主体凭据写入用于运行命令的计算机上的 `~/.azure/aksServicePrincipal.json` 文件中。
- 删除通过 [az aks create][az-aks-create] 创建的 AKS 群集时，不会删除自动创建的服务主体。
    - 若要删除服务主体，请首先使用 [az ad app list][az-ad-app-list] 获取服务主体的 ID。 以下示例查询名为 *myAKSCluster* 的群集，然后使用 [az ad app delete][az-ad-app-delete] 删除应用 ID。 将以下名称替换为你自己的值：

        ```azurecli
        az ad app list --query "[?displayName=='myAKSCluster'].{Name:displayName,Id:appId}" --output table
        az ad app delete --id <appId>
        ```

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure Active Directory 服务主体，请参阅[应用程序和服务主体对象][service-principal]

<!-- LINKS - internal -->
[aad-service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md
[az-ad-app-list]: /cli/azure/ad/app#az-ad-app-list
[az-ad-app-delete]: /cli/azure/ad/app#az-ad-app-delete
[az-aks-create]: /cli/azure/aks#az-aks-create
