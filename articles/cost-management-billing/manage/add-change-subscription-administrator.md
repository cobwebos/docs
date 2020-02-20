---
title: 添加或更改 Azure 订阅管理员
description: 介绍了如何使用基于角色的访问控制 (RBAC) 来添加或更改 Azure 订阅管理员。
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 3e4ad74a55504367de18a3dc9f0a7d33c462b329
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199834"
---
# <a name="add-or-change-azure-subscription-administrators"></a>添加或更改 Azure 订阅管理员


若要管理对 Azure 资源的访问权限，必须具有相应的管理员角色。 Azure 有一个称为[基于角色的访问控制](../../role-based-access-control/overview.md) (RBAC) 的授权系统，其中包含了你可以从中选择的多个内置角色。 可以在不同的作用域（例如，管理组、订阅或资源组）分配这些角色。 默认情况下，创建新的 Azure 订阅的人员可以向其他用户分配对订阅的管理访问权限。

本文介绍了如何在订阅作用域使用 RBAC 为用户添加或更改管理员角色。

Microsoft 建议你使用 RBAC 来管理对资源的访问权限。 但是，如果仍使用经典部署模型，并使用 [Azure 服务管理 PowerShell 模块](https://docs.microsoft.com/powershell/module/servicemanagement/azure)管理经典资源，则需要使用经典管理员。

> [!TIP]
> 如果只使用 Azure 门户管理经典资源，则无需使用经典管理员。

有关详细信息，请参阅 [Azure 资源管理器与经典部署和](../../azure-resource-manager/management/deployment-models.md)和 [Azure 经典订阅管理员](../../role-based-access-control/classic-administrators.md)。

<a name="add-an-admin-for-a-subscription"></a>

## <a name="assign-a-subscription-administrator"></a>分配订阅管理员

要使用户成为 Azure 订阅的管理员，现有管理员应在订阅范围内为其分配[所有者](../../role-based-access-control/built-in-roles.md#owner)角色（一个 RBAC 角色）。 “所有者”角色会为该用户提供订阅中所有资源的完全访问权限，包括将访问权限委派给其他用户的权限。 这些步骤与任何其他角色分配相同。

如果不确定谁是订阅的帐户管理员，可使用以下步骤查明。

1. 打开 [Azure 门户中的“订阅”页](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。
1. 选择要检查的订阅，并关注“设置”  下的信息。
1. 选择“属性”  。 订阅的帐户管理员会显示在“帐户管理员”框中。 

### <a name="to-assign-a-user-as-an-administrator"></a>以管理员身份分配用户

1. 以订阅所有者身份登录到 Azure 门户，然后打开[订阅](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。

1. 单击要授予访问权限的订阅。

1. 单击“访问控制(IAM)”  。

1. 单击“角色分配”选项卡以查看此订阅的所有角色分配  。

    ![其中显示了角色分配的屏幕截图](./media/add-change-subscription-administrator/role-assignments.png)

1. 单击“添加” > “添加角色分配”以打开“添加角色分配”窗格。   

    如果你无权分配角色，则此选项会被禁用。

1. 在“角色”  下拉列表中，选择“所有者”  角色。

1. 在“选择”  列表中，选择用户。 如果没有在列表中看到用户，则可在“选择”框中键入相应内容，以便在目录中搜索显示名称和电子邮件地址。 

    ![显示所选所有者角色的屏幕截图](./media/add-change-subscription-administrator/add-role.png)

1. 单击“保存”以分配该角色  。

    片刻之后，会在订阅范围内为该用户分配“所有者”角色。

## <a name="next-steps"></a>后续步骤

* [什么是基于角色的访问控制 (RBAC)？](../../role-based-access-control/overview.md)
* [了解 Azure 中的不同角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* [如何：将 Azure 订阅关联或添加到 Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [Azure Active Directory 中的管理员角色权限](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。
