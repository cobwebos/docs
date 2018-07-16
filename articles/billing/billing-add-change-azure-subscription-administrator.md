---
title: 如何添加或更改 Azure 管理员订阅角色 | Microsoft Docs
description: 描述如何添加或更改 Azure 共同管理员、服务管理员和帐户管理员
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/14/2018
ms.author: genli
ms.openlocfilehash: 97210c0d9dba9c4130b1da9ad17a257ff1d81b42
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448761"
---
# <a name="add-or-change-azure-subscription-administrators"></a>添加或更改 Azure 订阅管理员

若要管理对 Azure 资源的访问权限，必须具有相应的管理员角色。 本文介绍如何在订阅级别为用户添加或更改管理员角色。

## <a name="what-administrator-role-do-i-use"></a>使用什么管理员角色？

Azure 有几个不同的角色。 若要管理对资源的访问权限，可以使用经典订阅管理员角色（如服务管理员和共同管理员）或更新的授权系统（称为“基于角色的访问控制 (RBAC)”）。 为确保实现更好的控制并简化访问管理，建议使用 RBAC 满足所有访问管理需求。 如果可能，建议使用 RBAC 重新配置现有访问策略。 有关详细信息，请参阅[什么是基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md) 和[了解 Azure 中的不同角色](../role-based-access-control/rbac-and-directory-admin-roles.md)。

<a name="add-an-admin-for-a-subscription"></a>

## <a name="add-an-rbac-owner-for-a-subscription-in-azure-portal"></a>在 Azure 门户中为订阅添加 RBAC 所有者 

若要将某人添加为 Azure 订阅的管理员，请在订阅范围内为其分配[所有者](../role-based-access-control/built-in-roles.md#owner)角色（一个 RBAC 角色）。 所有者角色可以管理你分配的订阅中的资源，并且对其他订阅不具有访问权限。

1. 请访问 [Azure 门户中的“订阅”](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。
2. 选择要提供访问权限的订阅。
3. 选择 **添加** 。
   （如果缺少“添加”按钮，则表示无权添加权限。）
4. 选择列表中的“访问控制(IAM)”。
5. 在“角色”框中，选择“所有者”。 
6. 在“分配其访问权限”框中，选择“Azure AD 用户、组或应用程序”。 
7. 在“选择”框中，键入要添加为“所有者”的用户的电子邮件地址。 选择用户，再选择“保存”。

    ![显示所选所有者角色的屏幕截图](./media/billing-add-change-azure-subscription-administrator/add-role.png)

这会为该用户提供所有资源的完全访问权限，包括将访问权限委派给其他用户的权限。 若要在不同范围（如资源组）内授予访问权限，请访问该范围内的“访问控制(IAM)”边栏选项卡。

## <a name="add-or-change-co-administrator"></a>添加或更改协同管理员

仅[所有者](../role-based-access-control/built-in-roles.md#owner)可添加为共同管理员。 无法将具有[参与者](../role-based-access-control/built-in-roles.md#contributor)、[读者](../role-based-access-control/built-in-roles.md#reader)等角色的其他用户添加为共同管理员。

> [!TIP]
> 如果用户需要管理 Azure 经典部署，则仅需将“所有者”添加为共同管理员。 建议使用 RBAC 实现所有其他目的。

1. 如果尚未添加所有者，请按照上述说明将某人添加为所有者。
2. 右键单击刚添加的所有者用户，然后选择“添加为协同管理员”。 如果看不到“添加为协同管理员”选项，请刷新页面或尝试其他 Internet 浏览器。 

    ![添加协同管理员的屏幕截图](./media/billing-add-change-azure-subscription-administrator/add-coadmin.png)

    若要删除共同管理员权限，请右键单击“共同管理员”用户，然后选择“删除共同管理员”。

    ![删除协同管理员的屏幕截图](./media/billing-add-change-azure-subscription-administrator/remove-coadmin.png)

<a name="change-service-administrator-for-a-subscription"></a>

## <a name="change-the-service-administrator-for-an-azure-subscription"></a>更改 Azure 订阅的服务管理员

只有帐户管理员可以更改订阅的服务管理员。 默认情况下，注册时，服务管理员即为帐户管理员。 如果服务管理员更改为另一个用户，帐户管理员会失去对 Azure 门户的访问权限。 但是，帐户管理员始终可使用帐户中心将服务管理员重新更改为自己。

1. 请查看[服务管理员变更限制](#limits)，确保自己的方案受支持。
1. 以帐户管理员身份登录到[帐户中心](https://account.windowsazure.com/subscriptions)。
1. 选择一个订阅。
1. 在右侧，选择“编辑订阅详细信息”。

    ![显示帐户中心中“编辑订阅”按钮的屏幕截图](./media/billing-add-change-azure-subscription-administrator/editsub.png)
1. 在“服务管理员”框中，输入新服务管理员的电子邮件地址。

    ![屏幕截图：用于更改服务管理员电子邮件的框](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

<a name="limits"></a>

### <a name="limitations-for-changing-service-administrators"></a>服务管理员变更限制

* 每个订阅都与一个 Azure AD 目录相关联。 要查找与订阅相关联的目录，请转到“[订阅](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)”，然后选择一个订阅以查看目录。
* 如果已使用工作或学校帐户登录，则可将组织中的其他帐户添加为服务管理员。 例如，abby@contoso.com 可以添加 bob@contoso.com 作为服务管理员，但不能添加 john@notcontoso.com，除非 john@notcontoso.com 位于 contoso.com 目录中。 使用工作或学校帐户登录的用户可以继续将 Microsoft 帐户用户添加为服务管理员。

  | 登录方法 | 将 Microsoft 帐户用户添加为服务管理员？ | 将同一组织中的工作或学校帐户添加为服务管理员？ | 将不同组织中的工作或学校帐户添加为服务管理员？ |
  | --- | --- | --- | --- |
  |  Microsoft 帐户 |是 |否 |否 |
  |  工作或学校帐户 |是 |是 |否 |

## <a name="change-the-account-administrator-for-an-azure-subscription"></a>更改 Azure 订阅的帐户管理员

帐户管理员是最初注册 Azure 订阅的用户，负责充当订阅的账单所有者。 若要更改订阅的帐户管理员，请参阅[将 Azure 订阅所有权转让给其他帐户](billing-subscription-transfer.md)。

<a name="check-the-account-administrator-of-the-subscription"></a>

**不确定谁是帐户管理员？** 执行以下步骤:

1. 请访问 [Azure 门户中的“订阅”](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。
1. 选择要检查的订阅，并关注“设置”下的信息。
1. 选择“属性”。 订阅的帐户管理员会显示在“帐户管理员”框中。  

## <a name="learn-more-about-resource-access-control-and-active-directory"></a>了解有关资源访问控制和 Active Directory 的详细信息

* 若要详细了解 RBAC，请参阅[什么是基于角色的访问控制 (RBAC)？](../role-based-access-control/overview.md)
* 若要详细了解 Azure 中的所有角色，请参阅[了解 Azure 中的不同角色](../role-based-access-control/rbac-and-directory-admin-roles.md)。
* 有关 Azure Active Directory 的详细信息，请参阅 [Azure 订阅与 Azure Active Directory 的关联方式](../active-directory/active-directory-how-subscriptions-associated-directory.md)以及[在 Azure Active Directory 中分配管理员角色](../active-directory/users-groups-roles/directory-assign-admin-roles.md)。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。

如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。
