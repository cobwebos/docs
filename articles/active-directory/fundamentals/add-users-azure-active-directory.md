---
title: 添加或删除用户 - Azure Active Directory | Microsoft Docs
description: 有关如何使用 Azure Active Directory 添加新用户或删除现有用户的说明。
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b01eb7730290fbf7340fc0a6d8cac8157498f64a
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013614"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>使用 Azure Active Directory 添加或删除用户

在 Azure Active Directory (Azure AD) 组织中添加新用户或删除现有用户。 若要添加或删除用户，您必须是用户管理员或全局管理员。

## <a name="add-a-new-user"></a>添加新用户

可使用 Azure Active Directory 门户创建新用户。

### <a name="to-add-a-new-user"></a>要添加新用户

1. 以组织的用户管理员身份登录到 [Azure 门户](https://portal.azure.com/)。

2. 依次选择“Azure Active Directory”、“用户”、“新建用户”。

    ![用户 - 突出显示新用户的所有用户页面](media/add-users-azure-active-directory/new-user-all-users-blade.png)

3. 在 "**新建用户**" 页上，选择 "**创建用户**"，然后添加用户的信息。

    ![添加新用户，具有用户信息的用户页](media/add-users-azure-active-directory/new-user-user-blade.png)

   - **名称（必需）** ：新用户的名字和姓氏。 例如，丽丽绿色。

   - **用户名（必需）** ：新用户的用户名。 例如，chris@contoso.com。

     用户名的域部分必须使用初始默认域名，<_yourdomainname_> 或 Azure AD 组织中的自定义域名（例如 contoso.com）。 您可以从可用域列表中进行选择。 还可以通过键入域名的一部分来筛选列表。 若要了解有关如何创建自定义域名的详细信息，请参阅[如何将自定义域名添加到 Azure Active Directory](add-custom-domain.md)。

   - **组**：你可以将用户添加到一个或多个现有组，也可以稍后执行此操作。 有关将用户添加到组的详细信息，请参阅[如何创建基本组并添加成员](active-directory-groups-create-azure-portal.md)。

   - **目录角色**：如果需要用户 Azure AD 管理权限，则可以将其添加到 Azure AD 角色。 可以将用户分配为全局管理员，或者分配为 Azure AD 中有限的管理员角色中的一个或多个。 有关分配角色的详细信息，请参阅[如何为用户分配角色](active-directory-users-assign-role-azure-portal.md)。

   - **作业信息**：可以在此处添加有关用户的详细信息，也可以稍后执行此操作。 有关添加用户信息的详细信息，请参阅[如何添加或更改用户配置文件信息](active-directory-users-profile-azure-portal.md)。

4. 复制“密码”框中提供的自动生成的密码。 你可以选择使用 "密码" 框中提供的自动生成的密码或创建自定义密码。 需要将此密码提供给用户以进行初始登录过程。

5. 选择“创建”。

将创建用户并将其添加到 Azure AD 组织。

## <a name="add-a-new-guest-user"></a>添加新的来宾用户

你还可以通过从**新用户**页面中选择 "**邀请用户**"，邀请新的来宾用户与你的组织协作。 如果你的组织的外部协作设置已配置为允许你邀请来宾，则会向用户发送电子邮件邀请，以便开始协作。 有关邀请 B2B 协作用户的详细信息，请参阅[邀请 b2b 用户 Azure Active Directory](../b2b/add-users-administrator.md)

## <a name="add-a-consumer-user"></a>添加使用者用户

在某些情况下，你可能想要在 Azure Active Directory B2C （Azure AD B2C）目录中手动创建使用者帐户。 有关创建使用者帐户的详细信息，请参阅[在 Azure AD B2C 中创建和删除使用者用户](../../active-directory-b2c/manage-users-portal.md)。

## <a name="add-a-new-user-within-a-hybrid-environment"></a>在混合环境中添加新用户

若环境同时具有 Azure Active Directory（云）和 Windows Server Active Directory（内部部署），则可以通过同步现有用户帐户数据来添加新用户。 有关混合环境和用户的详细信息，请参阅[将本地目录与 Azure Active Directory 进行集成](../hybrid/whatis-hybrid-identity.md)。

## <a name="delete-a-user"></a>删除用户

可使用 Azure Active Directory 门户删除现有用户。

### <a name="to-delete-a-user"></a>删除用户

1. 使用组织的用户管理员帐户登录到 [Azure 门户](https://portal.azure.com/)。

1. 选择“Azure Active Directory”并选择“用户”然后搜索并选择想要从 Azure AD 租户中删除的用户。 例如，Mary Parker。

1. 选择“删除用户”。

    ![用户 - 突出显示删除用户的所有用户页面](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

    该用户已删除并不再显示在“用户 - 所有用户”页上。 可在接下来的 30 天内于“已删除用户”页查看该用户，在此期间可将其还原。 有关还原用户的详细信息，请参阅[如何还原或永久删除最近删除的用户](active-directory-users-restore.md)。 删除某个用户后，该用户使用的任何许可证将可供其他用户使用。

    >[!Note]
    >必须使用 Windows Server Active Directory 更新归属于 Windows Server Active Directory 的用户的身份、联系信息或工作信息。 完成更新后，必须等待下一个同步周期完成才能看到更改。

## <a name="next-steps"></a>后续步骤

添加用户后，可以执行以下基本过程：

- [添加或更改配置文件信息](active-directory-users-profile-azure-portal.md)

- [向用户分配角色](active-directory-users-assign-role-azure-portal.md)

- [创建基本组并添加成员](active-directory-groups-create-azure-portal.md)

- [使用动态组和用户](../users-groups-roles/groups-create-rule.md)

或者，你可以执行其他用户管理任务，例如，[从另一个 Azure AD 组织添加来宾用户](../b2b/what-is-b2b.md)或[还原已删除的用户](active-directory-users-restore.md)。 有关其他可用操作的详细信息，请参阅 [Azure Active Directory 用户管理文档](../users-groups-roles/index.yml)。
