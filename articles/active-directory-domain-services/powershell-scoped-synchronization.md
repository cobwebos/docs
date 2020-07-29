---
title: 使用 PowerShell 实现 Azure AD 域服务的作用域同步 |Microsoft Docs
description: 了解如何使用 Azure AD PowerShell 将范围内的同步从 Azure AD 配置为 Azure Active Directory 域服务托管域
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/24/2020
ms.author: iainfou
ms.openlocfilehash: 197ae37b0c63b19ebe4dcdf2732169be0f357a07
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294005"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services-using-azure-ad-powershell"></a>使用 Azure AD PowerShell 将范围内的同步从 Azure AD 配置 Azure Active Directory 域服务

为了提供身份验证服务，Azure Active Directory 域服务（Azure AD DS）从 Azure AD 同步用户和组。 在混合环境中，可以先使用 Azure AD Connect 将本地 Active Directory 域服务（AD DS）环境中的用户和组同步到 Azure AD，然后同步到 Azure AD DS。

默认情况下，Azure AD 目录中的所有用户和组都将同步到 Azure AD DS 托管域。 如果有特定需求，可以改为选择只同步定义的一组用户。

本文介绍如何创建使用范围内同步的托管域，然后更改或禁用使用 Azure AD PowerShell 的作用域用户集。 也可以[使用 Azure 门户完成这些步骤][scoped-sync]。

## <a name="before-you-begin"></a>准备阶段

需有以下资源和特权才能完成本文：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请完成[创建和配置 Azure Active Directory 域服务托管域][tutorial-create-instance]的教程。
* 需要 Azure AD 租户中的*全局管理员*权限才能更改 Azure AD DS 同步作用域。

## <a name="scoped-synchronization-overview"></a>作用域同步概述

默认情况下，Azure AD 目录中的所有用户和组都将同步到托管域。 如果只有少数用户需要访问托管域，则只能同步这些用户帐户。 此作用域内同步是基于组的。 配置基于组的作用域同步时，只有属于指定组的用户帐户才会同步到托管域。 嵌套组不同步，只同步所选的特定组。

您可以在创建托管域时或部署后更改同步作用域。 你现在还可以更改现有托管域上的同步的作用域，而无需重新创建它。

若要了解有关同步过程的详细信息，请参阅[了解 Azure AD 域服务中的同步][concepts-sync]。

> [!WARNING]
> 更改同步范围会导致托管域重新同步所有数据。 请注意以下事项：
>
>  * 如果你更改托管域的同步范围，便会发生完全重新同步。
>  * 托管域中不再需要的对象将被删除。 托管域中会新建对象。

## <a name="powershell-script-for-scoped-synchronization"></a>作用域内同步的 PowerShell 脚本

若要使用 PowerShell 配置作用域同步，请首先将以下脚本保存到名为的文件 `Select-GroupsToSync.ps1` 。

此脚本将 Azure AD DS 配置为从 Azure AD 同步所选组。 属于指定组的所有用户帐户将同步到托管域。

此脚本用于本文中的其他步骤。

```powershell
param (
    [Parameter(Position = 0)]
    [String[]]$groupsToAdd
)

Connect-AzureAD
$sp = Get-AzureADServicePrincipal -Filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
$role = $sp.AppRoles | where-object -FilterScript {$_.DisplayName -eq "User"}

Write-Output "`n****************************************************************************"

