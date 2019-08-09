---
title: 将现有 Azure 订阅添加到租户 - Azure Active Directory | Microsoft Docs
description: 有关将现有 Azure 订阅添加到 Azure Active Directory 租户的说明。
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb4fa92d8b3c174cdf9b3695f8564cc11c1ad291
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851745"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>将 Azure 订阅关联或添加到 Azure Active Directory 租户

Azure 订阅与 Azure Active Directory (Azure AD) 建立了信任关系，即该订阅信任 Azure AD 对用户、服务和设备进行身份验证。 多个订阅可以信任同一个 Azure AD 目录，但每个订阅只能信任一个目录。

如果订阅过期，则将失去与该订阅关联的所有其他资源的访问权限。 但是，Azure AD Directory 仍保留在 Azure 中，可使用不同的 Azure 订阅关联和管理目录。

所有用户都有一个用于身份验证的“主”目录。 但是，用户还可在其他目录中作为来宾。 可在 Azure AD 中查看每位用户的主目录和来宾目录。

> [!Important]
> 当你将订阅与其他目录关联时，具有使用[基于角色的访问控制 (RBAC)](../../role-based-access-control/role-assignments-portal.md) 分配的角色的用户将失去其访问权限。 经典订阅管理员（服务管理员和共同管理员）也将失去其访问权限。
> 
> 此外，如果将 Azure Kubernetes 服务 (AKS) 群集移到其他订阅，或者将拥有该群集的订阅移到新租户，该群集将会由于失去角色分配和服务主体权限而丢失功能。 有关 AKS 的详细信息，请参阅 [Azure Kubernetes 服务 (AKS)](https://docs.microsoft.com/azure/aks/)。

## <a name="before-you-begin"></a>开始之前

要想关联或添加订阅，必须先执行以下任务：

1. 请查看下面包含更改及你可能受到的影响的列表：

    - 已使用 RBAC 为其分配了角色的用户将失去其访问权限
    - 服务管理员和共同管理员将失去其访问权限
    - 如果你有任何密钥保管库，这些密钥保管库将无法访问，而且你必须在关联后对其进行修复
    - 如果拥有资源（如虚拟机或逻辑应用）的任何托管标识，则将必须在关联后重新启用或重新创建这些标识
    - 如果拥有已注册的 Azure Stack，则将必须在关联后重新注册它

1. 使用符合以下条件的帐户登录：
    - 具有该订阅的[所有者](../../role-based-access-control/built-in-roles.md#owner)角色分配。 有关如何分配“所有者”角色的详细信息，请参阅[使用 RBAC 和 Azure 门户管理对 Azure 资源的访问权限](../../role-based-access-control/role-assignments-portal.md)。
    - 同时存在于当前目录（与订阅关联）和新目录（希望以后与订阅关联）中。 有关获取其他目录访问权限的详细信息，请参阅 [Azure Active Directory 管理员如何添加 B2B 协作用户？](../b2b/add-users-administrator.md)。

1. 请确保未使用 Azure 云服务提供商 (CSP) 订阅（MS-AZR-0145P、MS-AZR-0146P、MS-AZR-159P）、Microsoft 内部订阅 (MS-AZR-0015P) 或 Microsoft Imagine 订阅 (MS-AZR-0144P)。
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>将现有订阅关联到 Azure AD 目录

1. 登录，然后从 [Azure 门户中的“订阅”页面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)选择要使用的订阅。

2. 选择“更改目录”。

    ![订阅页面，其中突出显示了“更改目录”选项](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. 查看出现的任何警告，然后选择“更改”。

    ![“更改目录”页，显示要更改到的目录](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    订阅目录将发生更改并会显示一条成功消息。

    ![有关目录更改的成功消息](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)
4. 使用**目录切换**器来切换到新目录。 所有内容可能需要几个小时才能正常显示。 如果看起来太长, 请检查已移动订阅的**全局订阅筛选器**, 以确保它不会被隐藏。 你可能需要注销 Azure 门户并重新登录才能查看新目录。 

    !["目录切换器" 页, 包含示例信息](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

更改订阅目录是服务级操作，不会影响订阅的账单所有权。 帐户管理员仍可从[帐户中心](https://account.azure.com/subscriptions)更改服务管理员。 若要删除原始目录，必须将订阅的账单所有权转让给新的帐户管理员。若要详细了解如何转让账单所有权，请参阅[将 Azure 订阅所有权转让给其他帐户](../../billing/billing-subscription-transfer.md)。

## <a name="post-association-steps"></a>发布内容关联步骤
将订阅关联到其他目录后，可能还要执行其他必须执行才能恢复操作的步骤。

1. 如果有任何密钥保管库，则必须更改该密钥保管库租户 ID。 有关详细信息，请参阅[在订阅移动后更改密钥保管库租户 ID](../../key-vault/key-vault-subscription-move-fix.md)。

2. 如果已使用系统为资源分配的托管标识，则必须重新启用这些标识。 如果已使用用户分配的托管标识，则必须重新创建这些标识。 重新启用或重新创建托管标识后，必须重新建立分配给这些标识的权限。 有关详细信息, 请参阅[Azure 资源的托管标识是什么？](../managed-identities-azure-resources/overview.md)。

3. 如果已使用此订阅注册 Azure Stack，则必须重新注册。 有关详细信息，请参阅[将 Azure Stack 注册到 Azure](/azure-stack/operator/azure-stack-registration)。



## <a name="next-steps"></a>后续步骤

- 要创建新的 Azure AD 租户，请参阅[访问 Azure Active Directory 以创建新租户](active-directory-access-create-new-tenant.md)

- 若要了解有关如何在 Microsoft Azure 中控制资源访问的详细信息，请参阅 [了解 Azure 中的资源访问权限](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- 若要详细了解如何在 Azure AD 中分配角色，请参阅[如何使用 Azure Active Directory 为用户分配目录角色](active-directory-users-assign-role-azure-portal.md)
