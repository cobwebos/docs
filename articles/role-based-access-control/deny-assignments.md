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
ms.date: 06/13/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 432703b5acb4cd56dac9b25edf99165ca26b0aa0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67118279"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>了解 Azure 资源的拒绝分配

*拒绝分配*类似于角色分配，可将一组拒绝操作附加到特定范围内的用户、组或服务主体，以便拒绝访问。 即使角色分配向用户授予了访问权限，拒绝分配也会阻止用户执行特定的 Azure 资源操作。

本文介绍如何定义拒绝分配。

## <a name="how-deny-assignments-are-created"></a>如何拒绝创建分配时

拒绝创建和管理由 Azure 保护的资源分配。 例如，Azure 蓝图和 Azure 托管应用程序使用拒绝分配，以保护系统管理资源。 有关详细信息，请参阅[使用 Azure 蓝图资源锁保护新资源](../governance/blueprints/tutorials/protect-new-resources.md)。

## <a name="compare-role-assignments-and-deny-assignments"></a>比较角色分配，并拒绝分配

拒绝分配遵循类似的模式拒绝分配，但也有一些差异。

| 功能 | 角色分配 | 拒绝分配 |
| --- | --- | --- |
| 授予访问权限 | :heavy_check_mark: |  |
| 拒绝访问 |  | :heavy_check_mark: |
| 可以直接创建 | :heavy_check_mark: |  |
| 在作用域应用 | :heavy_check_mark: | :heavy_check_mark: |
| 排除主体 |  | :heavy_check_mark: |
| 阻止到子范围继承 |  | :heavy_check_mark: |
| 将应用于[经典订阅管理员](rbac-and-directory-admin-roles.md)分配 |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>拒绝分配属性

 拒绝分配具有以下属性：

> [!div class="mx-tableFixed"]
> | 属性 | 必选 | Type | 描述 |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | 是 | String | 拒绝分配的显示名称。 对于给定作用域，名称必须是唯一的。 |
> | `Description` | 否 | String | 拒绝分配的说明。 |
> | `Permissions.Actions` | 至少一个 Actions 或一个 DataActions | String[] | 用于指定拒绝分配阻止访问的管理操作的字符串数组。 |
> | `Permissions.NotActions` | 否 | String[] | 用于指定要从拒绝分配中排除的管理操作的字符串数组。 |
> | `Permissions.DataActions` | 至少一个 Actions 或一个 DataActions | String[] | 用于指定拒绝分配阻止访问的数据操作的字符串数组。 |
> | `Permissions.NotDataActions` | 否 | String[] | 用于指定要从拒绝分配中排除的数据操作的字符串数组。 |
> | `Scope` | 否 | String | 用于指定拒绝分配应用到的作用域的字符串。 |
> | `DoNotApplyToChildScopes` | 否 | Boolean | 指定拒绝分配是否应用到子作用域。 默认值为 false。 |
> | `Principals[i].Id` | 是 | String[] | 拒绝分配应用到的 Azure AD 主体对象 ID（用户、组、服务主体或托管主体）的数组。 设置为空 GUID `00000000-0000-0000-0000-000000000000` 将表示所有主体。 |
> | `Principals[i].Type` | 否 | String[] | Principals[i].Id 所表示的对象类型的数组。设置为 `SystemDefined` 将表示所有主体。 |
> | `ExcludePrincipals[i].Id` | 否 | String[] | 拒绝分配不会应用到的 Azure AD 主体对象 ID（用户、组、服务主体或托管主体）的数组。 |
> | `ExcludePrincipals[i].Type` | 否 | String[] | ExcludePrincipals[i].Id 所表示的对象类型的数组。 |
> | `IsSystemProtected` | 否 | Boolean | 指定此拒绝分配是否由 Azure 创建，且无法编辑或删除。 当前，所有拒绝分配受系统保护。 |

## <a name="the-all-principals-principal"></a>所有主体主体

为支持拒绝分配，名为的系统定义的主体*的所有主体*引入了。 此主体表示 Azure AD 目录中的所有用户、组、服务主体和托管标识。 如果主体 ID 是零 GUID `00000000-0000-0000-0000-000000000000` 且主体类型是 `SystemDefined`，则此主体表示所有主体。 Azure PowerShell 输出中的所有主体类似于下面所都示：

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

可与结合使用的所有主体`ExcludePrincipals`拒绝除某些用户的所有主体。 所有主体都具有以下限制：

- 只能用于 `Principals`，不能用于 `ExcludePrincipals`。
- `Principals[i].Type` 必须设置为 `SystemDefined`。

## <a name="next-steps"></a>后续步骤

* [列表拒绝使用 Azure 门户的 Azure 资源的分配](deny-assignments-portal.md)
* [了解 Azure 资源的角色定义](role-definitions.md)
