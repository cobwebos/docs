---
title: 在 Azure Kubernetes 服务中使用 Azure AD
description: 了解如何在 Azure Kubernetes 服务 (AKS) 中使用 Azure AD
services: container-service
manager: gwallace
author: mlearned
ms.topic: article
ms.date: 06/25/2020
ms.author: mlearned
ms.openlocfilehash: bf635d37559d09e887a67be27c412bff7899127b
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86023391"
---
# <a name="integrate-aks-managed-azure-ad-preview"></a>集成 AKS 托管 Azure AD （预览版）

> [!Note]
> 具有 Azure Active Directory （Azure AD）集成的现有 AKS （Azure Kubernetes Service）群集不受新的 AKS 管理的 Azure AD 体验的影响。

与 AKS 管理的 Azure AD Azure AD 集成旨在简化 Azure AD 集成体验，其中用户以前需要创建客户端应用、服务器应用，并需要 Azure AD 租户授予目录读取权限。 在新版本中，AKS 资源提供程序为你管理客户端应用和服务器应用。

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
## <a name="azure-ad-authentication-overview"></a>Azure AD 身份验证概述

群集管理员可以根据用户标识或目录组成员身份来配置 Kubernetes 基于角色的访问控制 (RBAC)。 使用 OpenID Connect 向 AKS 群集提供 Azure AD 身份验证。 OpenID Connect 是构建在 OAuth 2.0 协议顶层的标识层。 有关 OpenID Connect 的详细信息，请参阅[OPEN ID connect 文档][open-id-connect]。

在 Kubernetes 群集内部，使用 Webhook 令牌身份验证来验证身份验证令牌。 Webhook 令牌身份验证作为 AKS 群集的一部分进行配置和管理。

## <a name="webhook-and-api-server"></a>Webhook 和 API 服务器

:::image type="content" source="media/aad-integration/auth-flow.png" alt-text="Webhook 和 API 服务器身份验证流":::

如上图所示，API 服务器调用 AKS webhook 服务器并执行以下步骤：

1. Kubectl 使用 Azure AD 客户端应用程序，通过[OAuth 2.0 设备授权流](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code)登录用户。
2. Azure AD 提供 access_token、id_token 和 refresh_token。
3. 用户使用来自 kubeconfig 的 access_token 向 kubectl 发出请求。
4. Kubectl 将 access_token 发送到 APIServer。
5. API 服务器配置了 Auth WebHook 服务器来执行验证。
6. 身份验证 webhook 服务器通过检查 Azure AD 的公共签名密钥，确认 JSON Web 令牌签名有效。
7. 服务器应用程序使用用户提供的凭据从 MS 图形 API 查询登录用户的组成员身份。
8. 将使用用户信息（例如访问令牌的用户主体名称（UPN）声明）和用户的组成员身份（基于对象 ID）将响应发送到 APIServer。
9. API 基于 Kubernetes Role/RoleBinding 执行授权决定。
10. 获得授权后，API 服务器会将响应返回到 kubectl。
11. Kubectl 为用户提供反馈。


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

若要为群集管理员创建 & 新的 Azure AD 组，请使用以下命令：

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

如果你被永久阻止，但不能访问具有群集访问权限的有效 Azure AD 组，则仍可获取管理员凭据以直接访问群集。

若要执行这些步骤，你需要具有[Azure Kubernetes Service 群集管理员](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#azure-kubernetes-service-cluster-admin-role)内置角色的访问权限。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```

## <a name="non-interactive-login-with-kubelogin"></a>具有 kubelogin 的非交互式登录

有一些非交互式方案，如持续集成管道，它们目前不适用于 kubectl。 你可以使用[kubelogin](https://github.com/Azure/kubelogin)来访问具有非交互式服务主体登录名的群集。

## <a name="next-steps"></a>后续步骤

* 了解 [Azure AD 基于角色的访问控制][azure-ad-rbac]。
* 使用 [kubelogin](https://github.com/Azure/kubelogin) 来访问在 kubectl 中不可用的 Azure 身份验证功能。
* 使用[Azure 资源管理器（ARM）模板][aks-arm-template]创建启用了 AKS 的 Azure AD 启用群集。

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: https://docs.microsoft.com/azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - Internal -->
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

