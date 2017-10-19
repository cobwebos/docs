---
title: "如何添加或更改 Azure 管理员订阅角色 | Microsoft Docs"
description: "描述如何添加或更改 Azure 共同管理员、服务管理员和帐户管理员"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/19/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 4f77c7a615aaf5f87c0b260321f45a4e7129f339
ms.openlocfilehash: 38742fd84dafc3fb68829a55ab7be0827ad0b451
ms.contentlocale: zh-cn
ms.lasthandoff: 09/23/2017

---
# <a name="add-or-change-azure-administrator-roles-that-manage-the-subscription-or-services"></a>添加或更改管理订阅或服务的 Azure 管理员角色

用户可以更改管理 Azure 订阅或管理订阅中使用的 Azure 服务的 Azure 管理员。 若要查看 Azure 计费信息及管理订阅，必须以帐户管理员身份登录“帐户中心”。 

<a name="add-an-admin-for-a-subscription"></a>

## <a name="add-an-rbac-owner-admin-for-a-subscription-in-azure-portal"></a>在 Azure 门户中为订阅添加 RBAC 所有者管理员 

若要在 Azure 门户中将某人添加为订阅的管理员，建议向其授予 [RBAC](../active-directory/role-based-access-control-configure.md) 所有者角色。 所有者角色可以管理你分配的订阅中的资源，并且对其他订阅不具有访问权限。 通过 [Azure 门户](https://portal.azure.com)添加的所有者不能在 [Azure 经典门户](https://manage.windowsazure.com)中管理资源。

1. 登录到 [Azure 门户中的订阅视图](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。
1. 选择希望管理员访问的订阅。
1. 选择菜单中的“访问控制 (IAM)”。
1. 选择“添加” > “角色” > “所有者”。 键入要添加为所有者的用户的电子邮件地址，选择该用户，然后选择“保存”。

    ![显示所选所有者角色的屏幕截图](./media/billing-add-change-azure-subscription-administrator/add-role.png)

## <a name="add-or-change-co-administrator"></a>添加或更改协同管理员

仅所有者可添加为协同管理员。 无法将具有参与者、读者等角色的其他用户添加为协同管理员。

1. 如果尚未添加所有者，请按照上述说明将某人添加为所有者。
2. 右键单击刚添加的所有者用户，然后选择“添加为协同管理员”。 如果看不到“添加为协同管理员”选项，请刷新页面或尝试其他 Internet 浏览器。 

     ![添加协同管理员的屏幕截图](./media/billing-add-change-azure-subscription-administrator/add-coadmin.png)

    >[!TIP]
    >如果用户需要在 [Azure 经典门户](https://manage.windowsazure.com/)中管理 Azure 服务，则需要将“所有者”帐户作为协同管理员添加。

    若要删除协同管理员权限，请右键单击“协同管理员”用户，然后选择“删除协同管理员”。

    ![删除协同管理员的屏幕截图](./media/billing-add-change-azure-subscription-administrator/remove-coadmin.png)

<a name="change-service-administrator-for-a-subscription"></a>

## <a name="change-the-service-administrator-for-an-azure-subscription"></a>更改 Azure 订阅的服务管理员

只有帐户管理员可以更改订阅的服务管理员。 默认情况下，注册时，服务管理员即为帐户管理员。

1. 请查看[服务管理员变更限制](#limits)，确保自己的方案受支持。
1. 以帐户管理员身份登录到[帐户中心](https://account.windowsazure.com/subscriptions)。
1. 选择一个订阅。
1. 在右侧，选择“编辑订阅详细信息”。

    ![显示帐户中心中“编辑订阅”按钮的屏幕截图](./media/billing-add-change-azure-subscription-administrator/editsub.png)
1. 在“服务管理员”框中，输入新服务管理员的电子邮件地址。

    ![屏幕截图：用于更改服务管理员电子邮件的框](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

<a name="limits"></a>

### <a name="limitations-for-changing-service-administrators"></a>服务管理员变更限制

* 每个订阅都与一个 Azure AD 目录相关联。 要查找与订阅相关联的目录，请转到 [Azure 经典门户](https://manage.windowsazure.com/)，并选择“设置” > “订阅”。 选中订阅 ID 即可查找目录。
* 如果使用工作或学习帐户登录，则可将组织中的其他组织帐户添加为服务管理员。 例如，abby@contoso.com 可以添加 bob@contoso.com 作为服务管理员，但不能添加 john@notcontoso.com，除非 john@notcontoso.com 位于 contoso.com 目录中。 使用工作或学校帐户登录的用户可以继续将 Microsoft 帐户用户添加为服务管理员。

  | 登录方法 | 是否要将 Microsoft 帐户用户添加为 SA？ | 是否要将同一组织中的工作或学校帐户添加为 SA？ | 将不同组织的工作或学校帐户添加为 SA？ |
  | --- | --- | --- | --- |
  |  Microsoft 帐户 |是 |否 |否 |
  |  工作或学校帐户 |是 |是 |否 |

## <a name="change-the-account-administrator-for-an-azure-subscription"></a>更改 Azure 订阅的帐户管理员

若要更改订阅的账户管理员，请参阅[将 Azure 订阅所有权转让给其他帐户](billing-subscription-transfer.md)。

<a name="check-the-account-administrator-of-the-subscription"></a>

**不确定谁是帐户管理员？** 执行以下步骤:

1. 登录到 [Azure 门户中的订阅视图](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。
1. 选择要检查的订阅，并关注“设置”下的信息。
1. 选择“属性”。 订阅的帐户管理员会显示在“帐户管理员”框中。  

## <a name="types-of-azure-admin-accounts"></a>Azure 管理员帐户的类型

 帐户管理员、服务管理员和共同管理员是 Microsoft Azure 中的三种管理员角色。 下表描述这三个管理角色之间的差异。

| 管理角色 | 限制 | 说明 |
| --- | --- | --- |
| 帐户管理员 (AA) |每个 Azure 帐户 1 个帐户管理员 |这是注册或购买了 Azure 订阅的人员，有权访问[帐户中心](https://account.azure.com/Subscriptions)并执行各种管理任务。 其中包括能够创建订阅、取消订阅、更改订阅的帐单，以及更改服务管理员。 |
| 服务管理员 (SA) |每个 Azure 订阅 1 个服务管理员 |此角色有权管理 [Azure 门户](https://portal.azure.com)中的服务。 默认情况下，新订阅的帐户管理员也是服务管理员。 |
| [Azure 经典门户](https://manage.windowsazure.com)中的共同管理员 (CA) |每个订阅 200 个共同管理员 |此角色具有与服务管理员一样的访问特权，但不能更改订阅与 Azure 目录之间的关联关系。 |

可以使用 Azure Active Directory 基于角色的访问控制 (RBAC) 将用户添加到多个角色。 有关更多信息，请参阅 [Azure Active Directory 基于角色的访问控制](../active-directory/role-based-access-control-configure.md)。


## <a name="learn-more-about-resource-access-control-and-active-directory"></a>了解有关资源访问控制和 Active Directory 的详细信息

* 若要深入了解如何在 Microsoft Azure 中控制资源访问，请参阅[了解 Azure 中的资源访问](../active-directory/active-directory-understanding-resource-access.md)。
* 有关 Azure Active Directory 的详细信息，请参阅 [Azure 订阅与 Azure Active Directory 的关联方式](../active-directory/active-directory-how-subscriptions-associated-directory.md)以及[在 Azure Active Directory 中分配管理员角色](../active-directory/active-directory-assign-admin-roles.md)。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。

如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

