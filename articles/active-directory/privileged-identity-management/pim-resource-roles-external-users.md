---
title: 邀请来宾并将 Azure 资源角色分配到 Privileged Identity Management Azure Active Directory |Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management （PIM）中邀请外部来宾用户和分配 Azure 资源角色。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08c2ef1695d118c248abfc500025479d1186eb05
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895651"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-privileged-identity-management"></a>邀请来宾用户并将 Azure 资源角色分配到 Privileged Identity Management

Azure Active Directory （Azure AD）来宾用户是 Azure AD 中企业到企业（B2B）协作功能的一部分，因此你可以在 Azure AD 中将外部来宾用户和供应商作为来宾进行管理。 将 B2B 协作与 Azure AD Privileged Identity Management （PIM）相结合时，可以将相容性和监管要求扩展到来宾。 例如，可以将以下 Privileged Identity Management 功能用于与来宾一起使用 Azure 标识任务：

- 分配对特定 Azure 资源的访问权限
- 启用实时 VM 访问
- 指定分配持续时间和结束日期
- 活动分配或激活时需要多重身份验证
- 执行访问评审
- 使用警报和审核日志

本文介绍如何使用 Privileged Identity Management 将来宾邀请到你的组织，并管理其对 Azure 资源的访问权限。

## <a name="when-would-you-invite-guests"></a>何时邀请来宾？

下面是你可能会向组织邀请来宾的几个示例：

- 允许仅拥有电子邮件帐户的外部个体私营供应商访问项目的 Azure 资源。
- 允许使用本地 Active Directory 联合身份验证服务的大型组织的外部合作伙伴访问费用应用程序。
- 允许组织外的支持工程师（如 Microsoft 支持部门）临时访问 Azure 资源，以解决问题。

## <a name="how-does-collaboration-using-b2b-guests-work"></a>使用 B2B 来宾的协作如何工作？

使用 B2B 协作时，可以邀请外部用户作为来宾加入你的组织。 来宾可以作为你的组织中的用户进行管理，但必须在其本组织中而不是在你的 Azure AD 组织中进行身份验证。 这意味着，如果来宾不再有权访问其本组织，他们也将失去对你的组织的访问权限。 例如，如果来宾离开了组织，则他们会自动失去对在 Azure AD 中与他们共享的任何资源的访问权限，而无需执行任何操作。 有关 B2B 协作的详细信息，请参阅[什么是来宾用户访问权限 AZURE ACTIVE DIRECTORY B2B？](../b2b/what-is-b2b.md)。

![显示如何在用户的主目录中对来宾用户进行身份验证的关系图](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>检查来宾协作设置

若要确保可以邀请来宾加入你的组织，应检查来宾协作设置。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 选择**Azure Active Directory** > **用户设置**。

1. 选择 "**管理外部协作设置**"。

    ![显示权限、邀请和协作限制设置的 "外部协作设置" 页](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. 确保**来宾邀请者角色中的管理员和用户可以邀请**switch 设置为 **"是"** 。

## <a name="invite-a-guest-and-assign-a-role"></a>邀请来宾并分配角色

使用 Privileged Identity Management，可以邀请来宾并使其适用于 Azure 资源角色。

1. 使用 "[特权角色管理员](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)" 或 "[用户管理员](../users-groups-roles/directory-assign-admin-roles.md#user-administrator)" 角色的成员用户身份登录到[Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择“Azure 资源”。

1. 使用资源筛选器对托管资源的列表进行筛选。

1. 选择要管理的资源，例如资源、资源组、订阅或管理组。

    应仅将作用域设置为来宾需要的内容。

1. 在 "管理" 下，选择 "**角色**" 以查看 Azure 资源的角色列表。

    ![Azure 资源角色列表显示处于活动状态且符合条件的用户数](./media/pim-resource-roles-external-users/resources-roles.png)

1. 选择用户将需要的最小角色。

    ![列出该角色的当前成员的所选角色页](./media/pim-resource-roles-external-users/selected-role.png)

1. 在 "角色" 页上，选择 "**添加成员**" 以打开 "新建分配" 窗格。

1. 单击“选择成员或组”。

    ![新建分配-选择列出用户和组的成员或组窗格以及邀请选项](./media/pim-resource-roles-external-users/select-member-group.png)

1. 若要邀请来宾，请单击 "**邀请**"。

    ![使用框邀请来宾页面输入电子邮件地址并指定个人消息](./media/pim-resource-roles-external-users/invite-guest.png)

1. 选择来宾后，请单击 "**邀请**"。

    来宾应作为选定成员添加。

1. 在 "**选择成员或组**" 窗格中，单击 "**选择**"。

1. 在 "**成员身份设置**" 窗格中，选择 "分配类型" 和 "持续时间"。

    ![新的分配-"成员资格设置" 页，其中包含用于指定分配类型、开始日期和结束日期的选项](./media/pim-resource-roles-external-users/membership-settings.png)

1. 若要完成分配，请选择 "**完成**"，然后单击 "**添加**"。

    "来宾" 角色分配将显示在角色列表中。

    ![列出来宾资格的角色页](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>激活作为来宾角色的角色

如果你是外部用户，则必须接受邀请作为 Azure AD 组织中的来宾，并可能激活你的角色分配。

1. 用邀请打开电子邮件。 该电子邮件如下所示。

    ![电子邮件邀请，其中包含目录名称、个人消息和入门链接](./media/pim-resource-roles-external-users/email-invite.png)

1. 选择电子邮件中的 "**入门**" 链接。

1. 查看权限后，单击“接受”。

    ![浏览器中的 "查看权限" 页，其中包含组织想要查看的权限列表](./media/pim-resource-roles-external-users/invite-accept.png)

1. 系统可能会要求你接受使用条款，并指定是否要保持登录。 在 Azure 门户中，如果你有*资格*使用某个角色，则你还没有访问资源的权限。

1. 若要激活角色分配，请通过 "激活角色" 链接打开电子邮件。 该电子邮件如下所示。

    ![表明你有资格使用 "激活角色" 链接的电子邮件](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. 选择 "**激活角色**" 可在 Privileged Identity Management 中打开符合条件的角色。

    !["我的角色" 页 Privileged Identity Management 列出你的合格角色](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. 在 "操作" 下，选择 "**激活**" 链接。

    需要指定一些信息来激活角色，具体取决于角色设置。

1. 指定角色设置后，单击“激活”，以激活角色。

    ![激活页面列出作用域和选项以指定开始时间、持续时间和原因](./media/pim-resource-roles-external-users/activate-role.png)

    如果并未要求管理员批准你的请求，则你应具有了对指定资源的访问权限。

## <a name="view-activity-for-a-guest"></a>查看来宾的活动

可以查看审核日志来跟踪来宾正在进行的操作。

1. 以管理员身份打开 Privileged Identity Management 并选择已共享的资源。

1. 选择 "**资源审核**" 以查看该资源的活动。 以下示例演示了资源组的活动。

    ![Azure 资源-列出时间、请求者和操作的资源审核页](./media/pim-resource-roles-external-users/audit-resource.png)

1. 若要查看来宾的活动，请选择 " **Azure Active Directory** > **用户**" > *来宾名称*。

1. 选择 "**审核日志**" 以查看组织的审核日志。 可以根据需要指定筛选器。

    ![目录审核日志列出日期、目标、启动者和活动](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中分配 Azure AD 管理员角色](pim-how-to-add-role-to-user.md)
- [什么是来宾用户访问 Azure AD B2B 协作？](../b2b/what-is-b2b.md)
