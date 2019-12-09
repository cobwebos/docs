---
title: 适用于 Azure Kubernetes 服务 (AKS) 的服务主体
description: 在 Azure Kubernetes 服务 (AKS) 中为群集创建和管理 Azure Active Directory 服务主体
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: mlearned
ms.openlocfilehash: ded3fc97c4cdf041fdf50d7b4aa9a9b2fbdf1c84
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74913486"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>使用 Azure Kubernetes 服务 (AKS) 的服务主体

要与 Azure Api 交互，AKS 群集需要[Azure Active Directory （AD）服务主体][aad-service-principal]。 需要服务主体才能动态创建和管理其他 Azure 资源，例如 Azure 负载均衡器或容器注册表 (ACR)。

本文介绍如何创建和使用适用于 AKS 群集的服务主体。

## <a name="before-you-begin"></a>开始之前

若要创建 Azure AD 服务主体，必须具有相应的权限，能够向 Azure AD 租户注册应用程序，并将应用程序分配到订阅中的角色。 如果没有必需的权限，可能需要请求 Azure AD 或订阅管理员来分配必需的权限，或者预先创建一个可以与 AKS 群集配合使用的服务主体。

如果你使用不同 Azure AD 租户中的服务主体，则需要考虑部署群集时可用的权限。 你可能没有适当的权限来读取和写入目录信息。 有关详细信息，请参阅[Azure Active Directory 中的默认用户权限是什么？][azure-ad-permissions]

还需要安装并配置 Azure CLI 版本2.0.59 或更高版本。 运行  `az --version` 即可查找版本。 如果需要安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

## <a name="automatically-create-and-use-a-service-principal"></a>自动创建和使用服务主体

在 Azure 门户或使用[az AKS create][az-aks-create]命令创建 AKS 群集时，Azure 可以自动生成服务主体。

在下述 Azure CLI 示例中，尚未指定服务主体。 在此方案中，Azure CLI 为 AKS 群集创建一个服务主体。 若要成功完成此操作，Azure 帐户必须具有创建服务主体所需的相应权限。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup
```

## <a name="manually-create-a-service-principal"></a>手动创建服务主体

若要使用 Azure CLI 手动创建服务主体，请使用[az ad sp create-rbac][az-ad-sp-create]命令。 在以下示例中，`--skip-assignment` 参数阻止系统分配更多的默认分配。

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment --name myAKSClusterServicePrincipal
```

输出类似于以下示例。 记下你自己的 `appId`和 `password`。 在下一部分创建 AKS 群集时，会使用这些值。

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "myAKSClusterServicePrincipal",
  "name": "http://myAKSClusterServicePrincipal",
  "password": "e763725a-5eee-40e8-a466-dc88d980f415",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

## <a name="specify-a-service-principal-for-an-aks-cluster"></a>指定适用于 AKS 群集的服务主体

若要在使用[az AKS create][az-aks-create]命令创建 AKS 群集时使用现有的服务主体，请使用 `--service-principal` 和 `--client-secret` 参数指定[az ad sp create-rbac][az-ad-sp-create]命令的输出中的 `appId` 和 `password`：

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

## <a name="delegate-access-to-other-azure-resources"></a>委托对其他 Azure 资源的访问权限

AKS 群集的服务主体可以用来访问其他资源。 例如，如果想要将 AKS 群集部署到现有 Azure 虚拟网络子网或连接到 Azure 容器注册表（ACR），则需要将对这些资源的访问权限委派给服务主体。

若要委派权限，请使用[az role create create][az-role-assignment-create]命令创建角色分配。 将 `appId` 分配给特定范围，例如资源组或虚拟网络资源。 然后，通过角色定义服务主体对资源的具体权限，如以下示例所示：

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

资源的 `--scope` 需要是完整的资源 ID，例如 */subscriptions/\<guid\>/resourceGroups/myResourceGroup* 或 */subscriptions/\<guid\>/resourceGroups/myResourceGroupVnet/providers/Microsoft.Network/virtualNetworks/myVnet*

以下各部分详述了可能需要使用的常见委托。

### <a name="azure-container-registry"></a>Azure 容器注册表

如果使用 Azure 容器注册表（ACR）作为容器映像存储，则需要向 AKS 群集的服务主体授予权限以读取和提取映像。 目前，建议的配置是使用[az aks create][az-aks-create]或[az aks update][az-aks-update]命令与注册表集成，并为服务主体分配相应的角色。 有关详细步骤，请参阅通过 azure[容器注册表从 Azure Kubernetes 服务进行身份验证][aks-to-acr]。

