---
title: 了解 Azure 资源的拒绝分配 | Microsoft Docs
description: 了解 Azure 资源基于角色的访问控制 (RBAC) 中的拒绝分配。
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 2c663b587d2e9ee278fc774c2841899b060ccbcf
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74479359"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>了解 Azure 资源的拒绝分配

*拒绝分配*类似于角色分配，可将一组拒绝操作附加到特定范围内的用户、组或服务主体，以便拒绝访问。 即使角色分配向用户授予了访问权限，拒绝分配也会阻止用户执行特定的 Azure 资源操作。

本文介绍如何定义拒绝分配。

## <a name="how-deny-assignments-are-created"></a>How deny assignments are created

Deny assignments are created and managed by Azure to protect resources. Azure Blueprints and Azure managed apps use deny assignments to protect system-managed resources. Azure Blueprints and Azure managed apps are the only way that deny assignments can be created. You can't directly create your own deny assignments.  有关详细信息，请参阅[使用 Azure 蓝图资源锁保护新资源](../governance/blueprints/tutorials/protect-new-resources.md)。

> [!NOTE]
> You can't directly create your own deny assignments.

## <a name="compare-role-assignments-and-deny-assignments"></a>Compare role assignments and deny assignments

Deny assignments follow a similar pattern as role assignments, but also have some differences.

| 功能 | 角色分配 | Deny assignment |
| --- | --- | --- |
| 授予访问权限 | :heavy_check_mark: |  |
| 拒绝访问 |  | :heavy_check_mark: |
| Can be directly created | :heavy_check_mark: |  |
| Apply at a scope | :heavy_check_mark: | :heavy_check_mark: |
| Exclude principals |  | :heavy_check_mark: |
| Prevent inheritance to child scopes |  | :heavy_check_mark: |
| Apply to [classic subscription administrator](rbac-and-directory-admin-roles.md) assignments |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>拒绝分配属性

 拒绝分配具有以下属性：

> [!div class="mx-tableFixed"]
> | properties | 需要 | Type | 描述 |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | 是 | 字符串 | 拒绝分配的显示名称。 对于给定作用域，名称必须是唯一的。 |
> | `Description` | No | 字符串 | 拒绝分配的说明。 |
> | `Permissions.Actions` | 至少一个 Actions 或一个 DataActions | String[] | 用于指定拒绝分配阻止访问的管理操作的字符串数组。 |
> | `Permissions.NotActions` | No | String[] | 用于指定要从拒绝分配中排除的管理操作的字符串数组。 |
> | `Permissions.DataActions` | 至少一个 Actions 或一个 DataActions | String[] | 用于指定拒绝分配阻止访问的数据操作的字符串数组。 |
> | `Permissions.NotDataActions` | No | String[] | 用于指定要从拒绝分配中排除的数据操作的字符串数组。 |
> | `Scope` | No | 字符串 | 用于指定拒绝分配应用到的作用域的字符串。 |
> | `DoNotApplyToChildScopes` | No | 布尔 | 指定拒绝分配是否应用到子作用域。 默认值为 false。 |
> | `Principals[i].Id` | 是 | String[] | 拒绝分配应用到的 Azure AD 主体对象 ID（用户、组、服务主体或托管主体）的数组。 设置为空 GUID `00000000-0000-0000-0000-000000000000` 将表示所有主体。 |
> | `Principals[i].Type` | No | String[] | An array of object types represented by Principals[i].Id. Set to `SystemDefined` to represent all principals. |
> | `ExcludePrincipals[i].Id` | No | String[] | 拒绝分配不会应用到的 Azure AD 主体对象 ID（用户、组、服务主体或托管主体）的数组。 |
> | `ExcludePrincipals[i].Type` | No | String[] | ExcludePrincipals[i].Id 所表示的对象类型的数组。 |
> | `IsSystemProtected` | No | 布尔 | 指定此拒绝分配是否由 Azure 创建，且无法编辑或删除。 当前，所有拒绝分配受系统保护。 |

## <a name="the-all-principals-principal"></a>The All Principals principal

To support deny assignments, a system-defined principal named *All Principals* has been introduced. 此主体表示 Azure AD 目录中的所有用户、组、服务主体和托管标识。 如果主体 ID 是零 GUID `00000000-0000-0000-0000-000000000000` 且主体类型是 `SystemDefined`，则此主体表示所有主体。 In Azure PowerShell output, All Principals looks like the following:

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

All Principals can be combined with `ExcludePrincipals` to deny all principals except some users. All Principals has the following constraints:

- 只能用于 `Principals`，不能用于 `ExcludePrincipals`。
- `Principals[i].Type` 必须设置为 `SystemDefined`。

## <a name="next-steps"></a>后续步骤

* [List deny assignments for Azure resources using the Azure portal](deny-assignments-portal.md)
* [了解 Azure 资源的角色定义](role-definitions.md)
