---
title: "向用户授予特定实验室策略的权限 |Microsoft Docs"
description: "了解如何根据各个客户的需求在开发测试实验室中向用户授予特定实验室策略的权限"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 5ca829f0-eb69-40a1-ae26-03a629db1d7e
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: tarcher
ms.openlocfilehash: fa7c1e4435106d0fc25cb7f77f195eb48705772d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>向用户授予特定实验室策略的权限
## <a name="overview"></a>概述
本文说明了如何使用 PowerShell 向用户授予某个特定实验室策略的权限。 这样一来，可基于每个用户的需求应用权限。 例如，你可能想向某个特定用户授予更改 VM 策略设置的权限，但不想授予成本策略权限。

## <a name="policies-as-resources"></a>将策略作为资源
正如 [Azure 基于角色的访问控制](../active-directory/role-based-access-control-configure.md)一文所述，可通过 RBAC 对 Azure 进行细致的访问管理。 使用 RBAC，可以在开发运营团队中对职责进行分配，仅向用户授予执行作业所需的访问权限。

在开发测试实验室中，策略是一种资源类型，可启用 RBAC 操作 **Microsoft.DevTestLab/labs/policySets/policies/**。 每个实验室策略都是策略资源类型中的一种资源，并可作为作用域分配给 RBAC 角色。

例如，为了向用户授予对 **允许的 VM 大小** 策略的读取/写入权限，需要创建一个使用 **Microsoft.DevTestLab/labs/policySets/policies/*** 操作 的自定义角色，然后将相应的用户分配给作用域 **Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab** 中的此自定义角色。

若要了解有关 RBAC 中自定义角色的详细信息，请参阅[自定义角色的访问控制](../active-directory/role-based-access-control-custom-roles.md)。

## <a name="creating-a-lab-custom-role-using-powershell"></a>使用 PowerShell 创建实验室自定义角色
开始前，需要请阅读以下文章，该文介绍了如何安装和配置 Azure PowerShell cmdlet：[https://azure.microsoft.com/blog/azps-1-0-pre](https://azure.microsoft.com/blog/azps-1-0-pre)。

一旦设置了 Azure PowerShell cmdlet，即可执行以下任务：

* 列出资源提供程序的所有操作
* 列出特定角色中的操作：
* 创建自定义角色

下方 PowerShell 脚本举例说明如何了执行这些任务：

    ‘List all the operations/actions for a resource provider.
    Get-AzureRmProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    ‘List actions in a particular role.
    (Get-AzureRmRoleDefinition "DevTest Labs User").Actions

    ‘Create custom role.
    $policyRoleDef = (Get-AzureRmRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzureRmRoleDefinition -Role $policyRoleDef)

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>使用自定义角色将某特定策略的权限分配给用户
一旦定义了自定义角色，即可将其分配给用户。 为了将自定义角色分配给用户，首先必须获取代表用户的 **ObjectId**。 若要获取，请使用 **Get-AzureRmADUser** cmdlet。

在下方示例中，*SomeUser* 用户的 **ObjectId** 为 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3。

    PS C:\>Get-AzureRmADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

一旦拥有了用户的 **ObjectId** 和自定义角色名称，即可使用 **New-AzureRmRoleAssignment** cmdlet 将该角色分配给用户：

    PS C:\>New-AzureRmRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab

在上一示例中，使用了 **AllowedVmSizesInLab** 策略。 可以使用下方任意策略：

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>后续步骤
向用户分配特定实验室策略的权限后，需要考虑执行后续步骤：

* [保护对实验室进行的访问](devtest-lab-add-devtest-user.md)。
* [设置实验室策略](devtest-lab-set-lab-policy.md)。
* [创建实验室模板](devtest-lab-create-template.md)。
* [创建适用于 VM 的自定义项目](devtest-lab-artifact-author.md)。
* [将含有项目的 VM 添加到实验室](devtest-lab-add-vm-with-artifacts.md)。

