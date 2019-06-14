---
title: Azure Active Directory 域服务：具有作用域的同步 | Microsoft Docs
description: 配置从 Azure AD 到托管域的范围内同步
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: 332c0bc43a269734e0dc4db37228006a78e460e2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66246804"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-your-managed-domain"></a>配置从 Azure AD 到托管域的范围内同步
本文介绍了如何仅将特定用户帐户配置为从 Azure AD 目录同步到 Azure AD 域服务托管域。


## <a name="group-based-scoped-synchronization"></a>基于组的范围内同步
默认情况下，Azure AD 目录中的所有用户和组都同步到托管域。 如果只有几个用户使用托管域，可能会仅同步这些用户帐户。 为此，可使用基于组的范围内同步。 配置此类同步后，只有属于已指定组的用户帐户，才会同步到托管域。

下表有助于确定如何使用范围内同步：

| **当前状态** | **所需状态** | **所需配置** |
| --- | --- | --- |
| 现有托管域配置为，同步所有用户帐户和组。 | 希望仅将属于特定组的用户帐户同步到托管域。 | [删除现有托管域](delete-aadds.md)。 然后，按照本文中的说明操作，重新创建已配置范围内同步的托管域。 |
| 尚无托管域。 | 希望新建托管域，并仅同步属于特定组的用户帐户。 | 按照本文中的说明操作，新建已配置范围内同步的托管域。 |
| 现有托管域配置为，仅同步属于特定组的帐户。 | 希望修改包含应同步到托管域的用户的组列表。 | 按照本文中的说明操作，修改范围内同步。 |

> [!WARNING]
> **更改同步范围会导致托管域进行重新同步。**
> 
>  * 如果你更改托管域的同步范围，便会发生完全重新同步。
>  * 托管域中不再需要的对象会遭删除。 托管域中会新建对象。
>  * 重新同步可能需要很长时间才能完成，具体视托管域和 Azure AD 目录中的对象（用户、组和组成员身份）数而定。 对于包含数十万个对象的大型目录，重新同步可能需要几天时间才能完成。


## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-azure-portal"></a>使用 Azure 门户新建托管域并启用基于组的范围内同步

1. 按照[入门指南](create-instance.md)创建托管域。
2. 在 Azure AD 域服务创建向导中选择同步样式期间，选择“范围内”  。

## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-powershell"></a>使用 PowerShell 新建托管域并启用基于组的范围内同步
使用 PowerShell 完成这一组步骤。 请参阅[使用 PowerShell 启用 Azure Active Directory 域服务](powershell-create-instance.md)中的说明。 为了配置范围内同步，我们对这篇文章中的几个步骤略作修改。

若要向托管域配置基于组的范围内同步，请完成以下步骤：

1. 完成以下任务：
   * [任务 1：安装所需的 PowerShell 模块](powershell-create-instance.md#task-1-install-the-required-powershell-modules)。
   * [任务 2：在 Azure AD 目录中创建所需的服务主体](powershell-create-instance.md#task-2-create-the-required-service-principal-in-your-azure-ad-directory)。
   * [任务 3:创建和配置 AAD DC 管理员 group]powershell-create-instance.md#task-3-create-and-configure-the-aad-dc-administrators-group）。
   * [任务 4：注册 Azure AD 域服务资源提供程序](powershell-create-instance.md#task-4-register-the-azure-ad-domain-services-resource-provider)。
   * [任务 5：创建资源组](powershell-create-instance.md#task-5-create-a-resource-group)。
   * [任务 6：创建并配置虚拟网络](powershell-create-instance.md#task-6-create-and-configure-the-virtual-network)。

2. 选择要同步的组，并提供要同步到托管域的组的显示名称。

3. 将[以下部分中的脚本](scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1)保存到 ```Select-GroupsToSync.ps1``` 文件中。 运行如下脚本：

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

   > [!WARNING]
   > **请务必添加“AAD DC 管理员”组。**
   >
   > 必须在已配置范围内同步的组列表中添加“AAD DC 管理员”组。 如果未添加此组，将无法使用托管域。
   >

4. 现在，创建托管域，并为托管域启用基于组的范围内同步。 在 ```Properties``` 参数中添加属性 ```"filteredSync" = "Enabled"```。 例如，请参阅以下复制自[任务 7：预配 Azure AD 域服务托管域](powershell-create-instance.md#task-7-provision-the-azure-ad-domain-services-managed-domain)的脚本片段。

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "contoso100.com"
   $ResourceGroupName = "ContosoAaddsRg"
   $VnetName = "DomainServicesVNet_WUS"
   $AzureLocation = "westus"

   # Enable Azure AD Domain Services for the directory.
   New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
   -Location $AzureLocation `
   -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
   -ApiVersion 2017-06-01 -Force -Verbose
   ```

   > [!TIP]
   > 请务必在 ```-Properties``` 参数中添加 ```"filteredSync" = "Enabled"```，这样才能为托管域启用范围内同步。


## <a name="script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1"></a>用于选择要同步到托管域的组的脚本 (Select-GroupsToSync.ps1)
将以下脚本保存到文件 ```Select-GroupsToSync.ps1``` 中。 此脚本将 Azure AD 域服务配置为，把选定组同步到托管域。 默认情况下，属于指定组的所有用户帐户都会同步到托管域。

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


## <a name="modify-group-based-scoped-synchronization"></a>修改基于组的范围内同步
若要修改包含应同步到托管域的用户的组列表，请重新运行 [PowerShell 脚本](scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1)，并指定新的组列表。 请务必始终在此列表中指定“AAD DC 管理员”组。

> [!WARNING]
> **请务必添加“AAD DC 管理员”组。**
>
> 必须在已配置范围内同步的组列表中添加“AAD DC 管理员”组。 如果未添加此组，将无法使用托管域。
>


## <a name="disable-group-based-scoped-synchronization"></a>禁用基于组的范围内同步
若要为托管域禁用基于组的范围内同步，请运行以下 PowerShell 脚本：

```powershell
// Login to your Azure AD tenant
Login-AzAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

## <a name="next-steps"></a>后续步骤
* [了解 Azure AD 域服务中的同步](synchronization.md)
* [使用 PowerShell 启用 Azure Active Directory 域服务](powershell-create-instance.md)
