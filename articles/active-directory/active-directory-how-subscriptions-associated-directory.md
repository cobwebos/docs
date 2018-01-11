---
title: "如何将现有的 Azure 订阅添加到 Azure AD 目录 | Microsoft Docs"
description: "如何将现有的订阅添加到 Azure AD 目录"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: bc4773c2-bc4a-4d21-9264-2267065f0aea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/12/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 4f5275584d75c88ce3ef5b607f315f3b425d2c3f
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2017
---
# <a name="how-to-associate-or-add-an-azure-subscription-to-azure-active-directory"></a>如何将 Azure 订阅关联或添加到 Azure Active Directory

本文介绍 Azure 订阅与 Azure Active Directory (Azure AD) 之间的关系，以及如何向 Azure AD 目录添加现有的订阅。 Azure 订阅与 Azure AD 建立了信任关系，也即会委托该目录对用户、服务和设备进行身份验证。 多个订阅可以委托同一个目录，但每个订阅只能委托一个目录。 

订阅与目录之间的这种委托关系不同于订阅与 Azure 中其他资源（网站、数据库等）之间的委托关系。 如果某个订阅过期，则与该订阅关联的其他资源的访问权限也会终止。 但是，Azure AD 目录会保留在 Azure 中，你可以将其他订阅与该目录相关联，然后使用新订阅管理目录。

所有用户都只有一个用于验证其身份的主目录，但他们也可以是其他目录中的来宾。 在 Azure AD 中，可以看到用户帐户是其成员或来宾的目录。

## <a name="before-you-begin"></a>开始之前

* 必须使用具有订阅的 RBAC 所有者访问权限的帐户登录。
* 使用帐户登录时，该帐户必须存在于订阅所关联的当前目录中，同时存在于要向其添加订阅的目录中。 若要详细了解如何访问另一目录，请参阅 [Azure Active Directory 管理员如何添加 B2B 协作用户？](active-directory-b2b-admin-add-users.md)

## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>将现有订阅关联到 Azure AD 目录

1. 登录，然后从 [Azure 门户中的“订阅”页面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)选择订阅。
2. 单击“更改目录”。

    ![显示“更改目录”按钮的屏幕截图](./media/active-directory-how-subscriptions-associated-directory/edit-directory-button.PNG)
3. 查看警告。 订阅目录更改后，所有已分配访问权限的[基于角色的访问控制 (RBAC)](role-based-access-control-configure.md) 用户和所有订阅管理员都会失去访问权限。
4. 选择目录。

    ![显示更改目录 UI 的屏幕截图](./media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.PNG)
5. 单击“更改”。
6. 成功！ 使用目录切换器转到新目录。

    ![显示“更改目录成功”通知的屏幕截图](./media/active-directory-how-subscriptions-associated-directory/edit-directory-success.PNG)

    ![显示切换器的屏幕截图](./media/active-directory-how-subscriptions-associated-directory/directory-switcher.PNG)


更改订阅目录是服务级别的操作， 不会影响订阅的账单所有权。帐户管理员仍可使用[帐户中心](https://account.azure.com/subscriptions)更改服务管理员。 如需删除原始目录，必须将订阅的账单所有权转让给新的帐户管理员。若要详细了解如何转让账单所有权，请参阅[将 Azure 订阅所有权转让给其他帐户](../billing/billing-subscription-transfer.md)。 

## <a name="next-steps"></a>后续步骤

* 若要详细了解如何创建免费的全新 Azure AD 目录，请参阅[如何获取 Azure Active Directory 租户](develop/active-directory-howto-tenant.md)
* 若要详细了解如何转让 Azure 订阅的账单所有权，请参阅[将 Azure 订阅所有权转让给其他帐户](../billing/billing-subscription-transfer.md)
* 若要了解有关如何在 Microsoft Azure 中控制资源访问的详细信息，请参阅 [Understanding resource access in Azure](active-directory-understanding-resource-access.md)（了解 Azure 中的资源访问）
* 有关如何在 Azure AD 中分配角色的详细信息，请参阅 [在 Azure Active Directory 中分配管理员角色](active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG
