---
title: 管理单元故障排除和常见问题-Azure Active Directory |Microsoft Docs
description: 在 Azure Active Directory 中调查管理单元以授予受限范围的权限。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: b48bebe6aa5f9862d5f51fea257c4b7f3057639b
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88794469"
---
# <a name="azure-ad-administrative-units-troubleshooting-and-faq"></a>Azure AD 管理单元：故障排除和常见问题

为了 Azure Active Directory (Azure AD) 中的更精细的管理控制，可以将用户分配到一个 Azure AD 的作用域，该角色的作用域限制为一个或多个管理单元 (澳大利亚) 。 有关常见任务的 PowerShell 脚本示例，请参阅 [使用管理单元](/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)。

## <a name="frequently-asked-questions"></a>常见问题解答

**问：为什么无法创建管理单元？**

**答：** 只有 *全局管理员* 或 *特权角色管理员* 才能在 Azure AD 中创建管理单元。 检查以确保尝试创建管理单元的用户已分配有 " *全局管理员* " 或 " *特权角色管理员* " 角色。

**问：我将一个组添加到了管理单元。为什么组成员仍无法显示？**

**答：** 向管理单元添加组时，不会导致将组的所有成员添加到该管理单元。 用户必须直接分配到管理单元。

**问：我只是添加了 (或删除了) 管理单元的成员。为什么成员未显示 (或仍显示用户界面) ？**

**答：** 有时，处理管理单元的一个或多个成员的添加或删除操作可能需要几分钟才能反映在 " **管理单元** " 页上。 或者，您可以直接访问关联资源的属性，并查看该操作是否已完成。 有关澳大利亚用户和组的详细信息，请参阅 [列出用户的管理单元](roles-admin-units-add-manage-users.md) 和 [列出组的管理单元](roles-admin-units-add-manage-groups.md)。

**问：我是管理单元上的委派密码管理员。为什么无法重置特定用户的密码？**

**答：** 作为管理单元的管理员，你可以仅为分配给你的管理单元的用户重置密码。 请确保其密码重置失败的用户属于您已分配到的管理单元。 如果用户属于同一管理单元，但仍无法重置其密码，请检查分配给该用户的角色。 

若要防止特权提升，管理单元范围的管理员不能重置分配到组织范围内角色的用户的密码。

**问：为什么需要管理单元？我们不能使用安全组作为定义作用域的方式？**

**答：** 安全组具有现有用途和授权模型。 例如， *用户管理员*可以管理 Azure AD 组织中的所有安全组的成员身份。 角色可以使用组来管理对应用程序（如 Salesforce）的访问。 *用户管理员*应该无法管理委派模型，如果安全组已扩展为支持 "资源分组" 方案，则会产生这种情况。 管理单元（如 Windows Server Active Directory 中的组织单位）旨在提供一种将各种目录对象的管理作用域。 安全组本身可以是资源范围的成员。 使用安全组定义管理员可以管理的安全组集可能会造成混淆。

**问：向管理单元添加组是什么意思？**

**答：** 向管理单元添加组会将组本身引入到任何 *用户管理员* 的管理作用域中，该管理员的作用域也是该管理单元的作用域。 管理单元的用户管理员可以管理组本身的名称和成员身份。 它不会向 *用户管理员* 授予管理单元权限以管理组的用户 (例如，将其密码重置) 。 若要授予 *用户管理员* 管理用户的能力，用户必须是管理单元的直接成员。

**问：资源 (用户或组) 是否为多个管理单元的成员？**

**答：** 是，一个资源可以是多个管理单元的成员。 资源可由组织范围内的所有组织范围和管理单元的管理员管理，这些管理员具有对资源的权限。

**问：企业中的管理单元是否可用？**

**答：** 不可以，管理单元不能用于 B2C 组织。

**问：嵌套管理单元是否受支持？**

**答：** 不支持，不支持嵌套的管理单元。

**问： PowerShell 和图形 API 中是否支持管理单元？**

**答:** 是的。 你将在 [PowerShell cmdlet 文档](/powershell/module/Azuread/?view=azureadps-2.0-preview) 和 [示例脚本](/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0-preview)中找到管理单元支持。 

在 Microsoft Graph 中查找对 [administrativeUnit 资源类型](/graph/api/resources/administrativeunit?view=graph-rest-beta) 的支持。

## <a name="next-steps"></a>后续步骤

- [使用管理单元限制角色的作用域](directory-administrative-units.md)
- [对管理单元进行管理](roles-admin-units-manage.md)