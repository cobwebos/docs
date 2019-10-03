---
title: 邀请来宾并在 PIM 中分配 Azure 资源角色-Azure Active Directory |Microsoft Docs
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
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3e01b58a2a2fc6f93ae5ccc15e200a0cea69a0c
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804217"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-pim"></a>邀请来宾用户并在 PIM 中分配 Azure 资源角色

Azure Active Directory （Azure AD）企业对企业（B2B）是 Azure AD 中的一组功能，使组织能够使用任何帐户与外部来宾用户（来宾）和供应商进行协作。 将 B2B 与 Azure AD Privileged Identity Management （PIM）结合使用时，可以继续将相容性和监管要求应用于来宾。 例如，你可以将这些 PIM 功能用于与来宾一起使用的 Azure 标识任务：

- 分配对特定 Azure 资源的访问权限
- 启用实时访问
- 指定分配持续时间和结束日期
- 要求对活动分配或激活进行 MFA
- 执行访问评审
- 使用警报和审核日志

本文介绍如何使用 Privileged Identity Management 将来宾邀请到你的组织，并管理其对 Azure 资源的访问权限。

## <a name="when-would-you-invite-guests"></a>何时邀请来宾？

下面是一些可能会向组织邀请来宾的示例方案：

- 允许仅拥有电子邮件帐户的外部个体私营供应商访问项目的 Azure 资源。
- 允许使用本地 Active Directory 联合身份验证服务的大型组织的外部合作伙伴访问费用应用程序。
- 允许组织外的支持工程师（如 Microsoft 支持部门）临时访问 Azure 资源，以解决问题。

## <a name="how-does-collaboration-using-b2b-guests-work"></a>使用 B2B 来宾的协作如何工作？

使用 B2B 协作时，可以邀请外部用户作为来宾加入你的组织。 来宾似乎在你的组织中，但来宾没有任何关联的凭据。 每当来宾必须进行身份验证时，必须在其本组织中进行身份验证，而不是在组织中进行身份验证。 这意味着，如果来宾不再有权访问其本组织，他们也将失去对你的组织的访问权限。 例如，如果来宾离开了组织，则他们会自动失去对在 Azure AD 中与他们共享的任何资源的访问权限，而无需执行任何操作。 有关 B2B 的详细信息，请参阅[什么是 Azure Active Directory B2B 中的来宾用户访问权限？](../b2b/what-is-b2b.md)。

![显示来宾用户在目录中的显示方式但在其主目录中进行身份验证的关系图](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>检查来宾协作设置

若要确保可以邀请来宾加入你的组织，应检查来宾协作设置。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 单击“Azure Active Directory” > “用户设置”。

1. 单击“管理外部协作设置”。

    ![显示权限、邀请和协作限制设置的 "外部协作设置" 页](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. 确保“管理员和具有来宾邀请者角色的用户可以邀请”开关设置为“是”。

## <a name="invite-a-guest-and-assign-a-role"></a>邀请来宾并分配角色

使用 PIM，可以邀请来宾并使其符合 Azure 资源角色的条件，就像成员用户一样。

1. 使用 "[特权角色管理员](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)" 或 "[用户管理员](../users-groups-roles/directory-assign-admin-roles.md#user-administrator)" 角色的成员用户身份登录到[Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。

1. 单击“Azure 资源”。

1. 使用资源筛选器对托管资源的列表进行筛选。

1. 单击要管理的资源，如资源、资源组、订阅或管理组。

    应仅将作用域设置为来宾需要的内容。

1. 在“管理”下，单击“角色”以查看 Azure 资源的角色列表。

    ![Azure 资源角色列表显示处于活动状态且符合条件的用户数](./media/pim-resource-roles-external-users/resources-roles.png)

1. 单击用户所需的最小角色。

    ![列出该角色的当前成员的所选角色页](./media/pim-resource-roles-external-users/selected-role.png)

1. 在角色页面，单击“添加成员”以打开“新建分配”窗格。

1. 单击“选择成员或组”。

    ![新建分配-选择列出用户和组的成员或组窗格以及邀请选项](./media/pim-resource-roles-external-users/select-member-group.png)

1. 若要邀请来宾，请单击 "**邀请**"。

    ![使用框邀请来宾页面输入电子邮件地址并指定个人消息](./media/pim-resource-roles-external-users/invite-guest.png)

1. 选择来宾后，请单击 "**邀请**"。

    来宾应作为选定成员添加。

1. 在 "**选择成员或组**" 窗格中，单击 "**选择**"。

1. 在 "**成员身份设置**" 窗格中，选择 "分配类型" 和 "持续时间"。

    ![新的分配-"成员资格设置" 页，其中包含用于指定分配类型、开始日期和结束日期的选项](./media/pim-resource-roles-external-users/membership-settings.png)

1. 单击“完成”，然后单击“添加”，以完成分配。

    "来宾" 角色分配将显示在角色列表中。

    ![列出来宾资格的角色页](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>激活作为来宾角色的角色

作为外部用户，您首先需要接受邀请到您的 Azure AD 组织，并可能激活您的角色。

1. 用邀请打开电子邮件。 该电子邮件如下所示。

    ![电子邮件邀请，其中包含目录名称、个人消息和入门链接](./media/pim-resource-roles-external-users/email-invite.png)

1. 单击电子邮件中的“开始”链接。

1. 查看权限后，单击“接受”。

    ![浏览器中的 "查看权限" 页，其中包含组织想要查看的权限列表](./media/pim-resource-roles-external-users/invite-accept.png)

1. 系统可能会要求你接受使用条款，并指定是否要保持登录。

    此时会打开 Azure 门户。 若你刚刚才符合某个角色的条件，则没有对资源的访问权限。

1. 请打开包含激活角色链接的电子邮件，以激活角色。 该电子邮件如下所示。

    ![PIM 中的电子邮件，指示你有资格使用 "激活角色" 链接](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. 单击“激活角色”，以在 PIM 中打开你的符合条件的角色。

    ![PIM 中的 "我的角色" 页列出你的合格角色](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. 在“操作”下单击“激活”链接。

    需要指定一些信息来激活角色，具体取决于角色设置。

1. 指定角色设置后，单击“激活”，以激活角色。

    ![激活页面列出作用域和选项以指定开始时间、持续时间和原因](./media/pim-resource-roles-external-users/activate-role.png)

    如果并未要求管理员批准你的请求，则你应具有了对指定资源的访问权限。

## <a name="view-activity-for-a-guest"></a>查看来宾的活动

与成员用户类似，你可以查看审核日志来跟踪来宾正在进行的操作。

1. 以管理员身份打开 PIM，并选择已共享的资源。

1. 单击“资源审核”，以查看该资源的活动。 以下示例演示了资源组的活动。

    ![Azure 资源-列出时间、请求者和操作的资源审核页](./media/pim-resource-roles-external-users/audit-resource.png)

1. 若要查看来宾的活动，请单击 " **Azure Active Directory** > **用户**> guest name"。

1. 单击 "**审核日志**" 以查看组织的审核日志。 可以根据需要指定筛选器。

    ![目录审核日志列出日期、目标、启动者和活动](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>后续步骤

- [在 PIM 中分配 Azure AD 管理员角色](pim-how-to-add-role-to-user.md)
- [什么是 Azure Active Directory B2B 中的来宾用户访问权限？](../b2b/what-is-b2b.md)
