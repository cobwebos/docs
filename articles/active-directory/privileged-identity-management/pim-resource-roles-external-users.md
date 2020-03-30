---
title: 在 PIM - Azure AD 中为来宾分配 Azure 资源角色 |微软文档
description: 了解如何邀请外部来宾用户并在 Azure AD 特权标识管理 （PIM） 中分配 Azure 资源角色。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2efcf77d65fa2f9e203ed805cd7d78b9802ee3aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74021942"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-privileged-identity-management"></a>邀请来宾用户并在特权标识管理中分配 Azure 资源角色

Azure 活动目录 （Azure AD） 来宾用户是 Azure AD 中的企业对企业 （B2B） 协作功能的一部分，因此您可以在 Azure AD 中管理外部来宾用户和供应商作为来宾。 将 B2B 协作与 Azure AD 特权标识管理 （PIM） 相结合时，可以将合规性和治理要求扩展到来宾。 例如，您可以将这些特权标识管理功能用于 Azure 标识任务，以便与来宾执行以下功能：

- 分配对特定 Azure 资源的访问权限
- 启用实时访问
- 指定分配持续时间和结束日期
- 在活动分配或激活时需要多重身份验证
- 执行访问评审
- 使用警报和审核日志

本文介绍如何邀请来宾加入您的组织，并使用特权标识管理管理他们对 Azure 资源的访问。

## <a name="when-would-you-invite-guests"></a>你什么时候邀请客人？

以下是您何时邀请来宾加入您的组织的几个例子：

- 允许仅拥有电子邮件帐户的外部个体私营供应商访问项目的 Azure 资源。
- 允许使用本地 Active Directory 联合身份验证服务的大型组织的外部合作伙伴访问费用应用程序。
- 允许组织外的支持工程师（如 Microsoft 支持部门）临时访问 Azure 资源，以解决问题。

## <a name="how-does-collaboration-using-b2b-guests-work"></a>使用 B2B 来宾的协作如何工作？

使用 B2B 协作时，可以邀请外部用户作为来宾加入您的组织。 来宾可以作为组织中的用户进行管理，但来宾必须在其主组织中进行身份验证，而不是在 Azure AD 组织中进行身份验证。 这意味着，如果来宾不再有权访问其家庭组织，他们也会无法访问您的组织。 例如，如果来宾离开其组织，他们将自动无法访问您在 Azure AD 中与他们共享的任何资源，而无需执行任何操作。 有关 B2B 协作的详细信息，请参阅[Azure 活动目录 B2B 中的来宾用户访问是什么？](../b2b/what-is-b2b.md)

![显示来宾用户在其主目录中如何进行身份验证的图表](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>检查来宾协作设置

为了确保可以邀请来宾加入您的组织，应检查来宾协作设置。

1. 登录到[Azure 门户](https://portal.azure.com/)。

1. 选择**Azure 活动目录** > **用户设置**。

1. 选择 **"管理外部协作设置**"。

    ![显示权限、邀请和协作限制设置的外部协作设置页面](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. 确保**来宾邀请者角色的管理员和用户可以邀请**切换设置为 **"是**"。

## <a name="invite-a-guest-and-assign-a-role"></a>邀请来宾并分配角色

使用特权标识管理，可以邀请来宾，并使他们有资格担任 Azure 资源角色。

1. 使用[特权角色管理员](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)或[用户管理员](../users-groups-roles/directory-assign-admin-roles.md#user-administrator)角色的成员登录到[Azure 门户](https://portal.azure.com/)。

1. 打开**Azure AD 特权标识管理**。

1. 选择“Azure 资源”****。

1. 使用资源筛选器**** 对托管资源的列表进行筛选。

1. 选择要管理的资源，如资源、资源组、订阅或管理组。

    您应该将范围设置为仅满足客人的需求。

1. 在“管理”下，选择“角色”**** 以查看 Azure 资源的角色列表。

    ![Azure 资源角色列表，显示处于活动状态且符合条件的用户数](./media/pim-resource-roles-external-users/resources-roles.png)

1. 选择用户所需的最小角色。

    ![列出该角色的当前成员的选定角色页面](./media/pim-resource-roles-external-users/selected-role.png)

1. 在角色页上，选择 **"添加成员**"以打开"新建分配"窗格。

1. 单击“选择成员或组”。****

    ![新分配 - 选择列出用户和组的成员或组窗格以及"邀请"选项](./media/pim-resource-roles-external-users/select-member-group.png)

1. 要邀请来宾，请单击"**邀请**"。

    ![邀请带框的来宾页面输入电子邮件地址并指定个人邮件](./media/pim-resource-roles-external-users/invite-guest.png)

1. 选择来宾后，单击"**邀请**"。

    来宾应添加为选定成员。

1. 在"**选择成员或组**"窗格中，单击"**选择**"。

1. 在 **"成员资格设置"** 窗格中，选择分配类型和持续时间。

    ![新分配 - 成员设置页面，具有指定分配类型、开始日期和结束日期的选项](./media/pim-resource-roles-external-users/membership-settings.png)

1. 要完成分配，请选择 **"完成"，** 然后**添加**。

    来宾角色分配将显示在您的角色列表中。

    ![将来宾列为合格角色页](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>激活来宾角色

如果您是外部用户，则必须接受邀请才能成为 Azure AD 组织中的客人，并可能激活角色分配。

1. 打开电子邮件与您的邀请。 该电子邮件如下所示。

    ![使用目录名称、个人消息和入门链接发送电子邮件邀请](./media/pim-resource-roles-external-users/email-invite.png)

1. 在电子邮件中选择 **"入门"** 链接。

1. 查看权限后，单击“接受”。****

    ![查看浏览器中的权限页，其中包含组织希望您查看的权限列表](./media/pim-resource-roles-external-users/invite-accept.png)

1. 系统可能会要求您接受使用条款，并指定是否要保持登录状态。 在 Azure 门户中，如果您*有资格*担任角色，则您尚未有权访问资源。

1. 要激活角色分配，请使用激活角色链接打开电子邮件。 该电子邮件如下所示。

    ![显示您有资格使用"激活"角色链接的角色的电子邮件](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. 选择 **"激活角色"** 以在特权身份管理中打开符合条件的角色。

    ![我在特权身份管理中的角色页面列出您的合格角色](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. 在"操作"下，选择 **"激活"** 链接。

    需要指定一些信息来激活角色，具体取决于角色设置。

1. 指定角色设置后，单击“激活”，以激活角色。****

    ![激活页面列表范围和选项以指定开始时间、持续时间和原因](./media/pim-resource-roles-external-users/activate-role.png)

    如果并未要求管理员批准你的请求，则你应具有了对指定资源的访问权限。

## <a name="view-activity-for-a-guest"></a>查看来宾的活动

您可以查看审核日志，以跟踪来宾正在执行的操作。

1. 作为管理员，打开特权标识管理并选择已共享的资源。

1. 选择 **"资源审核**"以查看该资源的活动。 以下示例演示了资源组的活动。

    ![Azure 资源 - 资源审核页面列出时间、请求者和操作](./media/pim-resource-roles-external-users/audit-resource.png)

1. 要查看来宾的活动，请选择**Azure 活动目录** > **用户** > *来宾名称*。

1. 选择**审核日志**以查看组织的审核日志。 可以根据需要指定筛选器。

    ![目录审核日志列出日期、目标、启动日期和活动](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>后续步骤

- [在特权标识管理中分配 Azure AD 管理员角色](pim-how-to-add-role-to-user.md)
- [Azure AD B2B 协作中的访客用户访问是什么？](../b2b/what-is-b2b.md)
