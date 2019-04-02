---
title: 添加或删除用户 - Azure Active Directory | Microsoft Docs
description: 有关如何使用 Azure Active Directory 添加新用户或删除现有用户的说明。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9cda9f976a7680a1338584e4308426683de82a79
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2019
ms.locfileid: "58802111"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>使用 Azure Active Directory 添加或删除用户
添加新用户或从组织 Azure Active Directory (Azure AD) 中删除现有用户。

## <a name="add-a-new-user"></a>添加新用户
可使用 Azure Active Directory 门户创建新用户。

### <a name="to-add-a-new-user"></a>添加新用户
1. 登录到[Azure 门户](https://portal.azure.com/)作为组织的用户管理员。

2. 依次选择“Azure Active Directory”、“用户”、“新建用户”。

    ![“用户 - 所有用户”页，其中突出显示了“新建用户”](media/add-users-azure-active-directory/new-user-all-users-blade.png)

3. 在“用户”页上，填写所需的信息。

    ![添加新用户，包含用户信息的“用户”页](media/add-users-azure-active-directory/new-user-user-blade.png)

   - **名称（必填）。** 新用户的名字和姓氏。 例如，Mary Parker。

   - **用户名（必填）。** 新用户的用户名。 例如，mary@contoso.com。
    
       用户名的域名部分必须是初始默认域名“<yourdomain name>.onmicrosoft.com”，或自定义域名（例如“contoso.com”）。 若要了解有关如何创建自定义域名的详细信息，请参阅[如何将自定义域名添加到 Azure Active Directory](add-custom-domain.md)。

   - **个人资料。** （可选）你可以添加关于用户的详细信息。 也可以在以后添加用户信息。 有关添加用户信息的详细信息，请参阅[如何添加或更改用户个人资料信息](active-directory-users-profile-azure-portal.md)。

   - **组。** （可选）可以将用户添加到一个或多个现有组。 也可以在以后将用户添加到组中。 有关将用户添加到组中的详细信息，请参阅[如何创建基本组并添加成员](active-directory-groups-create-azure-portal.md)。

   - **目录角色。** 或者，可以添加到用户的 Azure AD 管理员角色。 您可以分配用户具有 Azure AD 中的全局管理员或一个或多个受限制的管理员角色。 有关分配角色的详细信息，请参阅[如何向用户分配角色](active-directory-users-assign-role-azure-portal.md)。

4. 复制“密码”框中提供的自动生成的密码。 需要将此密码提供给用户以进行初始登录过程。

5. 选择“创建”。

    用户已创建并添加到 Azure AD 租户。

## <a name="add-a-new-user-within-a-hybrid-environment"></a>在混合环境内添加新用户
如果你的环境中同时包含 Azure Active Directory（云）和 Windows Server Active Directory（本地），则可以通过同步现有用户帐户数据来添加新用户。 有关混合环境和用户的详细信息，请参阅[将本地目录与 Azure Active Directory 进行集成](../hybrid/whatis-hybrid-identity.md)。

## <a name="delete-a-user"></a>删除用户
可使用 Azure Active Directory 门户删除现有用户。

### <a name="to-delete-a-user"></a>删除用户
1. 登录到[Azure 门户](https://portal.azure.com/)为组织中使用用户管理员帐户。

2. 选择“Azure Active Directory”，选择“用户”，然后搜索并选择要从 Azure AD 租户中删除的用户。 例如，_Mary Parker_。

3. 选择“删除用户”。

    ![“用户 - 所有用户”页，其中突出显示了“删除用户”](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

    该用户已删除并不再显示在“用户 - 所有用户”页上。 可在接下来的 30 天内于“已删除用户”页查看该用户，在此期间可将其还原。 有关还原用户的详细信息，请参阅[如何还原或永久删除最近删除的用户](active-directory-users-restore.md)。 删除用户时，使用用户的任何许可证都可供其他用户使用。

    >[!Note]
    >必须使用 Windows Server Active Directory 更新其授权来源为 Windows Server Active Directory 的用户的标识、联系信息或工作信息。 完成更新后，必须等待下一个同步周期完成才能看到更改。

## <a name="next-steps"></a>后续步骤

添加用户后，可以执行以下基本过程：

- [添加或更改配置文件信息](active-directory-users-profile-azure-portal.md)

- [向用户分配角色](active-directory-users-assign-role-azure-portal.md)

- [创建基本组并添加成员](active-directory-groups-create-azure-portal.md)

- [使用动态组和用户](../users-groups-roles/groups-create-rule.md)

或可执行其他用户管理任务，例如[从其他目录添加访客用户](../b2b/what-is-b2b.md)或[恢复已删除的用户](active-directory-users-restore.md)。 有关其他可用操作的详细信息，请参阅 [Azure Active Directory 用户管理文档](../users-groups-roles/index.yml)。
