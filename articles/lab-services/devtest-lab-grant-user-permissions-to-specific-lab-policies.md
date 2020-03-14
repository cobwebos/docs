---
title: 向用户授予特定实验室策略的权限 |Microsoft Docs
description: 了解如何根据各个客户的需求在开发测试实验室中向用户授予特定实验室策略的权限
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 5ca829f0-eb69-40a1-ae26-03a629db1d7e
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: spelluru
ms.openlocfilehash: 9b31f3e68fbabc32f301fdcd8066a3bfbf1c2dbd
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284208"
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>向用户授予特定实验室策略的权限
## <a name="overview"></a>概述
本文说明了如何使用 PowerShell 向用户授予某个特定实验室策略的权限。 这样一来，可基于每个用户的需求应用权限。 例如，你可能想向某个特定用户授予更改 VM 策略设置的权限，但不想授予成本策略权限。

## <a name="policies-as-resources"></a>将策略作为资源
正如 [Azure 基于角色的访问控制](../role-based-access-control/role-assignments-portal.md)一文所述，可通过 RBAC 对 Azure 进行细致的访问管理。 使用 RBAC，可以在开发运营团队中对职责进行分配，仅向用户授予执行作业所需的访问权限。

在开发测试实验室中，策略是一种资源类型，可启用 RBAC 操作 **Microsoft.DevTestLab/labs/policySets/policies/** 。 每个实验室策略都是策略资源类型中的一种资源，并可作为作用域分配给 RBAC 角色。

例如，若要授予用户对**允许的 VM 大小**策略的读/写权限，你需要创建一个可与**microsoft.devtestlab/实验室/policySets/policy/** 操作一起工作的自定义角色，然后将相应的用户分配到**microsoft.devtestlab/实验室/policySets/policy/AllowedVmSizesInLab**范围内的此自定义角色。

若要了解有关 RBAC 中自定义角色的详细信息，请参阅[自定义角色的访问控制](../role-based-access-control/custom-roles.md)。

## <a name="creating-a-lab-custom-role-using-powershell"></a>使用 PowerShell 创建实验室自定义角色
若要开始，需要[安装 Azure PowerShell](/powershell/azure/install-az-ps)。 

一旦设置了 Azure PowerShell cmdlet，即可执行以下任务：

* 列出资源提供程序的所有操作
* 列出特定角色中的操作：
* 创建自定义角色

下方 PowerShell 脚本举例说明如何了执行这些任务：

    # List all the operations/actions for a resource provider.
    Get-AzProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    # List actions in a particular role.
    (Get-AzRoleDefinition "DevTest Labs User").Actions

    # Create custom role.
    $policyRoleDef = (Get-AzRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzRoleDefinition -Role $policyRoleDef)

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>使用自定义角色将某特定策略的权限分配给用户
一旦定义了自定义角色，即可将其分配给用户。 为了将自定义角色分配给用户，首先必须获取代表用户的 **ObjectId**。 为此，请使用**AzADUser** cmdlet。

在下方示例中，**SomeUser** 用户的 *ObjectId* 为 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3。

    PS C:\>Get-AzADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

获得用户的**ObjectId**和自定义角色名称后，可以使用**AzRoleAssignment** cmdlet 将该角色分配给用户：

    PS C:\>New-AzRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab

在上一示例中，使用了 **AllowedVmSizesInLab** 策略。 可以使用下方任意策略：

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>后续步骤
向用户分配特定实验室策略的权限后，需要考虑执行后续步骤：

* [保护对实验室的访问](devtest-lab-add-devtest-user.md)
* [设置实验室策略](devtest-lab-set-lab-policy.md)
* [创建实验室模板](devtest-lab-create-template.md)
* [创建适用于 VM 的自定义项目](devtest-lab-artifact-author.md)
* [将 VM 添加到实验室](devtest-lab-add-vm.md)

