---
title: 在 Azure Kubernetes 服务中使用 Azure AD
description: 了解如何在 Azure Kubernetes 服务 (AKS) 中使用 Azure AD
services: container-service
manager: gwallace
author: mlearned
ms.topic: article
ms.date: 06/25/2020
ms.author: mlearned
ms.openlocfilehash: f22b79cb8a730fb9c28dd1a208ab672473218b79
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86105942"
---
# <a name="aks-managed-azure-active-directory-integration-preview"></a>AKS-托管 Azure Active Directory 集成（预览版）

> [!NOTE]
> 具有 Azure Active Directory （Azure AD）集成的现有 AKS （Azure Kubernetes Service）群集不受新的 AKS 管理的 Azure AD 体验的影响。

AKS 托管 Azure AD 集成旨在简化 Azure AD 集成体验，其中用户以前需要创建客户端应用、服务器应用，并需要 Azure AD 租户授予目录读取权限。 在新版本中，AKS 资源提供程序为你管理客户端应用和服务器应用。

## <a name="azure-ad-authentication-overview"></a>Azure AD 身份验证概述

群集管理员可以根据用户标识或目录组成员身份来配置 Kubernetes 基于角色的访问控制 (RBAC)。 使用 OpenID Connect 向 AKS 群集提供 Azure AD 身份验证。 OpenID Connect 是构建在 OAuth 2.0 协议顶层的标识层。 有关 OpenID Connect 的详细信息，请参阅[OPEN ID connect 文档][open-id-connect]。

有关详细信息，请参阅[Azure Active Directory 集成概念文档](concepts-identity.md#azure-active-directory-integration)中的 AAD 集成流。

## <a name="limitations"></a>限制

* 目前不能将现有的 AKS Azure AD 集成的群集升级到新的 AKS 管理的 Azure AD 体验。

> [!IMPORTANT]
> AKS 预览功能是可选择启用的自助功能。 预览功能是“按现状”和“按可用”提供的，不包括在服务级别协议和有限保证中。 AKS 预览功能是由客户支持尽最大努力部分覆盖。 因此，这些功能并不适合用于生产。 有关详细信息，请参阅以下支持文章：
>
> - [AKS 支持策略](support-policies.md)
> - [Azure 支持常见问题](faq.md)

## <a name="before-you-begin"></a>开始之前

* 通过导航到 Azure 门户找到你的 Azure 帐户租户 ID，然后选择 Azure Active Directory > 属性 > 目录 ID

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

若要安装 kubectl，请使用以下命令：

```azurecli
sudo az aks install-cli
kubectl version --client
```

对于其他操作系统，请遵循[这些说明](https://kubernetes.io/docs/tasks/tools/install-kubectl/)。

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

使用以下 CLI 命令创建 AKS 群集。

创建 Azure 资源组：

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

您可以使用现有的 Azure AD 组，或创建一个新组。 你需要 Azure AD 组的对象 ID。

```azurecli-interactive
# List existing groups in the directory
az ad group list
```

若要为群集管理员创建新的 Azure AD 组，请使用以下命令：

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name MyDisplay --mail-nickname MyDisplay
```

创建 AKS 群集，并为 Azure AD 组启用管理访问权限

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```

成功创建 AKS 托管 Azure AD 群集的响应正文中包含以下部分
```
"AADProfile": {
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

需要[Azure Kubernetes Service 群集用户](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#azure-kubernetes-service-cluster-user-role)内置角色才能执行以下步骤。

获取用户凭据以访问群集：
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
按照说明进行登录。

使用 kubectl get 节点命令查看群集中的节点：

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
配置[基于角色的访问控制（RBAC）](https://docs.microsoft.com/azure/aks/azure-ad-rbac) ，为群集配置其他安全组。

## <a name="troubleshooting-access-issues-with-azure-ad"></a>排查 Azure AD 的访问问题

> [!Important]
> 下面所述的步骤将绕过普通 Azure AD 组身份验证。 仅在紧急情况下使用它们。

如果你被永久阻止，不能访问具有群集访问权限的有效 Azure AD 组，你仍可以获取管理员凭据以直接访问群集。

若要执行这些步骤，你需要具有[Azure Kubernetes Service 群集管理员](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#azure-kubernetes-service-cluster-admin-role)内置角色的访问权限。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```

## <a name="non-interactive-sign-in-with-kubelogin"></a>Kubelogin 中的非交互式登录

有一些非交互式方案，如持续集成管道，当前不适用于 kubectl。 你可以使用 [`kubelogin`](https://github.com/Azure/kubelogin) 来访问包含非交互式服务主体登录的群集。

## <a name="next-steps"></a>后续步骤

* 了解[适用于 Kubernetes 授权的 AZURE RBAC 集成][azure-rbac-integration]
* 了解[Azure AD 与 KUBERNETES RBAC 集成][azure-ad-rbac]。
* 使用[kubelogin](https://github.com/Azure/kubelogin)访问 kubectl 中不可用的 Azure 身份验证功能。
* 了解有关[AKS 和 Kubernetes 标识概念][aks-concepts-identity]的详细信息。
* 使用[Azure 资源管理器（ARM）模板][aks-arm-template]创建启用了 AKS 的 Azure AD 启用群集。

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: https://docs.microsoft.com/azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - Internal -->
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md

