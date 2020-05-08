---
title: 准备 Azure Migrate 来使用 ISV 工具/Movere
description: 准备 Azure Migrate 来使用 ISV 工具/Movere
ms.topic: how-to
ms.date: 05/07/2020
ms.openlocfilehash: b2ac95743e97bce41f38a4078e5e38f6b1bf7cd9
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82906977"
---
# <a name="prepare-to-work-with-isv-toolsmovere"></a>准备使用 ISV 工具/Movere

如果已将[ISV 工具](migrate-services-overview.md#isv-integration)或 Movere 添加到 Azure Migrate 项目，则在链接该工具之前需要准备几个步骤，并将数据发送到 Azure Migrate。 

## <a name="check-azure-ad-permissions"></a>检查 Azure AD 权限

Azure 用户帐户需要几个权限：

- 向 Azure 租户注册 Azure AD 应用的权限。
- 在订阅级别将角色分配到 Azure AD 应用的权限。


## <a name="set-permissions-to-register-an-azure-ad-app"></a>设置注册 Azure AD 应用的权限

1. 在 Azure Active Directory 中，检查帐户的角色。 如果你拥有用户角色，请单击左侧的 "**用户设置**"，并验证用户是否可以注册应用程序。
2. 如果设置为 **"是"**，则 Azure AD 租户中的任何用户都可以注册应用。 如果不能，则只有管理员用户可以注册应用。
3. 如果你没有权限，则管理员用户可以提供具有[应用程序管理员](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-administrator)角色的用户帐户，以便你可以注册应用。
4. 将该工具链接到 Azure Migrate 后，管理员可以从帐户中删除该角色。

## <a name="set-permissions-to-assign-a-role-to-an-azure-ad-app"></a>设置将角色分配到 Azure AD 应用的权限
 
在 Azure 订阅中，你的帐户需要 **/Write/* 访问权限**，才能将角色分配给 Azure AD 应用。 

1. 若要检查订阅权限，请在 "Azure 门户中，打开"**订阅**"。
2. 选择相关订阅。 如果看不到，请选择 "**全局订阅" 筛选器**。 
3. 选择“我的权限”  。 然后，选择“单击此处查看此订阅的完整访问详细信息”  。
4. 在 "**角色分配** > "**视图**中，检查权限。
5. 如果帐户没有权限，请要求订阅管理员将你添加到 "[用户访问管理员](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator)" 角色或 "[所有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)" 角色。
 

## <a name="start-using-the-tool"></a>开始使用该工具

1. 如果该工具还没有许可证或免费试用版，请在 Azure Migrate 的工具项的 "**注册**" 中，单击 "**了解详细信息**"。
2. 在该工具中，按照说明将工具链接到 Azure Migrate 项目，并将数据发送到 Azure Migrate。

## <a name="next-steps"></a>后续步骤

按照 ISV 或 Movere 说明将数据发送到 Azure Migrate。

   
