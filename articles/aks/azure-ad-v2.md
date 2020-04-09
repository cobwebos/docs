---
title: 在 Azure 库伯奈斯服务中使用 Azure AD
description: 了解如何在 Azure 库伯奈斯服务 （AKS） 中使用 Azure AD
services: container-service
manager: gwallace
ms.topic: article
ms.date: 03/24/2020
ms.openlocfilehash: 27abdfe28e2594c98778b51532fbd22f95bfa3ac
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984702"
---
# <a name="integrate-azure-ad-in-azure-kubernetes-service-preview"></a>在 Azure 库伯奈斯服务中集成 Azure AD（预览版）

> [!Note]
> 具有 AD 集成的现有 AKS v1 群集不受新的 AKS v2 体验的影响。

Azure AD 与 AKS v2 集成旨在简化 Azure AD 与 AKS v1 体验的集成，其中用户需要创建客户端应用、服务器应用，并要求 Azure AD 租户授予目录读取权限。 在新版本中，AKS 资源提供程序为您管理客户端和服务器应用。

## <a name="limitations"></a>限制

* 当前无法将启用 Azure AD 的 AKS v1 群集升级到 v2 体验。

> [!IMPORTANT]
> AKS 预览功能提供自助服务、选择加入功能。 预览版提供"根据"和"可用"，并且不在服务级别协议和有限保修中。 客户支持会尽力而为，部分涵盖 AKS 预览。 因此，这些功能不应用于生产。 有关详细信息，请参阅以下支持文章：
>
> - [AKS 支持策略](support-policies.md)
> - [Azure 支持常见问题](faq.md)

## <a name="before-you-begin"></a>在开始之前

您必须安装以下资源：

- Azure CLI，版本 2.2.0 或更高版本
- aks 预览 0.4.38 扩展
- 库布克特尔最低版本为[1.18 beta](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#client-binaries)

要安装/更新 aks 预览扩展或更高版本，请使用以下 Azure CLI 命令：

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

要安装 kubectl，请使用以下操作：

```azurecli
sudo az aks install-cli
kubectl version --client
```

[将这些说明](https://kubernetes.io/docs/tasks/tools/install-kubectl/)用于其他操作系统。

> [!CAUTION]
> 在订阅上注册功能后，当前无法取消注册该功能。 启用某些预览功能时，可能会将默认值用于订阅中以后创建的所有 AKS 群集。 不要在生产订阅上启用预览功能。 相反，请使用单独的订阅来测试预览功能并收集反馈。

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

状态可能需要几分钟才能显示为 **"已注册**"。 您可以使用[az 要素列表](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list)命令检查注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

当状态显示为已注册时，请使用 az`Microsoft.ContainerService`提供程序寄存器命令刷新资源提供程序[的注册](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register)：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>创建启用 Azure AD 的 AKS 群集

现在，您可以使用以下 CLI 命令创建 AKS 群集。

首先，创建 Azure 资源组：

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

然后，创建 AKS 群集：

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad
```
上述命令创建一个三个节点 AKS 群集，但默认情况下创建群集的用户不是有权访问此群集的组的成员。 此用户需要创建 Azure AD 组，将自己添加为组的成员，然后按如下所示更新群集。 在此处[按照说明](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)操作

创建组并添加自己作为成员（和其他人）后，可以使用以下命令使用 Azure AD 组更新群集

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster [--aad-admin-group-object-ids <id1,id2>] [--aad-tenant-id <id>]
```
或者，如果首先创建组并添加成员，则可以使用以下命令在创建时间启用 Azure AD 组，

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id1,id2>] [--aad-tenant-id <id>]
```

成功创建 Azure AD v2 群集在响应正文中具有以下部分
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

群集在几分钟内创建。

## <a name="access-an-azure-ad-enabled-cluster"></a>访问启用 Azure AD 的群集
要获取访问群集的管理员凭据，可以执行以下操作：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```
现在使用 kubectl 获取节点命令查看群集中的节点：

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```

要获取访问群集的用户凭据，可以：
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
按照说明登录。

您收到：**您必须登录到服务器（未经授权）**

上述用户会收到错误，因为用户不是有权访问群集的组的一部分。

## <a name="next-steps"></a>后续步骤

了解[Azure AD 角色访问控制][azure-ad-rbac]。

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
