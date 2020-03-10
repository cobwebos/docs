---
title: 将现有的 Azure 订阅添加到租户-Azure AD
description: 有关将现有 Azure 订阅添加到 Azure Active Directory 租户的说明。
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8f44e9a1e43da2b9ce6c817898c1722fba715c4
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78391068"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>将 Azure 订阅关联或添加到 Azure Active Directory 租户

Azure 订阅与 Azure Active Directory （Azure AD）有信任关系。 订阅信任 Azure AD 来对用户、服务和设备进行身份验证。

多个订阅可以信任同一个 Azure AD 目录。 每个订阅只能信任一个目录。

如果订阅过期，则将失去与该订阅关联的所有其他资源的访问权限。 但 Azure AD 目录将保留在 Azure 中。 你可以使用不同的 Azure 订阅来关联和管理该目录。

你的所有用户都有一个用于身份验证的*主*目录。 你的用户也可以是其他目录中的来宾。 可在 Azure AD 中查看每位用户的主目录和来宾目录。

> [!Important]
> 将订阅关联到不同的目录时，具有使用[基于角色的访问控制（RBAC）](../../role-based-access-control/role-assignments-portal.md)分配的角色的用户将失去其访问权限。 经典订阅管理员（包括服务管理员和共同管理员）也会失去访问权限。
>
> 将订阅与其他目录相关联时，还会从订阅中删除策略分配。
>
> 将 Azure Kubernetes Service （AKS）群集移到不同的订阅，或将群集拥有的订阅移动到新租户，导致群集由于角色分配和服务主体的权限丢失而丢失功能。 有关 AKS 的详细信息，请参阅[Azure Kubernetes 服务（AKS）](https://docs.microsoft.com/azure/aks/)。


## <a name="before-you-begin"></a>开始之前

在可以关联或添加订阅之前，请执行以下任务：

- 查看以下更改列表以及可能受到的影响：

  - 使用 RBAC 分配了角色的用户将失去其访问权限
  - 服务管理员和共同管理员将失去访问权限
  - 如果你有任何密钥保管库，则它们将无法访问，你必须在关联后进行修复
  - 如果你拥有虚拟机或逻辑应用等资源的任何托管标识，则必须在关联后重新启用或重新创建它们。
  - 如果有已注册的 Azure Stack，则必须在关联后重新注册它

- 使用符合以下条件的帐户登录：

  - 具有订阅的[所有者](../../role-based-access-control/built-in-roles.md#owner)角色分配。 有关如何分配所有者角色的信息，请参阅[使用 RBAC 和 Azure 门户管理对 Azure 资源的访问权限](../../role-based-access-control/role-assignments-portal.md)。
  - 存在于当前目录和新目录中。 当前目录与订阅相关联。 将新目录与订阅相关联。 有关获取对其他目录的访问权限的详细信息，请参阅[在 Azure 门户中添加 AZURE ACTIVE DIRECTORY B2B 协作用户](../b2b/add-users-administrator.md)。

- 请确保未使用 Azure 云服务提供商 (CSP) 订阅（MS-AZR-0145P、MS-AZR-0146P、MS-AZR-159P）、Microsoft 内部订阅 (MS-AZR-0015P) 或 Microsoft Imagine 订阅 (MS-AZR-0144P)。

## 将订阅关联到目录<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

若要将现有订阅关联到 Azure AD 目录，请执行以下步骤：

1. 登录，然后从 [Azure 门户中的“订阅”页面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)选择要使用的订阅。

1. 选择“更改目录”。

    ![订阅页面，其中突出显示了“更改目录”选项](media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png)

1. 查看出现的任何警告，然后选择“更改”。

    ![“更改目录”页，显示要更改到的目录](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    订阅目录将发生更改并会显示一条成功消息。

    ![有关目录更改的成功消息](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)

使用**交换机目录**转到新目录。 所有内容可能需要几个小时才能正常显示。 如果看起来太长，请检查**全局订阅筛选器**。 请确保移动的订阅不隐藏。 你可能需要注销 Azure 门户并重新登录才能查看新目录。

!["目录切换器" 页，包含示例信息](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

更改订阅目录是服务级操作，不会影响订阅的账单所有权。 帐户管理员仍可从[帐户中心](https://account.azure.com/subscriptions)更改服务管理员。 若要删除原始目录，必须将订阅计费所有权转移到新的帐户管理员。若要详细了解如何传输计费所有权，请参阅[将 Azure 订阅所有权转让给其他帐户](../../cost-management-billing/manage/billing-subscription-transfer.md)。

## <a name="post-association-steps"></a>后关联步骤

将订阅关联到其他目录后，你可能需要执行以下任务来恢复操作：

- 如果有任何密钥保管库，则必须更改密钥保管库租户 ID。 有关详细信息，请参阅在[订阅移动后更改密钥保管库租户 ID](../../key-vault/key-vault-subscription-move-fix.md)。

- 如果为资源使用系统分配的托管标识，则必须重新启用这些标识。 如果使用了用户分配的托管标识，则必须重新创建这些标识。 重新启用或重新创建托管标识之后，必须重新建立分配给这些标识的权限。 有关详细信息，请参阅[什么是 Azure 资源的托管标识？](../managed-identities-azure-resources/overview.md)。

- 如果已使用此订阅注册了 Azure Stack，则必须重新注册。 有关详细信息，请参阅[在 Azure 中注册 Azure Stack](/azure-stack/operator/azure-stack-registration)。

## <a name="next-steps"></a>后续步骤

- 若要创建新的 Azure AD 租户，请参阅[快速入门：在 Azure Active Directory 中创建新租户](active-directory-access-create-new-tenant.md)。

- 若要详细了解 Microsoft Azure 如何控制资源访问权限，请参阅[经典订阅管理员角色、AZURE RBAC 角色和 Azure AD 管理员角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)。

- 若要了解有关如何在 Azure AD 中分配角色的详细信息，请参阅为[具有 Azure Active Directory 的用户分配管理员和非管理员角色](active-directory-users-assign-role-azure-portal.md)。
