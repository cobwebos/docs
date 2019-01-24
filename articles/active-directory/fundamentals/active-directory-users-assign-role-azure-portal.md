---
title: 向用户分配目录角色 - Azure Active Directory | Microsoft Docs
description: 有关如何为具有 Azure Active Directory 的用户分配管理员和非管理员角色的说明。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.openlocfilehash: ea5fd592dfbebd59df0cbd72c49093ffe383feeb
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2019
ms.locfileid: "54448748"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>为具有 Azure Active Directory 的用户分配管理员和非管理员角色
如果组织中的用户需要管理 Azure Active Directory (Azure AD) 资源的权限，则必须根据用户需要权限才能执行的操作为 Azure AD 中的用户分配适当的角色。

有关可用角色的详细信息，请参阅[在 Azure Active Directory 中分配管理员角色](../users-groups-roles/directory-assign-admin-roles.md)。 有关添加用户的详细信息，请参阅[将新用户添加到 Azure Active Directory](add-users-azure-active-directory.md)。

## <a name="assign-roles"></a>分配角色
常用方法是在用户的“目录角色”页面上将 Azure AD 角色分配给用户。

还可以使用 Privileged Identity Management (PIM) 分配角色。 了解如何使用 PIM 的详细信息，请参阅 [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management)。

### <a name="to-assign-a-role-to-a-user"></a>向用户分配角色
1. 使用目录的全局管理员帐户登录到 [Azure 门户](https://portal.azure.com/)。

2. 选择“Azure Active Directory”，选择“用户”，然后搜索并选择要获得角色分配的用户。 例如，Alain Charon。

3. 在“Alain Charon - 配置文件”页上，选择“目录角色”。

    将出现“Alain Charon - 目录角色”页面。

4. 依次选择“添加角色”、要分配给 Alain 的角色（例如，“应用程序管理员”）和“选择”。

    ![目录角色页面，显示所选角色](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    应用程序管理员角色分配给 Alain Charon，显示在“Alain Charon - 目录角色”页面上。

## <a name="remove-a-role-assignment"></a>删除角色分配
如果需要从用户删除角色分配，也可从“Alain Charon - 目录角色”页面执行此操作。

### <a name="to-remove-a-role-assignment-from-a-user"></a>从用户删除角色分配

1. 选择“Azure Active Directory”，选择“用户”，然后搜索并选择删除分配了角色的用户。 例如，Alain Charon。

2. 依次选择“目录角色”、“应用程序管理员”和“删除角色”。

    ![目录角色页面，显示所选角色和删除选项](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    应用程序管理员角色已从 Alain Charon 删除，并且不再显示在“Alain Charon - 目录角色”页面上。

## <a name="next-steps"></a>后续步骤
- [添加或删除用户](add-users-azure-active-directory.md)

- [添加或更改配置文件信息](active-directory-users-profile-azure-portal.md)

- [添加另一个目录中的来宾用户](../b2b/what-is-b2b.md)

也可以执行其他用户管理任务，例如分配委托、使用策略以及共享用户帐户。 有关其他可用操作的详细信息，请参阅 [Azure Active Directory 用户管理文档](../users-groups-roles/index.yml)。


