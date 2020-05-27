---
title: 在 Azure Kubernetes 服务中使用 Azure AD
description: 了解如何在 Azure Kubernetes 服务 (AKS) 中使用 Azure AD
services: container-service
manager: gwallace
ms.topic: article
ms.date: 05/11/2020
ms.openlocfilehash: 67f5f707ad2971551e3c9623dd5c07ad880afcf2
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211140"
---
# <a name="integrate-azure-ad-in-azure-kubernetes-service-preview"></a>在 Azure Kubernetes 服务（预览）中集成 Azure AD

> [!Note]
> 集成了 AAD (Azure Active Directory) 的现有 AKS 群集不受新的 AKS 托管 AAD 体验的影响。

Azure AD 与 AKS 托管 AAD 的集成旨在简化 Azure AD 集成体验，用户以前需要创建客户端应用和服务器应用，而且还需要由 Azure AD 租户授予目录读取权限。 在新版本中，AKS 资源提供程序为你管理客户端应用和服务器应用。

## <a name="limitations"></a>限制

* 暂无法将现有的 AKS AAD 集成群集升级到新的 AKS 托管 AAD 体验。

> [!IMPORTANT]
> AKS 预览功能是可选择启用的自助功能。 预览功能是“按现状”和“按可用”提供的，不包括在服务级别协议和有限保证中。 AKS 预览功能是由客户支持尽最大努力部分覆盖。 因此，这些功能并不适合用于生产。 有关详细信息，请参阅以下支持文章：
>
> - [AKS 支持策略](support-policies.md)
> - [Azure 支持常见问题](faq.md)

## <a name="before-you-begin"></a>开始之前

> [!Important]
> 必须使用版本不低于 1.18 的 Kubectl

必须已安装以下资源：

- Azure CLI 版本 2.5.1 或更高版本
- aks-preview 0.4.38 扩展
- 版本不低于 [1.18](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1180) 的 Kubectl

若要安装/更新 aks-preview 扩展或更高版本，请运行以下 Azure CLI 命令：

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

若要安装 kubectl，请运行以下命令：

```azurecli
sudo az aks install-cli
kubectl version --client
```

对于其他操作系统，请遵循[这些说明](https://kubernetes.io/docs/tasks/tools/install-kubectl/)。

> [!CAUTION]
> 暂无法取消注册在订阅上注册的功能。 如果你启用某些预览功能，在订阅中随后创建的所有 AKS 群集都可能使用默认值。 不要在生产订阅上启用预览功能。 相反，请改用单独的订阅来测试预览功能并收集反馈。

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

状态可能需要几分钟才显示为“已注册”。 可以使用 [az feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) 命令来检查注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

当状态显示为“已注册”时，使用 [az provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) 命令来刷新 `Microsoft.ContainerService` 资源提供程序的注册：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>创建已启用 Azure AD 的 AKS 群集

现在，可以运行下面的 CLI 命令来创建 AKS 群集。

首先，创建 Azure 资源组：

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

然后，创建 AKS 群集：

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad
```
上面的命令创建了一个三节点 AKS 群集，但是在默认情况下，创建此群集的用户不是有权访问此群集的组的成员。 此用户需要创建一个 Azure AD 组，将自己添加为组成员，然后更新群集，如下所示。 请按照[此处](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)的说明操作

在创建组并将自己（和其他人）添加为成员后，可以运行下面的命令，以使用 Azure AD 组来更新群集

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```
或者，如果你先创建组和添加成员，可以运行下面的命令，以在创建时启用 Azure AD 组：

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```

成功创建的 AKS 托管 AAD 群集的响应正文中包含以下部分
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

## <a name="access-an-azure-ad-enabled-cluster"></a>访问已启用 Azure AD 的群集
若要获取用于访问群集的管理员凭据，请执行以下操作：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```
现在使用 kubectl get nodes 命令来查看群集中的节点：

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```

若要获取用于访问群集的用户凭据，请执行以下操作：
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
按照说明进行登录。

你会收到：必须登录服务器(未授权)

上面的用户收到一条错误消息，因为此用户不属于有权访问群集的组。

## <a name="next-steps"></a>后续步骤

* 了解 [Azure AD 基于角色的访问控制][azure-ad-rbac]。
* 使用 [kubelogin](https://github.com/Azure/kubelogin) 来访问在 kubectl 中不可用的 Azure 身份验证功能。

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
