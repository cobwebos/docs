---
title: 使用 Azure PowerShell 列出 Azure 拒绝分配-Azure RBAC
description: 了解如何使用 Azure PowerShell 和 Azure 基于角色的访问控制（Azure RBAC）来列出已拒绝使用特定范围内的特定 Azure 资源操作的用户、组、服务主体和管理标识。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: cd852d19b284f97995855fe06c97ea0ea69be293
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82733956"
---
# <a name="list-azure-deny-assignments-using-azure-powershell"></a>使用 Azure PowerShell 列出 Azure 拒绝分配

[Azure 拒绝分配](deny-assignments.md)会阻止用户执行特定的 Azure 资源操作，即使角色分配授予访问权限。 本文介绍如何使用 Azure PowerShell 列出拒绝分配。

> [!NOTE]
> 不能直接创建自己的拒绝分配。 有关如何创建拒绝分配的信息，请参阅[Azure 拒绝分配](deny-assignments.md)。

## <a name="prerequisites"></a>必备条件

如要获取拒绝分配的相关信息，必须具有：

- `Microsoft.Authorization/denyAssignments/read`权限包括在大多数[Azure 内置角色](built-in-roles.md)中
- Azure Cloud Shell 或[Azure PowerShell](/powershell/azure/install-az-ps) [中的 PowerShell](/azure/cloud-shell/overview)

## <a name="list-deny-assignments"></a>列出拒绝分配

### <a name="list-all-deny-assignments"></a>列出所有拒绝分配

若要列出当前订阅的所有“拒绝分配”信息，请使用 [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment)。

```azurepowershell
Get-AzDenyAssignment
```

```Example
PS C:\> Get-AzDenyAssignment

Id                      : 22222222-2222-2222-2222-222222222222
DenyAssignmentName      : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          ObjectType:   ServicePrincipal
                          }
IsSystemProtected       : True

Id                      : 33333333-3333-3333-3333-333333333333
DenyAssignmentName      : Deny assignment '33333333-3333-3333-3333-333333333333' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks/providers/Microsoft.Storage/storageAccounts/storep6vkuxmu4m4pq
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          DisplayName:  assignment-locked-storageaccount-TestingBPLocks
                          ObjectType:   ServicePrincipal
                          ObjectId:     2311a0b7-657a-4ca2-af6f-d1c33f6d2fff
                          }
IsSystemProtected       : True
```

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>列出资源组范围内的拒绝分配

若要列出资源组范围内的所有拒绝分配，请使用 [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment)。

```azurepowershell
Get-AzDenyAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzDenyAssignment -ResourceGroupName TestingBPLocks | FL DenyAssignmentName, Scope

DenyAssignmentName : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                     '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
Principals         : {
                     DisplayName:       All Principals
                     ObjectType:        SystemDefined
                     ObjectId:  00000000-0000-0000-0000-000000000000
                     }
```

### <a name="list-deny-assignments-at-a-subscription-scope"></a>列出订阅范围内的拒绝分配

若要列出订阅范围内的所有拒绝分配，请使用 [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment)。 若要获取订阅 ID，可以在 Azure 门户中的“订阅”  边栏选项卡上找到它，也可以使用 [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription)。

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>后续步骤

- [了解 Azure 拒绝分配](deny-assignments.md)
- [使用 Azure 门户列出 Azure 拒绝分配](deny-assignments-portal.md)
- [使用 REST API 列出 Azure 拒绝分配](deny-assignments-rest.md)