### <a name="networking"></a>网络

可以使用高级网络，在该网络中，虚拟网络和子网或公共 IP 地址位于另一资源组中。 分配下列角色权限集之一：

- 创建[自定义角色][rbac-custom-role]并定义以下角色权限：
  - *Microsoft.Network/virtualNetworks/subnets/join/action*
  - *Microsoft.Network/virtualNetworks/subnets/read*
  - *Microsoft.Network/virtualNetworks/subnets/write*
  - *Microsoft.Network/publicIPAddresses/join/action*
  - *Microsoft.Network/publicIPAddresses/read*
  - *Microsoft.Network/publicIPAddresses/write*
- 或者，将[网络参与者][rbac-network-contributor]内置角色分配到虚拟网络中的子网上

### <a name="storage"></a>存储空间

可能需要访问另一资源组中的现有磁盘资源。 分配下列角色权限集之一：

- 创建[自定义角色][rbac-custom-role]并定义以下角色权限：
  - *Microsoft.Compute/disks/read*
  - *Microsoft.Compute/disks/write*
- 或者，将[存储帐户参与者][rbac-storage-contributor]内置角色分配到资源组

### <a name="azure-container-instances"></a>Azure 容器实例

如果使用虚拟 Kubelet 与 AKS 集成并选择在与 AKS 群集分开的资源组中运行 Azure 容器实例 (ACI)，则必须在 ACI 资源组上授予 AKS 服务主体“参与者”权限。

## <a name="additional-considerations"></a>其他注意事项

使用 AKS 和 Azure AD 服务主体时，请牢记以下注意事项。

- Kubernetes 的服务主体是群集配置的一部分。 但是，请勿使用标识来部署群集。
- 默认情况下，服务主体凭据的有效期为一年。 你可以随时[更新或轮换服务主体凭据][update-credentials]。
- 每个服务主体都与一个 Azure AD 应用程序相关联。 Kubernetes 群集的服务主体可以与任何有效的 Azure AD 应用程序名称（例如 *https://www.contoso.org/example* ）相关联。 应用程序的 URL 不一定是实际的终结点。
- 指定服务主体**客户端 ID** 时，请使用 `appId` 的值。
- 在 Kubernetes 群集中的代理节点 Vm 中，服务主体凭据存储在文件中 `/etc/kubernetes/azure.json`
- 当你使用[az aks create][az-aks-create]命令自动生成服务主体时，服务主体凭据将写入用于运行命令的计算机上的文件 `~/.azure/aksServicePrincipal.json`。
- 如果删除由[az AKS create][az-aks-create]创建的 AKS 群集，则不会删除自动创建的服务主体。
    - 若要删除服务主体，请查询群集*servicePrincipalProfile* ，然后通过[az ad app delete][az-ad-app-delete]删除。 将以下资源组和群集名称替换为你自己的值：

        ```azurecli
        az ad sp delete --id $(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
        ```

## <a name="troubleshoot"></a>故障排除

Azure CLI 将缓存 AKS 群集的服务主体凭据。 如果这些凭据已过期，则会在部署 AKS 群集时遇到错误。 运行[az aks create][az-aks-create]时出现以下错误消息可能表示缓存的服务主体凭据存在问题：

```console
Operation failed with status: 'Bad Request'.
Details: The credentials in ServicePrincipalProfile were invalid. Please see https://aka.ms/aks-sp-help for more details.
(Details: adal: Refresh request failed. Status Code = '401'.
```

使用以下命令检查凭据文件的期限：

```console
ls -la $HOME/.azure/aksServicePrincipal.json
```

服务主体凭据的默认过期时间为一年。 如果你的*aksServicePrincipal*文件早于一年，请删除该文件，然后再次尝试部署 AKS 群集。

## <a name="next-steps"></a>后续步骤

有关 Azure Active Directory 服务主体的详细信息，请参阅[应用程序对象和服务主体对象][service-principal]。

有关如何更新凭据的信息，请参阅[在 AKS 中更新或轮换服务主体的凭据][update-credentials]。

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
[az-aks-update]: /cli/azure/aks#az-aks-update
[rbac-network-contributor]: ../role-based-access-control/built-in-roles.md#network-contributor
[rbac-custom-role]: ../role-based-access-control/custom-roles.md
[rbac-storage-contributor]: ../role-based-access-control/built-in-roles.md#storage-account-contributor
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[aks-to-acr]: cluster-container-registry-integration.md
[update-credentials]: update-credentials.md
[azure-ad-permissions]: ../active-directory/fundamentals/users-default-permissions.md