Write-Output "Total group-assignments need to be added: $($groupsToAdd.Count)"
$newGroupIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($groupName in $groupsToAdd)
{
    try
    {
        $group = Get-AzureADGroup -Filter "DisplayName eq '$groupName'"
        $newGroupIds.Add($group.ObjectId)

        Write-Output "Group-Name: $groupName, Id: $($group.ObjectId)"
    }
    catch
    {
        Write-Error "Failed to find group: $groupName. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

$currentAssignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId
Write-Output "Total current group-assignments: $($currentAssignments.Count), SP-ObjectId: $($sp.ObjectId)"

$currAssignedObjectIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($assignment in $currentAssignments)
{
    Write-Output "Assignment-ObjectId: $($assignment.PrincipalId)"

    if ($newGroupIds.Contains($assignment.PrincipalId) -eq $false)
    {
        Write-Output "This assignment is not needed anymore. Removing it! Assignment-ObjectId: $($assignment.PrincipalId)"
        Remove-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -AppRoleAssignmentId $assignment.ObjectId
    }
    else
    {
        $currAssignedObjectIds.Add($assignment.PrincipalId)
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

foreach ($id in $newGroupIds)
{
    try
    {
        if ($currAssignedObjectIds.Contains($id) -eq $false)
        {
            Write-Output "Adding new group-assignment. Role-Id: $($role.Id), Group-Object-Id: $id, ResourceId: $($sp.ObjectId)"
            New-AzureADGroupAppRoleAssignment -Id $role.Id -ObjectId $id -PrincipalId $id -ResourceId $sp.ObjectId
        }
        else
        {
            Write-Output "Group-ObjectId: $id is already assigned."
        }
    }
    catch
    {
        Write-Error "Exception occurred assigning Object-ID: $id. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
```

## <a name="enable-scoped-synchronization"></a>启用作用域同步

若要为托管域启用基于组的作用域同步，请完成以下步骤：

1. 首先在 Azure AD DS 资源上设置 *"filteredSync" = "Enabled"* ，然后更新托管域。

    出现提示时，指定*全局管理员*凭据，使用[AzureAD][Connect-AzureAD] cmdlet 登录到 Azure AD 租户：

    ```powershell
    // Connect to your Azure AD tenant
    Connect-AzureAD

    // Retrieve the Azure AD DS resource.
    $DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

    // Enable group-based scoped synchronization.
    $enableScopedSync = @{"filteredSync" = "Enabled"}

    // Update the Azure AD DS resource
    Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $enableScopedSync
    ```

1. 现在，指定其用户应同步到托管域的组的列表。

    运行 `Select-GroupsToSync.ps1` 脚本并指定要同步的组的列表。在下面的示例中，要同步的组是*GroupName1*和*GroupName2*。

    > [!WARNING]
    > 对于作用域同步，必须在组列表中包含*AAD DC 管理员*组。 如果不包含此组，则托管域不可用。

    ```powershell
    .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
    ```

更改同步范围会导致托管域重新同步所有数据。 托管域中不再需要的对象将被删除，重新同步可能需要很长时间才能完成。

## <a name="modify-scoped-synchronization"></a>修改作用域同步

若要修改其用户应同步到托管域的组的列表，请运行 `Select-GroupsToSync.ps1` 脚本，并指定要同步的组的新列表。

在下面的示例中，要同步的组不再包括*GroupName2*，现在包括*GroupName3*。

> [!WARNING]
> 对于作用域同步，必须在组列表中包含*AAD DC 管理员*组。 如果不包含此组，则托管域不可用。

出现提示时，指定*全局管理员*凭据，使用[AzureAD][Connect-AzureAD] cmdlet 登录到 Azure AD 租户：

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

更改同步范围会导致托管域重新同步所有数据。 托管域中不再需要的对象将被删除，重新同步可能需要很长时间才能完成。

## <a name="disable-scoped-synchronization"></a>禁用范围内同步

若要禁用托管域的基于组的作用域同步，请在 Azure AD DS 资源上设置 *"filteredSync" = "Disabled"* ，然后更新托管域。 完成后，所有用户和组都设置为从 Azure AD 同步。

出现提示时，指定*全局管理员*凭据，使用[AzureAD][Connect-AzureAD] cmdlet 登录到 Azure AD 租户：

```powershell
// Connect to your Azure AD tenant
Connect-AzureAD

// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

更改同步范围会导致托管域重新同步所有数据。 托管域中不再需要的对象将被删除，重新同步可能需要很长时间才能完成。

## <a name="next-steps"></a>后续步骤

若要了解有关同步过程的详细信息，请参阅[了解 Azure AD 域服务中的同步](synchronization.md)。

<!-- INTERNAL LINKS -->
[scoped-sync]: scoped-synchronization.md
[concepts-sync]: synchronization.md
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md

<!-- EXTERNAL LINKS -->
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
