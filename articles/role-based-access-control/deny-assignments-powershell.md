---
title: 列表拒绝使用 Azure PowerShell 的 Azure 资源的分配 |Microsoft Docs
description: 了解如何列出用户、 组、 服务主体和管理的标识已被拒绝访问在特定范围内使用 Azure PowerShell 的特定 Azure 资源操作。
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
ms.openlocfilehash: c1ea26fdb4d60262f89ea6ab0f87220a08c01e68
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67110480"
---
# <a name="list-deny-assignments-for-azure-resources-using-azure-powershell"></a>列表拒绝使用 Azure PowerShell 的 Azure 资源的分配

即使角色分配向用户授予了访问权限，[拒绝分配](deny-assignments.md)也会阻止用户执行特定的 Azure 资源操作。 本文介绍如何列出拒绝角色分配使用 Azure PowerShell。

> [!NOTE]
> 您不能直接创建您自己拒绝分配。 了解如何拒绝创建分配时，请参阅[拒绝分配](deny-assignments.md)。

## <a name="prerequisites"></a>必备组件

若要获取有关拒绝分配的信息，必须具有：

- `Microsoft.Authorization/denyAssignments/read` 权限，包括在大多数[Azure 资源的内置角色](built-in-roles.md)
- [PowerShell in Azure Cloud Shell](/azure/cloud-shell/overview)或[Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-deny-assignments"></a>列表拒绝分配

### <a name="list-all-deny-assignments"></a>列出所有拒绝的工作分配

若要列出所有拒绝的当前订阅，请使用分配[Get AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment)。

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

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>列表拒绝分配资源组范围内

若要列出所有拒绝的资源组范围内使用分配[Get AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment)。

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

### <a name="list-deny-assignments-at-a-subscription-scope"></a>列表拒绝订阅范围内分配

若要列出所有拒绝在订阅范围内，使用的分配[Get AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment)。 若要获取订阅 ID，可以找到在它**订阅**边栏选项卡在 Azure 门户或者您可以使用[Get AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription)。

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>后续步骤

- [了解 Azure 资源的拒绝分配](deny-assignments.md)
- [列表拒绝使用 Azure 门户的 Azure 资源的分配](deny-assignments-portal.md)
- [使用 REST API 列出 Azure 资源的拒绝分配](deny-assignments-rest.md)