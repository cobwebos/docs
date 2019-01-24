---
title: 将现有 Azure 订阅添加到租户 - Azure Active Directory | Microsoft Docs
description: 有关将现有 Azure 订阅添加到 Azure Active Directory 租户的说明。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.openlocfilehash: 20c4be3741fa1e728cf59a148beb5adbc87b89c0
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452015"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>将 Azure 订阅关联或添加到 Azure Active Directory 租户
Azure 订阅与 Azure Active Directory (Azure AD) 建立了信任关系，即该订阅信任 Azure AD 对用户、服务和设备进行身份验证。 多个订阅可以信任同一个 Azure AD 目录，但每个订阅只能信任一个目录。

如果订阅过期，则将失去与该订阅关联的所有其他资源的访问权限。 但是，Azure AD Directory 仍保留在 Azure 中，可使用不同的 Azure 订阅关联和管理目录。

所有用户都有一个用于身份验证的“主”目录。 但是，用户还可在其他目录中作为来宾。 可在 Azure AD 中查看每位用户的主目录和来宾目录。

>[!Important]
>订阅目录更改后，所有已分配访问权限的[基于角色的访问控制 (RBAC)](../../role-based-access-control/role-assignments-portal.md) 用户和所有订阅管理员都会失去访问权限。 此外，如果有任何密钥保管库，它们也将受到订阅移动的影响。 要解决该问题，必须[更改密钥保管库租户 ID](../../key-vault/key-vault-subscription-move-fix.md)，然后才能继续操作。


## <a name="before-you-begin"></a>开始之前
要想关联或添加订阅，必须先执行以下任务：

- 使用符合以下条件的帐户登录：
    - 对订阅具有“RBAC 所有者”访问权限。

    - 同时存在于当前目录（与订阅关联）和新目录（希望以后与订阅关联）中。 有关获取其他目录访问权限的详细信息，请参阅 [Azure Active Directory 管理员如何添加 B2B 协作用户？](../b2b/add-users-administrator.md)。

- 请确保未使用 Azure 云服务提供商 (CSP) 订阅（MS-AZR-0145P、MS-AZR-0146P、MS-AZR-159P）、Microsoft 内部订阅 (MS-AZR-0015P) 或 Microsoft Imagine 订阅 (MS-AZR-0144P)。
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>将现有订阅关联到 Azure AD 目录
1. 登录，然后从 [Azure 门户中的“订阅”页面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)选择要使用的订阅。

2. 选择“更改目录”。

    ![订阅页面，其中突出显示了“更改目录”选项](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. 查看出现的任何警告，然后选择“更改”。

    ![“更改目录”页，显示要更改到的目录](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    订阅目录将发生更改并会显示一条成功消息。

    ![成功消息](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)    

4. 使用目录切换器转到新目录。 正确显示所有内容可能需要长达 10 分钟的时间。

    ![目录切换器页](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

更改订阅目录是服务级操作，不会影响订阅的账单所有权。 帐户管理员仍可从[帐户中心](https://account.azure.com/subscriptions)更改服务管理员。 若要删除原始目录，必须将订阅的账单所有权转让给新的帐户管理员。若要详细了解如何转让账单所有权，请参阅[将 Azure 订阅所有权转让给其他帐户](../../billing/billing-subscription-transfer.md)。 

## <a name="next-steps"></a>后续步骤

- 要创建新的 Azure AD 租户，请参阅[访问 Azure Active Directory 以创建新租户](active-directory-access-create-new-tenant.md)

- 若要了解有关如何在 Microsoft Azure 中控制资源访问的详细信息，请参阅 [了解 Azure 中的资源访问权限](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- 若要详细了解如何在 Azure AD 中分配角色，请参阅[如何使用 Azure Active Directory 为用户分配目录角色](active-directory-users-assign-role-azure-portal.md)
