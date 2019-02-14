---
title: 在 PIM 中邀请外部用户并分配 Azure 资源角色 | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中邀请外部用户并分配 Azure 资源角色。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/29/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: a90d0d3d3f484044a0ffbab7a3c24a76c40aa74c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56208267"
---
# <a name="invite-external-users-and-assign-azure-resource-roles-in-pim"></a>在 PIM 中邀请外部用户并分配 Azure 资源角色

Azure Activity Directory (Azure AD) 企业到企业 (B2B) 是 Azure AD 内的一组功能。借助它，组织可以使用任何帐户与外部用户和供应商协作。 如果将 B2B 和 Azure AD Privileged Identity Management (PIM) 组合在一起使用，则可以继续向外部用户应用合规和管理要求。 例如，可以针对 Azure 资源将下列 PIM 功能用于外部用户：

- 分配对特定 Azure 资源的访问权限
- 启用实时访问
- 指定分配持续时间和结束日期
- 要求对活动分配或激活进行 MFA
- 执行访问评审
- 使用警报和审核日志

本文介绍了如何使用 PIM 邀请外部用户进入目录，及如何管理他们对 Azure 资源的访问。

## <a name="when-would-you-invite-external-users"></a>在什么情况下要邀请外部用户？

下面是一些可邀请外部用户进入目录的示例场景：

- 允许仅拥有电子邮件帐户的外部个体私营供应商访问项目的 Azure 资源。
- 允许使用本地 Active Directory 联合身份验证服务的大型组织的外部合作伙伴访问费用应用程序。
- 允许组织外的支持工程师（如 Microsoft 支持部门）临时访问 Azure 资源，以解决问题。

## <a name="how-does-external-collaboration-using-b2b-work"></a>如何使用 B2B 进行外部协作？

使用 B2B 时，可以邀请外部用户进入目录。 外部用户看上去是在目录中，但该用户没有任何与目录关联的凭据。 每当外部用户需要进行身份验证时，他们必须在其主目录中进行身份验证，而不是在你的目录中进行验证。 这意味着，如果外部用户不再拥有其主目录的访问权限，他们将自动失去对你的目录的访问权限。 例如，如果外部用户离开其组织，则你无需执行任何操作，他们即会自动失去对你在你的目录中与他们共享的任何资源的访问权限。 有关 B2B 的详细信息，请参阅[什么是 Azure Active Directory B2B 中的来宾用户访问权限？](../b2b/what-is-b2b.md)。

![B2B 和外部用户](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-external-collaboration-settings"></a>检查外部协作设置

应检查你的外部协作设置，以确保你可以邀请外部用户进入你的目录。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 单击“Azure Active Directory” > “用户设置”。

1. 单击“管理外部协作设置”。

    ![外部协作设置](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. 确保“管理员和具有来宾邀请者角色的用户可以邀请”开关设置为“是”。

## <a name="invite-an-external-user-and-assign-a-role"></a>邀请外部用户并分配角色

借助 PIM，可以邀请外部用户，并使他们如同成员用户一样符合 Azure 资源角色的条件。

1. 使用[特权角色管理员](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)或[用户帐户管理员](../users-groups-roles/directory-assign-admin-roles.md#user-account-administrator)角色成员用户的身份登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。

1. 单击“Azure 资源”。

1. 使用资源筛选器对托管资源的列表进行筛选。

1. 单击要管理的资源，如资源、资源组、订阅或管理组。

    应将范围设置为仅外部用户所需的内容。

1. 在“管理”下，单击“角色”以查看 Azure 资源的角色列表。

    ![Azure 资源角色](./media/pim-resource-roles-external-users/resources-roles.png)

1. 单击用户所需的最小角色。

    ![所选角色](./media/pim-resource-roles-external-users/selected-role.png)

1. 在角色页面，单击“添加成员”以打开“新建分配”窗格。

1. 单击“选择成员或组”。

    ![选择成员或组](./media/pim-resource-roles-external-users/select-member-group.png)

1. 单击“邀请”，以邀请外部用户。

    ![邀请来宾](./media/pim-resource-roles-external-users/invite-guest.png)

1. 指定外部用户后，单击“邀请”。

    应将外部用户添加为选定的成员。

1. 在“选择成员或组”窗格中单击“选择”。

1. 在“成员资格设置”窗格中，选择分配类型和持续时间。

    ![成员资格设置](./media/pim-resource-roles-external-users/membership-settings.png)

1. 单击“完成”，然后单击“添加”，以完成分配。

    该外部用户角色分配将在角色列表中显示。

    ![外部用户的角色分配](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-an-external-user"></a>以外部用户身份激活角色

外部用户首先需要接受进入 Azure AD 目录的邀请，然后才可能激活角色。

1. 打开包含目录邀请的电子邮件。 该电子邮件如下所示。

    ![电子邮件邀请](./media/pim-resource-roles-external-users/email-invite.png)

1. 单击电子邮件中的“开始”链接。

1. 查看权限后，单击“接受”。

    ![查看权限](./media/pim-resource-roles-external-users/invite-accept.png)

1. 可能会要求你接受使用条款，并指定是否要保持登录状态。

    此时会打开 Azure 门户。 若你刚刚才符合某个角色的条件，则没有对资源的访问权限。

1. 请打开包含激活角色链接的电子邮件，以激活角色。 该电子邮件如下所示。

    ![电子邮件邀请](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. 单击“激活角色”，以在 PIM 中打开你的符合条件的角色。

    ![我的角色 - 符合条件](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. 在“操作”下单击“激活”链接。

    需要指定一些信息来激活角色，具体取决于角色设置。

1. 指定角色设置后，单击“激活”，以激活角色。

    ![激活角色](./media/pim-resource-roles-external-users/activate-role.png)

    如果并未要求管理员批准你的请求，则你应具有了对指定资源的访问权限。

## <a name="view-activity-for-an-external-user"></a>查看外部用户的活动

如同对待成员用户一样，你可以通过查看审核日志来跟踪外部用户的活动。

1. 以管理员身份打开 PIM，并选择已共享的资源。

1. 单击“资源审核”，以查看该资源的活动。 以下示例演示了资源组的活动。

    ![资源审核](./media/pim-resource-roles-external-users/audit-resource.png)

1. 依次单击“Azure Active Directory” > “用户”>“外部用户”，以查看外部用户的活动。

1. 单击“审核日志”，以查看该目录的审核日志。 可以根据需要指定筛选器。

    ![目录审核](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>后续步骤

- [在 PIM 中分配 Azure AD 目录角色](pim-how-to-add-role-to-user.md)
- [什么是 Azure Active Directory B2B 中的来宾用户访问权限？](../b2b/what-is-b2b.md)
