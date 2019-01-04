---
title: 添加或删除用户 - Azure Active Directory | Microsoft Docs
description: 有关如何使用 Azure Active Directory 添加新用户或删除现有用户的说明。
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/04/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.openlocfilehash: dd4a9132d5f04870239be3127f723afb60b3ecef
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104673"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>使用 Azure Active Directory 添加或删除用户
在 Azure Active Directory (Azure AD) 租户中添加新用户或删除现有用户。

## <a name="add-a-new-user"></a>添加新用户
可使用 Azure Active Directory 门户创建新用户。

### <a name="to-add-a-new-user"></a>要添加新用户
1. 以目录全局管理员或用户管理员身份登录到 [Azure 门户](https://portal.azure.com/)。

2. 依次选择“Azure Active Directory”、“用户”、“新建用户”。

    ![用户 - 突出显示新用户的所有用户页面](media/add-users-azure-active-directory/new-user-all-users-blade.png)

3. 在“用户”页中，填写必填信息。

    ![添加新用户，具有用户信息的用户页](media/add-users-azure-active-directory/new-user-user-blade.png)

    - **名称（必需）。** 新用户的姓氏和名字。 例如，Mary Parker。

    - **用户名（必需）。** 新用户的用户名。 例如，mary@contoso.com。 
    
        用户名的域名部分必须是初始默认域名“<yourdomain name>.onmicrosoft.com”，或自定义域名（例如“contoso.com”）。 若要了解有关如何创建自定义域名的详细信息，请参阅[如何将自定义域名添加到 Azure Active Directory](add-custom-domain.md)。

    - **配置文件。** （可选）可添加有关用户的详细信息。 也可稍后添加用户信息。 有关添加用户信息的详细信息，请参阅[如何添加或更改用户配置文件信息](active-directory-users-profile-azure-portal.md)。

    - **组。** （可选）可将用户添加到一个或多个现有组。 也可稍后将用户添加到组。 有关将用户添加到组的详细信息，请参阅[如何创建基本组并添加成员](active-directory-groups-create-azure-portal.md)。

    - **目录角色。** （可选）可将用户添加到目录角色。 可将用户分配为全局管理员，或分配给 Azure AD 中的一个或多个其他管理员角色。 有关分配角色的详细信息，请参阅[如何为用户分配角色](active-directory-users-assign-role-azure-portal.md)。

4. 复制“密码”框中提供的自动生成的密码。 需要将此密码提供给用户以进行初始登录过程。

5. 选择“创建”。

    用户已创建并添加到 Azure AD 租户。

## <a name="add-a-new-user-within-a-hybrid-environment"></a>在混合环境中添加新用户
若环境同时具有 Azure Active Directory（云）和 Windows Server Active Directory（内部部署），则可以通过同步现有用户帐户数据来添加新用户。 有关混合环境和用户的详细信息，请参阅[将本地目录与 Azure Active Directory 进行集成](../hybrid/whatis-hybrid-identity.md)。

## <a name="delete-a-user"></a>删除用户
可使用 Azure Active Directory 门户删除现有用户。

### <a name="to-delete-a-user"></a>删除用户
1. 使用目录的全局管理员帐户登录到 [Azure 门户](https://portal.azure.com/)。

2. 选择“Azure Active Directory”并选择“用户”然后搜索并选择想要从 Azure AD 租户中删除的用户。 例如，Mary Parker。

3. 选择“删除用户”。

    ![用户 - 突出显示删除用户的所有用户页面](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

    该用户已删除并不再显示在“用户 - 所有用户”页上。 可在接下来的 30 天内于“已删除用户”页查看该用户，在此期间可将其还原。 有关还原用户的详细信息，请参阅[如何还原或永久删除最近删除的用户](active-directory-users-restore.md)。

    >[!Note]
    >必须使用 Windows Server Active Directory 更新归属于 Windows Server Active Directory 的用户的身份、联系信息或工作信息。 完成更新后，必须等待下一个同步周期完成才能看到更改。

## <a name="next-steps"></a>后续步骤
添加用户后，可以执行以下基本过程：

- [添加或更改配置文件信息](active-directory-users-profile-azure-portal.md)

- [向用户分配角色](active-directory-users-assign-role-azure-portal.md)

- [创建基本组并添加成员](active-directory-groups-create-azure-portal.md)

- [使用动态组和用户](../users-groups-roles/groups-create-rule.md)

或可执行其他用户管理任务，例如[从其他目录添加访客用户](../b2b/what-is-b2b.md)或[恢复已删除的用户](active-directory-users-restore.md)。 有关其他可用操作的详细信息，请参阅 [Azure Active Directory 用户管理文档](../users-groups-roles/index.yml)。