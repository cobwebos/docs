---
title: 管理单元故障排除和常见问题解答 - Azure Active Directory | Microsoft Docs
description: 在 Azure Active Directory 中调查管理单元以授予作用域有限的权限。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f9af8ee3bf87cfd5d2e74adfce618c7cf8c7e63
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91450324"
---
# <a name="azure-ad-administrative-units-troubleshooting-and-faq"></a>Azure AD 管理单元：故障排除和常见问题解答

若要在 Azure Active Directory (Azure AD) 中实现更精细的管理控制，可将用户分配到作用域限定为一个或多个管理单元 (AU) 的 Azure AD 角色。 有关常见任务的 PowerShell 脚本示例，请参阅 [使用管理单元](/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0&preserve-view=true)。

## <a name="frequently-asked-questions"></a>常见问题解答

**问：我为何无法创建管理单元？**

**答:** 只有“全局管理员”或“特权角色管理员”能够在 Azure AD 中创建管理单元。 请进行检查以确保尝试创建管理单元的用户分配有“全局管理员”或“特权角色管理员”角色。

**问：我向管理单元中添加了一个组。为什么组成员仍然没有显示在其中？**

**答:** 向管理单元中添加组时，不会导致将组的所有成员添加到该管理单元。 用户必须直接分配到管理单元。

**问：我刚刚添加（或删除）了管理单元的成员。为什么成员未显示（或仍然显示）在用户界面中？**

**答:** 有时候，管理单元的一个或多个成员的添加或删除可能需要几分钟才能反映在“管理单元”页上。 此外，你可以直接访问关联资源的属性，查看该操作是否已完成。 有关管理单元中的用户和组的详细信息，请参阅[列出用户的管理单元](roles-admin-units-add-manage-users.md)和[列出组的管理单元](roles-admin-units-add-manage-groups.md)。

**问：我是某个管理单元上的委托密码管理员。我为何无法重置特定用户的密码？**

**答:** 作为管理单元的管理员，你只能为分配到你的管理单元的用户重置密码。 请确保其密码重置失败的用户属于已分配给你的管理单元。 如果用户属于同一管理单元，但你仍无法重置其密码，请检查分配给该用户的角色。 

为了防止特权提升，其权限范围为某个管理单元的管理员不能重置其角色权限范围为组织的用户的密码。

**问：为何需要使用管理单元？为何不能使用安全组作为定义作用域的方式？**

**答:** 安全组已有用途和授权模型。 例如，用户管理员可以管理 Azure AD 组织中所有安全组的成员身份。 角色可以使用组来管理对应用程序（例如 Salesforce）的访问权限。 用户管理员不应该能够管理委托模型本身，但如果安全组已扩展为支持“资源分组”方案，则会导致他们能够管理。 管理单元（例如 Windows Server Active Directory 中的组织单位）的目标是提供一种方式对范围广泛的目录对象的管理进行作用域限定。 安全组本身可以是资源作用域的成员。 使用安全组来定义管理员可以管理的安全组集可能会造成混淆。

**问：向管理单元添加组意味着什么？**

**答:** 向管理单元添加组会将组本身引入到其权限范围也限定为该管理单元的任何用户管理员的管理范围中。 管理单元的用户管理员可以管理组本身的名称和成员身份。 它不会向管理单元的用户管理员授予权限来管理组的用户（例如，重置密码）。 若要向用户管理员授予管理用户的权限，用户必须是管理单元的直接成员。

**问：一个资源（用户或组）是否可以是多个管理单元的成员？**

**答:** 可以，一个资源可以是多个管理单元的成员。 资源可由所有对该资源具有权限且权限范围为整个组织或管理单元的管理员进行管理。

**问：B2C 组织中是否有管理单元？**

**答:** 没有，管理单元不可用于 B2C 组织。

**问：是否支持嵌套的管理单元？**

**答:** 否，不支持嵌套的管理单元。

**问：PowerShell 和图形 API 中是否支持管理单元？**

**答:** 是的。 你将在 [PowerShell cmdlet 文档](/powershell/module/Azuread/?view=azureadps-2.0&preserve-view=true) 和 [示例脚本](/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0&preserve-view=true)中找到管理单元支持。

查找 Microsoft Graph 中对 [administrativeUnit 资源类型](/graph/api/resources/administrativeunit?view=graph-rest-1.0&preserve-view=true)的支持。

## <a name="next-steps"></a>后续步骤

- [使用管理单元限制角色的作用域](directory-administrative-units.md)
- [对管理单元进行管理](roles-admin-units-manage.md)