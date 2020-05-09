---
title: 限制对 Azure Kubernetes 服务 (AKS) 中的 kubeconfig 的访问
description: 了解如何控制群集管理员和群集用户对 Kubernetes 配置文件 (kubeconfig) 的访问
services: container-service
ms.topic: article
ms.date: 05/06/2020
ms.openlocfilehash: 87f4dc18fc595242117e10233d4fecd77e66373f
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82890896"
---
# <a name="use-azure-role-based-access-controls-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>使用 Azure 基于角色的访问控制定义对 Azure Kubernetes 服务 (AKS) 中的 Kubernetes 配置文件的访问

可以使用 `kubectl` 工具来与 Kubernetes 群集交互。 在 Azure CLI 中，可以轻松获取所需的访问凭据和配置信息，以使用 `kubectl` 连接到 AKS 群集。 若要限制谁可以获取该 Kubernetes 配置 (*kubeconfig*) 信息及限制其拥有的权限，可以使用 Azure 基于角色的访问控制 (RBAC)。

本文介绍如何分配 RBAC 角色用于限制谁可以获取 AKS 群集的配置信息。

## <a name="before-you-begin"></a>准备阶段

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

本文还要求运行 Azure CLI 2.0.65 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

## <a name="available-cluster-roles-permissions"></a>可用的群集角色权限

使用 `kubectl` 工具与 AKS 群集交互时，将使用一个定义了群集连接信息的配置文件。 此配置文件通常存储在 *~/.kube/config* 中。可在此 *kubeconfig* 文件中定义多个群集。 使用 [kubectl config use-context][kubectl-config-use-context] 命令在群集之间切换。

使用 [az aks get-credentials][az-aks-get-credentials] 命令可以获取 AKS 群集的访问凭据，并将其合并到 *kubeconfig* 文件中。 可以使用 Azure 基于角色的访问控制 (RBAC) 来控制对这些凭据的访问。 使用这些 Azure RBAC 角色可以定义谁能够检索 *kubeconfig* 文件，以及他们在群集中拥有的权限。

有两个内置角色：

* **Azure Kubernetes 服务群集管理员角色**  
  * 允许访问 *Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action* API 调用。 此 API 调用[列出群集管理员凭据][api-cluster-admin]。
  * 下载 *clusterAdmin* 角色的 *kubeconfig*。
* **Azure Kubernetes 服务群集用户角色**
  * 允许访问 *Microsoft.ContainerService/managedClusters/listClusterUserCredential/action* API 调用。 此 API 调用[列出群集用户凭据][api-cluster-user]。
  * 下载 *clusterUser* 角色的 *kubeconfig*。

这些 RBAC 角色可以应用到 Azure Active Directory (AD) 用户或组。

> [!NOTE]
> 在使用 Azure AD 的群集上，具有*clusterUser*角色的用户有一个提示登录的空*kubeconfig*文件。 登录后，用户可以根据其 Azure AD 用户或组设置进行访问。 具有 clusterAdmin  角色的用户拥有管理员访问权限。
>
> 不使用 Azure AD 的群集仅使用 clusterAdmin  角色。

## <a name="assign-role-permissions-to-a-user-or-group"></a>将角色权限分配给用户或组

若要分配某个可用角色，需要获取 AKS 群集的资源 ID 以及 Azure AD 用户帐户或组的 ID。 以下示例命令：

* 使用 [az aks show][az-aks-show] 命令获取 *myResourceGroup* 资源组中名为 *myAKSCluster* 的群集的群集资源 ID。 请根据需要提供自己的群集和资源组名称。
* 使用 [az account show][az-account-show] 和 [az ad user show][az-ad-user-show] 命令获取用户 ID。
* 最后，使用 [az role assignment create][az-role-assignment-create] 命令分配角色。

以下示例将 Azure Kubernetes 服务群集管理员角色分配给单个用户帐户： 

```azurecli-interactive
# Get the resource ID of your AKS cluster
AKS_CLUSTER=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query id -o tsv)

# Get the account credentials for the logged in user
ACCOUNT_UPN=$(az account show --query user.name -o tsv)
ACCOUNT_ID=$(az ad user show --id $ACCOUNT_UPN --query objectId -o tsv)

# Assign the 'Cluster Admin' role to the user
az role assignment create \
    --assignee $ACCOUNT_ID \
    --scope $AKS_CLUSTER \
    --role "Azure Kubernetes Service Cluster Admin Role"
```

> [!TIP]
> 若要将权限分配给 Azure AD 组，请使用组而不是用户的对象 ID 更新在上一示例中显示的 `--assignee` 参数。   若要获取组的对象 ID，请使用 [az ad group show][az-ad-group-show] 命令。 以下示例获取名为 *appdev* 的 Azure AD 组的对象 ID：`az ad group show --group appdev --query objectId -o tsv`

可根据需要将上述分配更改为“群集用户角色”。 

以下示例输出显示已成功创建角色分配：

```
{
  "canDelegate": null,
  "id": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/providers/Microsoft.Authorization/roleAssignments/b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "name": "b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "principalId": "946016dd-9362-4183-b17d-4c416d1f8f61",
  "resourceGroup": "myResourceGroup",
  "roleDefinitionId": "/subscriptions/<guid>/providers/Microsoft.Authorization/roleDefinitions/0ab01a8-8aac-4efd-b8c2-3ee1fb270be8",
  "scope": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-and-verify-the-configuration-information"></a>获取并验证配置信息

分配 RBAC 角色后，使用 [az aks get-credentials][az-aks-get-credentials] 命令获取 AKS 群集的 *kubeconfig* 定义。 以下示例获取 *--admin* 凭据，如果为用户分配了“群集管理员角色”，则这些凭据可正常运行： 

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

然后，可以使用 [kubectl config view][kubectl-config-view] 命令来验证群集上下文是否显示已应用管理员配置信息： 

```
$ kubectl config view

apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://myaksclust-myresourcegroup-19da35-4839be06.hcp.eastus.azmk8s.io:443
  name: myAKSCluster
contexts:
- context:
    cluster: myAKSCluster
    user: clusterAdmin_myResourceGroup_myAKSCluster
  name: myAKSCluster-admin
current-context: myAKSCluster-admin
kind: Config
preferences: {}
users:
- name: clusterAdmin_myResourceGroup_myAKSCluster
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
    token: e9f2f819a4496538b02cefff94e61d35
```

## <a name="remove-role-permissions"></a>删除角色权限

若要删除角色分配，请使用 [az role assignment delete][az-role-assignment-delete] 命令。 指定在前面命令中获取的帐户 ID 和群集资源 ID。 如果将角色分配给组而不是用户，请为 `--assignee` 参数指定相应的组对象 ID 而不是帐户对象 ID：

```azurecli-interactive
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>后续步骤

若要增强在访问 AKS 群集时的安全性，请[集成 Azure Active Directory 身份验证][aad-integration]。

<!-- LINKS - external -->
[kubectl-config-use-context]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config
[kubectl-config-view]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[api-cluster-admin]: /rest/api/aks/managedclusters/listclusteradmincredentials
[api-cluster-user]: /rest/api/aks/managedclusters/listclusterusercredentials
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-role-assignment-delete]: /cli/azure/role/assignment#az-role-assignment-delete
[aad-integration]: azure-ad-integration.md
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
