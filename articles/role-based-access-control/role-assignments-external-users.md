---
title: 使用 Azure 门户为外部用户添加或删除 Azure 角色分配-Azure RBAC
description: 了解如何使用 Azure 门户和 Azure 基于角色的访问控制（Azure RBAC）为组织外部的用户授予对 Azure 资源的访问权限。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: 568cb7602d9cb454685ef69e820288bcc68563c4
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735753"
---
# <a name="add-or-remove-azure-role-assignments-for-external-guest-users-using-the-azure-portal"></a>使用 Azure 门户添加或删除外部来宾用户的 Azure 角色分配

使用[azure 基于角色的访问控制（AZURE RBAC）](overview.md) ，可以更好地管理大型组织，适用于需要访问环境中的特定资源，但不一定要访问整个基础结构或任何计费相关范围的小型和中型企业。 你可以使用[AZURE ACTIVE DIRECTORY B2B](../active-directory/b2b/what-is-b2b.md)中的功能与外部来宾用户合作，并且你可以使用 Azure RBAC 仅授予在你的环境中来宾用户需要的权限。

## <a name="prerequisites"></a>先决条件

若要添加或删除角色分配，必须拥有以下权限：

- `Microsoft.Authorization/roleAssignments/write` 和 `Microsoft.Authorization/roleAssignments/delete` 权限，例如[用户访问管理员](built-in-roles.md#user-access-administrator)或[所有者](built-in-roles.md#owner)

## <a name="when-would-you-invite-guest-users"></a>何时邀请来宾用户？

下面是几个示例方案，你可能会向组织邀请来宾用户并授予权限：

- 允许仅拥有电子邮件帐户的外部个体私营供应商访问项目的 Azure 资源。
- 允许外部合作伙伴管理某些资源或整个订阅。
- 允许组织外的支持工程师（如 Microsoft 支持部门）临时访问 Azure 资源，以解决问题。

## <a name="permission-differences-between-member-users-and-guest-users"></a>成员用户和来宾用户之间的权限差异

目录的本机成员（成员用户）具有的权限不同于作为 B2B 协作来宾（来宾用户）从另一个目录邀请的用户。 例如，在来宾用户具有受限的目录权限的情况下，用户可以读取几乎所有的目录信息。 有关成员用户和来宾用户的详细信息，请参阅[Azure Active Directory 中的默认用户权限是什么？](../active-directory/fundamentals/users-default-permissions.md)。

## <a name="add-a-guest-user-to-your-directory"></a>将来宾用户添加到目录

按照以下步骤，使用 "Azure Active Directory" 页将来宾用户添加到目录。

1. 确保你的组织的外部协作设置已配置为允许你邀请来宾。 有关详细信息，请参阅[启用 B2B 外部协作和管理可以邀请来宾的人员](../active-directory/b2b/delegate-invitations.md)。

1. 在 Azure 门户中，单击 " **Azure Active Directory** > **用户** > " "**新建来宾用户**"。

    ![Azure 门户中的新来宾用户功能](./media/role-assignments-external-users/invite-guest-user.png)

1. 按照步骤添加新的来宾用户。 有关详细信息，请参阅[在 Azure 门户中添加 AZURE ACTIVE DIRECTORY B2B 协作用户](../active-directory/b2b/add-users-administrator.md#add-guest-users-to-the-directory)。

将来宾用户添加到目录后，你可以向来宾用户发送指向共享应用程序的直接链接，或者来宾用户可以单击邀请电子邮件中的兑换 URL。

![来宾用户邀请电子邮件](./media/role-assignments-external-users/invite-email.png)

要使来宾用户能够访问你的目录，必须完成邀请。

![来宾用户邀请审阅权限](./media/role-assignments-external-users/invite-review-permissions.png)

有关邀请过程的详细信息，请参阅[AZURE ACTIVE DIRECTORY B2B 协作邀请兑换](../active-directory/b2b/redemption-experience.md)。

## <a name="add-a-role-assignment-for-a-guest-user"></a>为来宾用户添加角色分配

在 Azure RBAC 中，若要授予访问权限，你需要分配一个角色。 若要为来宾用户添加角色分配，请执行与成员用户、组、服务主体或托管标识[相同的步骤](role-assignments-portal.md#add-a-role-assignment)。 按照以下步骤为不同范围内的来宾用户添加角色分配。

1. 在 Azure 门户中，单击 "**所有服务**"。

1.  选择访问应用到的资源集，也称为 "作用域"。 例如，可以选择“管理组”、“订阅”、“资源组”或某个资源************。

1. 单击特定的资源。

1. 单击 "**访问控制（IAM）**"。

    以下屏幕截图显示了资源组的 "访问控制（IAM）" 边栏选项卡的示例。 如果在此处进行任何访问控制更改，这些更改仅适用于资源组。

    ![资源组的“访问控制(IAM)”边栏选项卡](./media/role-assignments-external-users/access-control-resource-group.png)

1. 单击“角色分配”选项卡以查看在此范围内的所有角色分配****。

1. 单击 "**添加** > " "添加**角色分配**"，打开 "添加角色分配" 窗格。

    如果没有分配角色的权限，则将禁用“添加角色分配”选项。

    ![添加菜单](./media/role-assignments-external-users/add-menu.png)

1. 在“角色”下拉列表中选择一个角色，例如“虚拟机参与者”。********

1. 在 "**选择**" 列表中，选择 "来宾用户"。 如果在列表中看不到用户，则可以在 "**选择**" 框中键入，以便在目录中搜索显示名称、电子邮件地址和对象标识符。

   ![“添加角色分配”窗格](./media/role-assignments-external-users/add-role-assignment.png)

1. 单击 "**保存**" 可在所选作用域分配角色。

    ![虚拟机参与者的角色分配](./media/role-assignments-external-users/access-control-role-assignments.png)

## <a name="add-a-role-assignment-for-a-guest-user-not-yet-in-your-directory"></a>为尚未在你的目录中的来宾用户添加角色分配

若要为来宾用户添加角色分配，请执行与成员用户、组、服务主体或托管标识[相同的步骤](role-assignments-portal.md#add-a-role-assignment)。

如果来宾用户还不在你的目录中，你可以直接从 "添加角色分配" 窗格邀请用户。

1. 在 Azure 门户中，单击 "**所有服务**"。

1.  选择访问应用到的资源集，也称为 "作用域"。 例如，可以选择“管理组”、“订阅”、“资源组”或某个资源************。

1. 单击特定的资源。

1. 单击 "**访问控制（IAM）**"。

1. 单击“角色分配”选项卡以查看在此范围内的所有角色分配****。

1. 单击 "**添加** > " "添加**角色分配**"，打开 "添加角色分配" 窗格。

    ![添加菜单](./media/role-assignments-external-users/add-menu.png)

1. 在“角色”下拉列表中选择一个角色，例如“虚拟机参与者”。********

1. 在 "**选择**" 列表中，键入要邀请的人员的电子邮件地址，并选择该用户。

   ![在 "添加角色分配" 窗格中邀请来宾用户](./media/role-assignments-external-users/add-role-assignment-new-guest.png)

1. 单击 "**保存**" 将来宾用户添加到目录，分配角色并发送邀请。

    几分钟后，你将看到有关角色分配和邀请信息的通知。

    ![角色分配和受邀用户通知](./media/role-assignments-external-users/invited-user-notification.png)

1. 若要手动邀请来宾用户，请右键单击并复制通知中的 "邀请" 链接。 不要单击邀请链接，因为它会启动邀请。

    邀请链接将采用以下格式：

    `https://invitations.microsoft.com/redeem/...`

1. 向来宾用户发送邀请链接以完成邀请。

    有关邀请过程的详细信息，请参阅[AZURE ACTIVE DIRECTORY B2B 协作邀请兑换](../active-directory/b2b/redemption-experience.md)。

## <a name="remove-a-guest-user-from-your-directory"></a>从目录中删除来宾用户

从目录中删除来宾用户之前，应该先删除该来宾用户的所有角色分配。 请按照以下步骤从目录中删除来宾用户。

1. 在作用域（例如管理组、订阅、资源组或资源）中打开**访问控制（IAM）** ，其中 guest 用户具有角色分配。

1. 单击 "**角色分配**" 选项卡以查看所有角色分配。

1. 在角色分配列表中，在包含要删除的角色分配的来宾用户旁边添加复选标记。

   ![删除角色分配](./media/role-assignments-external-users/remove-role-assignment-select.png)

1. 单击 **“删除”**。

   ![“删除角色分配”消息](./media/role-assignments-external-users/remove-role-assignment.png)

1. 在显示的“删除角色分配”消息中，单击“是”。****

1. 在左侧导航栏中，单击 " **Azure Active Directory** > **用户**"。

1. 单击要删除的来宾用户。

1. 单击 **“删除”** 。

   ![删除来宾用户](./media/role-assignments-external-users/delete-guest-user.png)

1. 在出现的删除消息中单击“是”****。

## <a name="troubleshoot"></a>疑难解答

### <a name="guest-user-cannot-browse-the-directory"></a>来宾用户无法浏览目录

来宾用户的目录权限受到限制。 例如，来宾用户无法浏览目录，也无法搜索组或应用程序。 有关详细信息，请参阅[Azure Active Directory 中的默认用户权限是什么？](../active-directory/fundamentals/users-default-permissions.md)。

![来宾用户无法浏览目录中的用户](./media/role-assignments-external-users/directory-no-users.png)

如果来宾用户在目录中需要额外的权限，则可以向来宾用户分配目录角色。 如果你确实希望来宾用户对目录拥有完全读取访问权限，则可以在 Azure AD 中将来宾用户添加到[目录读者](../active-directory/users-groups-roles/directory-assign-admin-roles.md)角色。 有关详细信息，请参阅[从 Azure Active Directory 租户中的合作伙伴组织向用户授予权限](../active-directory/b2b/add-guest-to-role.md)。

![分配目录读者角色](./media/role-assignments-external-users/directory-roles.png)

### <a name="guest-user-cannot-browse-users-groups-or-service-principals-to-assign-roles"></a>来宾用户不能浏览用户、组或服务主体来分配角色

来宾用户的目录权限受到限制。 即使来宾用户是作用域的[所有者](built-in-roles.md#owner)，如果他们尝试添加角色分配来向其他人授予访问权限，他们也无法浏览用户、组或服务主体的列表。

![来宾用户无法浏览安全主体来分配角色](./media/role-assignments-external-users/directory-no-browse.png)

如果来宾用户在目录中知道某人的确切登录名，则他们可以授予访问权限。 如果你确实希望来宾用户对目录拥有完全读取访问权限，则可以在 Azure AD 中将来宾用户添加到[目录读者](../active-directory/users-groups-roles/directory-assign-admin-roles.md)角色。 有关详细信息，请参阅[从 Azure Active Directory 租户中的合作伙伴组织向用户授予权限](../active-directory/b2b/add-guest-to-role.md)。

### <a name="guest-user-cannot-register-applications-or-create-service-principals"></a>来宾用户无法注册应用程序或创建服务主体

来宾用户的目录权限受到限制。 如果来宾用户需要能够注册应用程序或创建服务主体，则可以将来宾用户添加到 Azure AD 中的[应用程序开发者](../active-directory/users-groups-roles/directory-assign-admin-roles.md)角色。 有关详细信息，请参阅[从 Azure Active Directory 租户中的合作伙伴组织向用户授予权限](../active-directory/b2b/add-guest-to-role.md)。

![来宾用户无法注册应用程序](./media/role-assignments-external-users/directory-access-denied.png)

### <a name="guest-user-does-not-see-the-new-directory"></a>来宾用户看不到新目录

如果已向来宾用户授予对某个目录的访问权限，但他们在尝试在 "**目录 + 订阅**" 窗格中切换时看不到 Azure 门户中列出的新目录，请确保来宾用户已完成邀请。 有关邀请过程的详细信息，请参阅[AZURE ACTIVE DIRECTORY B2B 协作邀请兑换](../active-directory/b2b/redemption-experience.md)。

### <a name="guest-user-does-not-see-resources"></a>来宾用户看不到资源

如果已向来宾用户授予对某个目录的访问权限，但他们在 Azure 门户中看不到他们有权访问的资源，请确保来宾用户选择了正确的目录。 来宾用户可能有权访问多个目录。 若要切换目录，请在左上方单击 "**目录 + 订阅**"，然后单击相应的目录。

![目录 + 订阅窗格 Azure 门户](./media/role-assignments-external-users/directory-subscription.png)

## <a name="next-steps"></a>后续步骤

- [在 Azure 门户中添加 Azure Active Directory B2B 协作用户](../active-directory/b2b/add-users-administrator.md)
- [Azure Active Directory B2B 协作用户的属性](../active-directory/b2b/user-properties.md)
- [B2B 协作邀请电子邮件的元素 - Azure Active Directory](../active-directory/b2b/invitation-email-elements.md)
- [将来宾用户添加为共同管理员](classic-administrators.md#add-a-guest-user-as-a-co-administrator)