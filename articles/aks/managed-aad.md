---
title: 在 Azure Kubernetes 服务中使用 Azure AD
description: 了解如何使用 Azure Kubernetes Service （AKS）中的 Azure AD
services: container-service
manager: gwallace
ms.topic: article
ms.date: 05/11/2020
ms.openlocfilehash: 67f5f707ad2971551e3c9623dd5c07ad880afcf2
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211140"
---
# <a name="integrate-azure-ad-in-azure-kubernetes-service-preview"></a>集成 Azure Kubernetes Service （预览版）中的 Azure AD

> [!Note]
> 使用 AAD （Azure Active Directory）集成的现有 AKS 群集不受新的 AKS 管理 AAD 体验的影响。

与 AKS 管理的 AAD Azure AD 集成旨在简化 Azure AD 集成体验，其中用户之前需要创建客户端应用、服务器应用，并需要 Azure AD 租户授予目录读取权限。 在新版本中，AKS 资源提供程序管理客户端和服务器应用。

## <a name="limitations"></a>限制

* 目前不能将现有的 AKS AAD 集成群集升级到新的 AKS 管理 AAD 体验。

> [!IMPORTANT]
> AKS 预览版功能提供自助服务，可选择使用。 预览按 "原样" 提供，并从服务级别协议和有限担保中排除。 AKS 预览版是一项尽力的客户支持部分。 因此，这些功能不应用于生产。 有关详细信息，请参阅以下支持文章：
>
> - [AKS 支持策略](support-policies.md)
> - [Azure 支持常见问题](faq.md)

## <a name="before-you-begin"></a>开始之前

> [!Important]
> 必须使用最低版本为1.18 的 Kubectl

必须安装下列资源：

- Azure CLI 2.5.1 或更高版本
- Aks-preview 0.4.38 扩展
- Kubectl，最低版本为[1.18](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1180)

若要安装/更新 aks 或更高版本，请使用以下 Azure CLI 命令：

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

若要安装 kubectl，请使用以下内容：

```azurecli
sudo az aks install-cli
kubectl version --client
```

对于其他操作系统，请使用[这些说明](https://kubernetes.io/docs/tasks/tools/install-kubectl/)。

> [!CAUTION]
> 在订阅上注册功能后，当前无法取消注册该功能。 启用某些预览功能后，默认值可用于在订阅中创建的所有 AKS 群集。 不要对生产订阅启用预览功能。 请改用单独的订阅来测试预览功能并收集反馈。

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

状态显示为 "**已注册**" 可能需要几分钟时间。 您可以使用[az feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list)命令检查注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

当状态显示为 "已注册" 时， `Microsoft.ContainerService` 使用[az provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register)命令刷新资源提供程序的注册：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>创建启用了 Azure AD 的 AKS 群集

现在可以使用以下 CLI 命令来创建 AKS 群集。

首先，创建一个 Azure 资源组：

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

然后，创建 AKS 群集：

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad
```
上述命令将创建一个三节点 AKS 群集，但默认情况下，创建该群集的用户不是有权访问此群集的组的成员。 此用户需要创建一个 Azure AD 组，将自己添加为组的成员，然后更新该群集，如下所示。 按照[此处](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)的说明操作

创建组并将自己（和其他）作为成员添加后，可以使用以下命令通过 Azure AD 组更新群集。

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```
或者，如果你首次创建组并添加成员，则可以使用以下命令在 create time 启用 Azure AD 组：

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```

成功创建 AKS 管理的 AAD 群集后，响应正文中将包含以下部分
```
"Azure ADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

群集是在几分钟内创建的。

## <a name="access-an-azure-ad-enabled-cluster"></a>访问启用 Azure AD 的群集
获取用于访问群集的管理员凭据：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```
现在，使用 kubectl get 节点命令查看群集中的节点：

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```

获取用户凭据以访问群集：
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
按照说明进行操作以登录。

你会收到：**你必须登录到服务器（未授权）**

上面的用户将收到错误，因为该用户不是有权访问群集的组的一部分。

## <a name="next-steps"></a>后续步骤

* 了解[Azure AD 基于角色的访问控制][azure-ad-rbac]。
* 使用[kubelogin](https://github.com/Azure/kubelogin)访问 kubectl 中不可用的 Azure 身份验证功能。

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
