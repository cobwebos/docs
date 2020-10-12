---
title: 在 PIM 中向来宾分配 Azure 资源角色 - Azure AD | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中邀请外部来宾用户并分配 Azure 资源角色。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 763441d023e4a707c6a1edc09abfb6d8e5525723
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88782730"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中邀请来宾用户并分配 Azure 资源角色

Azure Active Directory (Azure AD) 来宾用户是 Azure AD 中企业对企业 (B2B) 协作功能的一部分，因此你可以在 Azure AD 中将外部来宾用户和供应商作为来宾进行管理。 如果将 B2B 协作和 Azure AD Privileged Identity Management (PIM) 组合在一起使用，则可以将合规性和治理要求扩展到来宾。 例如，你可以将这些 Privileged Identity Management 功能用于来宾的 Azure 标识任务：

- 分配对特定 Azure 资源的访问权限
- 启用实时访问
- 指定分配持续时间和结束日期
- 要求在主动分配或激活时进行多重身份验证
- 执行访问评审
- 使用警报和审核日志

本文介绍了如何使用 Privileged Identity Management 为你的组织邀请来宾，以及如何管理他们对 Azure 资源的访问。

## <a name="when-would-you-invite-guests"></a>你何时会邀请来宾？

下面是说明你何时会为组织邀请来宾的几个示例：

- 允许仅拥有电子邮件帐户的外部个体私营供应商访问项目的 Azure 资源。
- 允许使用本地 Active Directory 联合身份验证服务的大型组织的外部合作伙伴访问费用应用程序。
- 允许组织外的支持工程师（如 Microsoft 支持部门）临时访问 Azure 资源，以解决问题。

## <a name="how-does-collaboration-using-b2b-guests-work"></a>使用 B2B 来宾的协作如何进行？

使用 B2B 协作时，可以将外部用户作为来宾邀请到你的组织。 可以将来宾作为你的组织中的用户进行管理，但来宾必须在其本组织中而不是在你的 Azure AD 组织中进行身份验证。 这意味着，如果来宾不再有权访问其本组织，他们也将失去对你的组织的访问权限。 例如，如果来宾离开其组织，则你无需执行任何操作，他们会自动失去对你在 Azure AD 中与他们共享的任何资源的访问权限。 有关 B2B 协作的详细信息，请参阅[什么是 Azure Active Directory B2B 中的来宾用户访问权限？](../external-identities/what-is-b2b.md)。

![显示了如何在来宾用户的本目录中对其进行身份验证的关系图](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>检查来宾协作设置

为了确保你可以将来宾邀请到你的组织，你应当检查你的来宾协作设置。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 选择“Azure Active Directory” > “用户设置”。 

1. 选择“管理外部协作设置”。

    ![显示了权限、邀请和协作限制设置的外部协作设置页](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. 确保“管理员和具有来宾邀请者角色的用户可以邀请”开关设置为“是”。 

## <a name="invite-a-guest-and-assign-a-role"></a>邀请来宾并分配角色

使用 Privileged Identity Management，你可以邀请来宾并使他们有资格担任 Azure 资源角色。

1. 使用[特权角色管理员](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)或[用户管理员](../users-groups-roles/directory-assign-admin-roles.md#user-administrator)角色成员用户的身份登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择“Azure 资源” 。

1. 使用资源筛选器**** 对托管资源的列表进行筛选。

1. 选择要管理的资源，例如资源、资源组、订阅或管理组。

    应将范围设置为仅限来宾所需的范围。

1. 在“管理”下，选择“角色”以查看 Azure 资源的角色列表。

    ![Azure 资源角色列表，其中显示了处于活动状态且符合条件的用户数](./media/pim-resource-roles-external-users/resources-roles.png)

1. 选择用户所需的最小角色。

    ![所选角色页，列出了该角色的当前成员](./media/pim-resource-roles-external-users/selected-role.png)

1. 在角色页面上，选择“添加成员”以打开“新建分配”窗格。

1. 单击“选择成员或组”。****

    ![“新建分配 - 选择成员或组”窗格，其中列出了用户和组以及一个“邀请”选项](./media/pim-resource-roles-external-users/select-member-group.png)

1. 若要邀请来宾，请单击“邀请”。

    ![“邀请来宾”页面，其中包含用来输入电子邮件地址并指定个人消息的框](./media/pim-resource-roles-external-users/invite-guest.png)

1. 选择某个来宾后，单击“邀请”。

    应将该来宾添加为选定的成员。

1. 在“选择成员或组”窗格中，单击“选择”。 

1. 在“成员身份设置”窗格中，选择分配类型和持续时间。

    ![“新建分配 - 成员身份设置”页面，其中包含用于指定分配类型、开始日期和结束日期的选项](./media/pim-resource-roles-external-users/membership-settings.png)

1. 若要完成分配，请选择“完成”，然后选择“添加”。 

    来宾角色分配将显示在你的角色列表中。

    ![“角色”页面，其中将来宾列为合格用户](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>将角色激活为来宾

如果你是外部用户，则必须接受邀请才能成为 Azure AD 组织中的来宾并可能激活你的角色分配。

1. 打开包含你的邀请的电子邮件。 该电子邮件如下所示。

    ![电子邮件邀请，其中包含目录名称、个人消息和“开始”链接](./media/pim-resource-roles-external-users/email-invite.png)

1. 选择电子邮件中的“开始”链接。

1. 查看权限后，单击“接受”。****

    ![浏览器中的“查看权限”页面，其中包含组织想要你查看的权限列表](./media/pim-resource-roles-external-users/invite-accept.png)

1. 系统可能会要求你接受使用条款并指定是否要保持登录状态。 在 Azure 门户中，你符合某个角色的条件并不意味着你有权访问相应资源。

1. 若要激活你的角色分配，请打开包含“激活角色”链接的电子邮件。 该电子邮件如下所示。

    ![指明你符合某个角色的条件且包含“激活角色”链接的电子邮件](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. 选择“激活角色”以在 Privileged Identity Management 中打开你的合格角色。

    ![Privileged Identity Management 中的“我的角色”页，其中列出了你的合格角色](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. 在“操作”下，选择“激活”链接。

    需要指定一些信息来激活角色，具体取决于角色设置。

1. 指定角色设置后，单击“激活”，以激活角色。****

    ![“激活”页面，其中列出了作用域和用来指定开始时间、持续时间和原因的选项](./media/pim-resource-roles-external-users/activate-role.png)

    如果并未要求管理员批准你的请求，则你应具有了对指定资源的访问权限。

## <a name="view-activity-for-a-guest"></a>查看来宾的活动

你可以查看审核日志来跟踪来宾正在进行的操作。

1. 以管理员身份打开 Privileged Identity Management，并选择已共享的资源。

1. 选择“资源审核”，以查看该资源的活动。 以下示例演示了资源组的活动。

    ![“Azure 资源 - 资源审核”页面，其中列出了时间、请求者和操作](./media/pim-resource-roles-external-users/audit-resource.png)

1. 若要查看来宾的活动，请选择“Azure Active Directory” > “用户” > “来宾姓名”。 

1. 选择“审核日志”，以查看组织的审核日志。 可以根据需要指定筛选器。

    ![目录审核日志，其中列出了日期、目标、发起者和活动](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中分配 Azure AD 管理员角色](pim-how-to-add-role-to-user.md)
- [什么是 Azure AD B2B 协作中的来宾用户访问权限？](../external-identities/what-is-b2b.md)