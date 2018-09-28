---
title: 了解 Azure RBAC 中的拒绝分配 | Microsoft 文档
description: 了解针对 Azure 中的资源基于角色的访问控制 (RBAC) 中的拒绝分配。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 8ef3a2ec44c5eff80d3a50a6c56805667e164ba8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46980162"
---
# <a name="understand-deny-assignments"></a>了解拒绝分配

“拒绝分配”类似于角色分配，可将一组拒绝操作绑定到特定范围内的用户、组或服务主体，以便拒绝访问。 此外，拒绝分配还可以排除主体并防止继承到子作用域，这点不同于角色分配。 当前，拒绝分配为“只读”，并且只能由 Azure 设置。 本文介绍如何定义拒绝分配。

## <a name="deny-assignment-properties"></a>拒绝分配属性

 拒绝分配具有以下属性：

> [!div class="mx-tableFixed"]
> | 属性 | 必选 | Type | Description |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | 是 | String | 拒绝分配的显示名称。 对于给定作用域，名称必须是唯一的。 |
> | `Description` | 否 | String | 拒绝分配的说明。 |
> | `Permissions.Actions` | 至少一个 Actions 或一个 DataActions | String[] | 用于指定拒绝分配阻止访问的管理操作的字符串数组。 |
> | `Permissions.NotActions` | 否 | String[] | 用于指定要从拒绝分配中排除的管理操作的字符串数组。 |
> | `Permissions.DataActions` | 至少一个 Actions 或一个 DataActions | String[] | 用于指定拒绝分配阻止访问的数据操作的字符串数组。 |
> | `Permissions.NotDataActions` | 否 | String[] | 用于指定要从拒绝分配中排除的数据操作的字符串数组。 |
> | `Scope` | 否 | String | 用于指定拒绝分配应用到的作用域的字符串。 |
> | `DoNotApplyToChildScopes` | 否 | 布尔 | 指定拒绝分配是否应用到子作用域。 默认值为 false。 |
> | `Principals[i].Id` | 是 | String[] | 拒绝分配应用到的 Azure AD 主体对象 ID（用户、组或服务主体）的数组。 设置为空 GUID `00000000-0000-0000-0000-000000000000` 以表示每个人。 |
> | `Principals[i].Type` | 否 | String[] | Principals[i].Id 所表示的对象类型的数组。设置为 `Everyone` 以表示每个人。 |
> | `ExcludePrincipals[i].Id` | 否 | String[] | 拒绝分配不应用到的 Azure AD 主体对象 ID（用户、组或服务主体）的数组。 |
> | `ExcludePrincipals[i].Type` | 否 | String[] | ExcludePrincipals[i].Id 所表示的对象类型的数组。 |
> | `IsSystemProtected` | 否 | 布尔 | 指定此拒绝分配是否由 Azure 创建，且无法编辑或删除。 当前，所有拒绝分配受系统保护。 |

## <a name="everyone-principal"></a>Everyone 主体

为了支持拒绝分配，引入了 Everyone 主体。 Everyone 主体表示 Azure AD 目录中的所有用户、组和服务主体。 如果主体 ID 是零 GUID `00000000-0000-0000-0000-000000000000` 且主体类型是 `Everyone`，则主体表示每个人。 Everyone 主体可以与 `ExcludePrincipals` 合并以拒绝除某些用户之外的每个人。 Everyone 主体具有以下约束：

- 只能用于 `Principals`，不能用于 `ExcludePrincipals`。
- `Principals[i].Type` 必须设置为 `Everyone`。

## <a name="next-steps"></a>后续步骤

* [使用 RBAC 和 REST API 列出拒绝分配](deny-assignments-rest.md)
* [了解角色定义](role-definitions.md)
