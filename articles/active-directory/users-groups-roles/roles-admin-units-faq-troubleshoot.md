---
title: 管理单位故障排除和常见问题解答 - Azure 活动目录 |微软文档
description: 调查管理单位以授予 Azure 活动目录中具有受限作用域的权限。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 022658306d6e4d69174cc616d230cfe4892f1204
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684860"
---
# <a name="azure-ad-administrative-units-troubleshooting-and-faq"></a>Azure AD 管理单元：故障排除和常见问题解答

要在 Azure 活动目录 （Azure AD） 中进行更精细的管理控制，可以将用户分配给范围为一个或多个管理单位 （A） 的 Azure AD 角色。 有关常见任务的示例 PowerShell 脚本，请参阅[使用管理单位](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)。

## <a name="frequently-asked-questions"></a>常见问题

**问：为什么无法创建管理单位？**

**答：** 只有*全局管理员*或*特权角色管理员*才能在 Azure AD 中创建管理单元。 检查以确保为尝试创建管理单元的用户分配*了全局管理员*或*特权角色管理员角色角色*。

**问：我向管理单位添加了一个组。为什么组成员仍然不在那里出现？**

**答：** 将组添加到管理单位时，不会导致将组的所有成员添加到管理单位。 用户必须直接分配给管理单位。

**问：我刚刚添加（或删除）管理单位的成员。为什么成员未在用户界面中显示（或仍然显示）？**

**答：** 有时，处理行政单位的一个或多个成员的添加或删除可能需要几分钟时间才能反映在**行政单位**页面上。 或者，您可以直接转到关联的资源的属性，并查看操作是否已完成。 有关 A 中的用户和组的详细信息，请参阅[列出用户和](roles-admin-units-add-manage-users.md)[组管理单位列表和列出组管理单位](roles-admin-units-add-manage-groups.md)。

**问：我是管理单位的委派密码管理员。为什么我无法重置特定用户的密码？**

**答：** 作为管理单位的管理员，您只能为分配给管理单元的用户重置密码。 确保密码重置失败的用户属于已分配您管理单元。 如果用户属于同一管理单位，但仍无法重置其密码，请检查分配给该用户的角色。 

为了防止特权提升，管理单位范围管理员无法重置分配给具有组织范围的角色的用户的密码。

**问：为什么需要行政单位？我们难道不能使用安全组来定义作用域吗？**

**答：** 安全组具有现有用途和授权模型。 例如，*用户管理员*可以管理 Azure AD 组织中所有安全组的成员身份。 该角色可能使用组来管理对应用程序（如 Salesforce）的访问。 *用户管理员*不应能够管理委派模型本身，如果安全组扩展到支持"资源分组"方案，则会产生此结果。 管理单位（如 Windows Server 活动目录中的组织单位）旨在提供一种对各种目录对象的范围管理的范围的方法。 安全组本身可以是资源作用域的成员。 使用安全组定义管理员可以管理的安全组集可能会变得令人困惑。

**问：将组添加到管理单位意味着什么？**

**答：** 将组添加到管理单位会将组本身引入任何用户管理员的管理范围，这些*用户管理员*也属于该管理单位的范围。 管理单位的用户管理员可以管理组本身的名称和成员身份。 它不授予*管理单位的用户管理员*管理组用户的权限（例如，重置其密码）。 要授予*用户管理员*管理用户的能力，用户必须是管理单位的直接成员。

**问：资源（用户或组）是否可以是多个管理单位的成员？**

**答：** 可以，资源可以是多个管理单位的成员。 资源可以由对资源具有权限的所有组织范围和管理单位范围的管理员进行管理。

**问：B2C 组织中有可用的管理单位吗？**

**答：** 否，B2C 组织不提供管理单位。

**问：支持嵌套管理单元吗？**

**答：** 否，不支持嵌套管理单位。

**问：PowerShell 和图形 API 中支持管理单元吗？**

**答：** 是的。 您可以在[PowerShell cmdlet 文档](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0-preview)和[示例脚本](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0-preview)中找到对管理单元的支持。 

在 Microsoft 图形中查找对[管理单位资源类型](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/administrativeunit)的支持。

## <a name="next-steps"></a>后续步骤

- [使用管理单位限制角色范围](directory-administrative-units.md)
- [对管理单元进行管理](roles-admin-units-manage.md)
