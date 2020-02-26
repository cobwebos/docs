---
title: 概念 - Azure Kubernetes 服务 (AKS) 中的访问和标识
description: 了解 Azure Kubernetes 服务 (AKS) 中的访问和标识，包括 Azure Active Directory 集成、Kubernetes 基于角色的访问控制 (RBAC) 以及角色和绑定。
services: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: e4945535417f7d8d33308121267ba97e1f835e13
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77596362"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 的访问和标识选项

可通过不同的方式来对 Kubernetes 群集进行身份验证和保护。 使用基于角色的访问控制 (RBAC)，可以仅向用户或组授予对所需资源的访问权限。 借助 Azure Kubernetes 服务 (AKS)，可以通过使用 Azure Active Directory 进一步增强安全性和权限结构。 这些方法有助于保护应用程序工作负载以及客户数据。

本文介绍了可帮助在 AKS 中进行身份验证和分配权限的核心概念：

- [Kubernetes 服务帐户](#kubernetes-service-accounts)
- [Azure Active Directory 集成](#azure-active-directory-integration)
- [基于角色的访问控制 (RBAC)](#role-based-access-controls-rbac)
- [角色和 ClusterRole](#roles-and-clusterroles)
- [RoleBinding 和 ClusterRoleBinding](#rolebindings-and-clusterrolebindings)

## <a name="kubernetes-service-accounts"></a>Kubernetes 服务帐户

Kubernetes 中的一个主要用户类型是“服务帐户”。 服务帐户存在于 Kubernetes API 中并由 Kubernetes API 进行管理。 服务帐户的凭据存储为 Kubernetes 机密，可供得到授权的 Pod 用于与 API 服务器进行通信。 大多数 API 请求都会提供服务帐户或普通用户帐户的身份验证令牌。

普通用户帐户允许人工管理员或开发人员进行更为传统的访问，而不仅仅是服务和进程。 Kubernetes 本身并不提供用于存储常规用户帐户和密码的标识管理解决方案。 而是将外部标识解决方案集成到 Kubernetes 中。 对于 AKS 群集，此集成标识解决方案就是 Azure Active Directory。

有关 Kubernetes 中的标识选项的详细信息，请参阅[Kubernetes authentication][kubernetes-authentication]。

## <a name="azure-active-directory-integration"></a>Azure Active Directory 集成

可通过集成 Azure Active Directory (AD) 增强 AKS 群集的安全性。 Azure AD 是基于数十年的企业标识管理经验推出的基于云的多租户目录，也是一种将核心目录服务、应用程序访问管理和标识保护相结合的标识管理服务。 借助 Azure AD，可以将本地标识集成到 AKS 群集中，提供帐户管理和安全性的单一源。

![Azure Active Directory 与 AKS 群集集成](media/concepts-identity/aad-integration.png)

借助集成了 Azure AD 的 AKS 群集，可授权用户或组访问一个命名空间或多个群集内的 Kubernetes 资源。 若要获取 `kubectl` 配置上下文，用户可以运行[az aks get 凭据][az-aks-get-credentials]命令。 随后在用户使用 `kubectl` 与 AKS 群集进行交互时，系统会提示他们使用自己的 Azure AD 凭据登录。 此方法提供用户帐户管理和密码凭据的单一源。 用户只能访问由群集管理员定义的资源。

AKS 群集中的 Azure AD 身份验证使用 OpenID Connect，后者是构建在 OAuth 2.0 协议顶层的标识层。 OAuth 2.0 定义获取和使用访问令牌以访问受保护资源的机制，而 OpenID Connect 实现身份验证，作为对 OAuth 2.0 授权过程的扩展。 有关 OpenID Connect 的详细信息，请参阅[OPEN ID connect 文档][openid-connect]。 为了验证通过 OpenID Connect 从 Azure AD 获取的身份验证令牌，AKS 群集使用 Kubernetes Webhook 令牌身份验证。 有关详细信息，请参阅[Webhook 令牌身份验证文档][webhook-token-docs]。

## <a name="role-based-access-controls-rbac"></a>基于角色的访问控制 (RBAC)

为了精确地筛选用户可以执行的操作，Kubernetes 采用基于角色的访问控制 (RBAC)。 使用此控制机制，可以向用户或用户组分配执行各种操作的权限，例如创建或修改资源，或者查看正在运行的应用程序工作负载的日志。 可将这些权限的范围限制为单个命名空间，也可以授予面向整个 AKS 群集的权限。 使用 Kubernetes RBAC，可通过创建“角色”来定义权限，然后通过“角色绑定”将这些角色分配给用户。

有关详细信息，请参阅[使用 RBAC 授权][kubernetes-rbac]。

### <a name="azure-role-based-access-controls-rbac"></a>Azure 基于角色的访问控制 (RBAC)
另一个用于控制资源访问权限的机制是 Azure 基于角色的访问控制 (RBAC)。 Kubernetes RBAC 用于 AKS 群集中的资源，而 Azure RBAC 用于 Azure 订阅中的资源。 使用 Azure RBAC，可创建“角色定义”，描述要应用的权限。 随后可在特定范围内向用户或组分配此角色定义，该范围可以是单个资源、资源组或整个订阅。

有关详细信息，请参阅[什么是 AZURE RBAC？][azure-rbac]

## <a name="roles-and-clusterroles"></a>角色和 ClusterRole

使用 Kubernetes RBAC 向用户分配权限之前，请先将这些权限定义为“角色”。 Kubernetes 角色可“授予”权限。 不存在“拒绝”权限的概念。

角色用于授予命名空间内的权限。 若需要针对整个群集或给定命名空间外的群集资源来授予权限，可以改用“ClusterRole”。

ClusterRole 的工作原理与授予对资源的权限相同，但前者可应用于整个群集而非特定命名空间中的资源。

## <a name="rolebindings-and-clusterrolebindings"></a>RoleBinding 和 ClusterRoleBinding

定义了角色来授予针对资源的权限后，可通过 RoleBinding 来分配这些 Kubernetes RBAC 权限。 若 AKS 群集与 Azure Active Directory 集成，向 Azure AD 用户授予在群集中执行操作的权限的过程就称为“绑定”。

角色绑定用于针对给定命名空间分配角色。 此方法可以从逻辑上分离各 AKS 群集，使用户只能访问向其分配的命名空间中的应用程序资源。 若需要针对整个群集或给定命名空间外的群集资源来绑定角色，可以改用“ClusterRoleBinding”。

ClusterRoleBinding 的工作原理与向用户绑定角色相同，但前者可应用于整个群集而非特定命名空间中的资源。 使用此方法，可向管理员或支持工程师授予对 AKS 群集中所有资源的访问权限。

## <a name="next-steps"></a>后续步骤

若要开始 Azure AD 和 Kubernetes RBAC，请参阅将[Azure Active Directory 与 AKS 集成][aks-aad]。

有关相关的最佳实践，请参阅[AKS 中身份验证和授权的最佳实践][operator-best-practices-identity]。

有关核心 Kubernetes 和 AKS 概念的详细信息，请参阅以下文章：

- [Kubernetes/AKS 群集和工作负荷][aks-concepts-clusters-workloads]
- [Kubernetes/AKS 安全性][aks-concepts-security]
- [Kubernetes/AKS 虚拟网络][aks-concepts-network]
- [Kubernetes/AKS 存储][aks-concepts-storage]
- [Kubernetes/AKS scale][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: azure-ad-integration-cli.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
