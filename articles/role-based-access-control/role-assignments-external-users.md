---
title: 使用 RBAC 和 Azure 门户为外部用户添加或删除角色分配
description: 了解如何使用基于 Azure 角色的访问控制 （RBAC） 为组织外部的用户授予对 Azure 资源的访问。
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
ms.openlocfilehash: 1c440b85f792ac5bb1336f4d20f930aafc38ad7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245637"
---
# <a name="add-or-remove-role-assignments-for-external-guest-users-using-azure-rbac-and-the-azure-portal"></a>使用 Azure RBAC 和 Azure 门户为外部来宾用户添加或删除角色分配

[基于 Azure 角色的访问控制 （RBAC）](overview.md)为大型组织和与外部协作者、供应商或自由职业者合作的中小型企业提供更好的安全管理，这些企业需要访问环境中的特定资源，但不一定访问整个基础结构或任何与计费相关的作用域。 您可以使用[Azure 活动目录 B2B](../active-directory/b2b/what-is-b2b.md)中的功能与外部来宾用户协作，也可以使用 RBAC 仅授予来宾用户在环境中所需的权限。

## <a name="prerequisites"></a>先决条件

若要添加或删除角色分配，必须拥有以下权限：

- `Microsoft.Authorization/roleAssignments/write` 和 `Microsoft.Authorization/roleAssignments/delete` 权限，例如[用户访问管理员](built-in-roles.md#user-access-administrator)或[所有者](built-in-roles.md#owner)

## <a name="when-would-you-invite-guest-users"></a>您何时会邀请访客用户？

下面是一些示例方案，当您可以邀请来宾用户加入您的组织并授予权限时：

- 允许仅拥有电子邮件帐户的外部个体私营供应商访问项目的 Azure 资源。
- 允许外部合作伙伴管理某些资源或整个订阅。
- 允许组织外的支持工程师（如 Microsoft 支持部门）临时访问 Azure 资源，以解决问题。

## <a name="permission-differences-between-member-users-and-guest-users"></a>成员用户和来宾用户之间的权限差异

目录的本机成员（成员用户）具有不同于作为 B2B 协作来宾（来宾用户）从其他目录中邀请的用户具有不同的权限。 例如，成员用户可以读取几乎所有目录信息，而来宾用户具有受限的目录权限。 有关成员用户和来宾用户的详细信息，请参阅[Azure 活动目录中的默认用户权限是什么？](../active-directory/fundamentals/users-default-permissions.md)

## <a name="add-a-guest-user-to-your-directory"></a>将来宾用户添加到目录

按照以下步骤使用 Azure 活动目录页面将来宾用户添加到目录。

1. 确保组织的外部协作设置已配置为允许您邀请来宾。 有关详细信息，请参阅启用[B2B 外部协作并管理谁可以邀请来宾](../active-directory/b2b/delegate-invitations.md)。

1. 在 Azure 门户中，单击**Azure 活动目录** > **用户** > **"新来宾用户**"。

    ![Azure 门户中的新来宾用户功能](./media/role-assignments-external-users/invite-guest-user.png)

1. 按照步骤添加新来宾用户。 有关详细信息，请参阅在[Azure 门户中添加 Azure 活动目录 B2B 协作用户](../active-directory/b2b/add-users-administrator.md#add-guest-users-to-the-directory)。

将来宾用户添加到目录后，你可以向来宾用户发送指向共享应用程序的直接链接，或者来宾用户可以单击邀请电子邮件中的兑换 URL。

![来宾用户邀请电子邮件](./media/role-assignments-external-users/invite-email.png)

要使来宾用户能够访问您的目录，他们必须完成邀请过程。

![来宾用户邀请审阅权限](./media/role-assignments-external-users/invite-review-permissions.png)

有关邀请过程的详细信息，请参阅 Azure[活动目录 B2B 协作邀请兑换](../active-directory/b2b/redemption-experience.md)。

## <a name="add-a-role-assignment-for-a-guest-user"></a>为来宾用户添加角色分配

在 RBAC 中，若要授予访问权限，请分配角色。 要为来宾用户添加角色分配，请执行与成员用户、组、服务主体或托管标识[相同的步骤](role-assignments-portal.md#add-a-role-assignment)。 按照以下步骤，为不同作用域的来宾用户添加角色分配。

1. 在 Azure 门户中，单击 **"所有服务**"。

1.  选择访问应用于的资源集，也称为作用域。 例如，可以选择“管理组”、“订阅”、“资源组”或某个资源************。

1. 单击特定的资源。

1. 单击**访问控制 （IAM）。**

    以下屏幕截图显示了资源组的访问控制 （IAM） 边栏选项卡的示例。 如果在此处进行任何访问控制更改，这些更改将仅应用于资源组。

    ![资源组的“访问控制(IAM)”边栏选项卡](./media/role-assignments-external-users/access-control-resource-group.png)

1. 单击“角色分配”选项卡以查看在此范围内的所有角色分配****。

1. 单击**Add** > "**添加添加角色分配**"以打开"添加角色分配"窗格。

    如果没有分配角色的权限，则将禁用“添加角色分配”选项。

    ![添加菜单](./media/role-assignments-external-users/add-menu.png)

1. 在“角色”下拉列表中选择一个角色，例如“虚拟机参与者”。********

1. 在 **"选择**"列表中，选择来宾用户。 如果在列表中看不到用户，则可以在 **"选择"** 框中键入以搜索目录以获取显示名称、电子邮件地址和对象标识符。

   ![“添加角色分配”窗格](./media/role-assignments-external-users/add-role-assignment.png)

1. 单击 **"保存**"以在所选作用域中分配角色。

    ![虚拟机参与者的角色分配](./media/role-assignments-external-users/access-control-role-assignments.png)

## <a name="add-a-role-assignment-for-a-guest-user-not-yet-in-your-directory"></a>为目录中尚未的来宾用户添加角色分配

要为来宾用户添加角色分配，请执行与成员用户、组、服务主体或托管标识[相同的步骤](role-assignments-portal.md#add-a-role-assignment)。

如果来宾用户尚未在目录中，则可以直接从"添加角色分配"窗格邀请该用户。

1. 在 Azure 门户中，单击 **"所有服务**"。

1.  选择访问应用于的资源集，也称为作用域。 例如，可以选择“管理组”、“订阅”、“资源组”或某个资源************。

1. 单击特定的资源。

1. 单击**访问控制 （IAM）。**

1. 单击“角色分配”选项卡以查看在此范围内的所有角色分配****。

1. 单击**Add** > "**添加添加角色分配**"以打开"添加角色分配"窗格。

    ![添加菜单](./media/role-assignments-external-users/add-menu.png)

1. 在“角色”下拉列表中选择一个角色，例如“虚拟机参与者”。********

1. 在 **"选择"** 列表中，键入要邀请的人员的电子邮件地址并选择此人。

   ![在"添加角色分配"窗格中邀请来宾用户](./media/role-assignments-external-users/add-role-assignment-new-guest.png)

1. 单击 **"保存**"可将来宾用户添加到目录、分配角色并发送邀请。

    几分钟后，您将看到有关角色分配的通知和有关邀请的信息。

    ![角色分配和邀请用户通知](./media/role-assignments-external-users/invited-user-notification.png)

1. 要手动邀请来宾用户，请右键单击并复制通知中的邀请链接。 不要单击邀请链接，因为它会启动邀请过程。

    邀请链接将具有以下格式：

    `https://invitations.microsoft.com/redeem/...`

1. 向来宾用户发送邀请链接以完成邀请过程。

    有关邀请过程的详细信息，请参阅 Azure[活动目录 B2B 协作邀请兑换](../active-directory/b2b/redemption-experience.md)。

## <a name="remove-a-guest-user-from-your-directory"></a>从目录中删除来宾用户

在从目录中删除来宾用户之前，应首先删除该来宾用户的任何角色分配。 按照以下步骤从目录中删除来宾用户。

1. 在作用域（如管理组、订阅、资源组或资源）处打开**访问控制 （IAM），** 其中来宾用户具有角色分配。

1. 单击"**角色分配**"选项卡以查看所有角色分配。

1. 在角色分配列表中，在来宾用户旁边添加一个复选标记，该来宾用户具有要删除的角色分配。

   ![删除角色分配](./media/role-assignments-external-users/remove-role-assignment-select.png)

1. 单击 **“删除”**。

   ![“删除角色分配”消息](./media/role-assignments-external-users/remove-role-assignment.png)

1. 在显示的“删除角色分配”消息中，单击“是”。****

1. 在左侧导航栏中，单击**Azure 活动目录** > **用户**。

1. 单击要删除的来宾用户。

1. 单击“删除”****。

   ![删除来宾用户](./media/role-assignments-external-users/delete-guest-user.png)

1. 在出现的删除消息中单击“是”****。

## <a name="troubleshoot"></a>疑难解答

### <a name="guest-user-cannot-browse-the-directory"></a>来宾用户无法浏览目录

来宾用户的目录权限受到限制。 例如，来宾用户无法浏览目录，也无法搜索组或应用程序。 有关详细信息，请参阅[Azure 活动目录中的默认用户权限是什么？](../active-directory/fundamentals/users-default-permissions.md)

![来宾用户无法浏览目录中的用户](./media/role-assignments-external-users/directory-no-users.png)

如果来宾用户需要在目录中获得其他权限，则可以为来宾用户分配目录角色。 如果确实希望来宾用户具有对目录的完全读取访问权限，则可以将来宾用户添加到 Azure AD 中的[目录阅读器](../active-directory/users-groups-roles/directory-assign-admin-roles.md)角色。 有关详细信息，请参阅向[Azure 活动目录租户中的合作伙伴组织的用户授予权限](../active-directory/b2b/add-guest-to-role.md)。

![分配目录阅读器角色](./media/role-assignments-external-users/directory-roles.png)

### <a name="guest-user-cannot-browse-users-groups-or-service-principals-to-assign-roles"></a>来宾用户无法浏览用户、组或服务主体以分配角色

来宾用户的目录权限受到限制。 即使来宾用户是作用域中的[所有者](built-in-roles.md#owner)，如果他们尝试添加角色分配以授予其他人访问权限，他们也无法浏览用户、组或服务主体的列表。

![来宾用户无法浏览安全主体以分配角色](./media/role-assignments-external-users/directory-no-browse.png)

如果来宾用户知道目录中某人的确切登录名称，他们可以授予访问权限。 如果确实希望来宾用户具有对目录的完全读取访问权限，则可以将来宾用户添加到 Azure AD 中的[目录阅读器](../active-directory/users-groups-roles/directory-assign-admin-roles.md)角色。 有关详细信息，请参阅向[Azure 活动目录租户中的合作伙伴组织的用户授予权限](../active-directory/b2b/add-guest-to-role.md)。

### <a name="guest-user-cannot-register-applications-or-create-service-principals"></a>来宾用户无法注册应用程序或创建服务主体

来宾用户的目录权限受到限制。 如果来宾用户需要能够注册应用程序或创建服务主体，则可以将来宾用户添加到 Azure AD 中[的应用程序开发人员](../active-directory/users-groups-roles/directory-assign-admin-roles.md)角色。 有关详细信息，请参阅向[Azure 活动目录租户中的合作伙伴组织的用户授予权限](../active-directory/b2b/add-guest-to-role.md)。

![来宾用户无法注册应用程序](./media/role-assignments-external-users/directory-access-denied.png)

### <a name="guest-user-does-not-see-the-new-directory"></a>来宾用户看不到新目录

如果来宾用户被授予对目录的访问权限，但在尝试在其**目录 + 订阅**窗格中切换时，他们看不到 Azure 门户中列出的新目录，请确保来宾用户已完成邀请过程。 有关邀请过程的详细信息，请参阅 Azure[活动目录 B2B 协作邀请兑换](../active-directory/b2b/redemption-experience.md)。

### <a name="guest-user-does-not-see-resources"></a>来宾用户看不到资源

如果来宾用户被授予对目录的访问权限，但他们没有看到他们在 Azure 门户中被授予访问权限的资源，请确保来宾用户选择了正确的目录。 来宾用户可能有权访问多个目录。 要切换目录，请在左上角单击 **"目录 + 订阅**"，然后单击相应的目录。

![Azure 门户中的目录和订阅窗格](./media/role-assignments-external-users/directory-subscription.png)

## <a name="next-steps"></a>后续步骤

- [在 Azure 门户中添加 Azure Active Directory B2B 协作用户](../active-directory/b2b/add-users-administrator.md)
- [Azure Active Directory B2B 协作用户的属性](../active-directory/b2b/user-properties.md)
- [B2B 协作邀请电子邮件的元素 - Azure Active Directory](../active-directory/b2b/invitation-email-elements.md)
- [将来宾用户添加为共同管理员](classic-administrators.md#add-a-guest-user-as-a-co-administrator)