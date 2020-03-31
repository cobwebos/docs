---
title: Azure AD 域服务的作用域同步 |微软文档
description: 了解如何从 Azure AD 配置范围同步到 Azure 活动目录域服务托管域
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: cc126af67a0d8627d61e595cee56f3df8973340d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613043"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services"></a>配置从 Azure AD 到 Azure 活动目录域服务的作用域同步

要提供身份验证服务，Azure 活动目录域服务 （Azure AD DS） 会同步 Azure AD 的用户和组。 在混合环境中，来自本地活动目录域服务 （AD DS） 环境的用户和组可以先使用 Azure AD 连接同步到 Azure AD，然后同步到 Azure AD DS。

默认情况下，Azure AD 目录中的所有用户和组将同步到 Azure AD DS 托管域。 如果您有特定需求，则可以选择仅同步一组定义的用户。

本文介绍如何创建使用作用域同步的 Azure AD DS 托管域，然后更改或禁用作用域用户集。

## <a name="scoped-synchronization-overview"></a>范围同步概述

默认情况下，Azure AD 目录中的所有用户和组将同步到 Azure AD DS 托管域。 如果只有少数用户需要访问托管域，则只能同步这些用户帐户。 此作用域同步基于组。 配置基于组的范围同步时，只有属于您指定的组的用户帐户同步到 Azure AD DS 托管域。

下表概述了如何使用作用域同步：

| 当前状态 | 所需状态 | 所需配置 |
| --- | --- | --- |
| 现有托管域配置为同步所有用户帐户和组。 | 您希望仅同步属于特定组的用户帐户。 | 不能从同步所有用户更改为使用作用域同步。 [删除现有的托管域](delete-aadds.md)，然后按照本文中的步骤重新创建 Azure AD DS 托管域，并配置作用域同步。 |
| 没有现有的托管域。 | 希望新建托管域，并仅同步属于特定组的用户帐户。 | 按照本文中的步骤创建 Azure AD DS 托管域，并配置作用域同步。 |
| 现有托管域配置为仅同步属于特定组的帐户。 | 您希望修改其用户应同步到 Azure AD DS 托管域的组的列表。 | 按照本文中的步骤修改作用域同步。 |

您可以使用 Azure 门户或 PowerShell 配置作用域同步设置：

| 操作 | | |
|--|--|--|
| 创建 Azure AD DS 托管域并配置作用域同步 | [Azure 门户](#enable-scoped-synchronization-using-the-azure-portal) | [电源外壳](#enable-scoped-synchronization-using-powershell) |
| 修改作用域同步 | [Azure 门户](#modify-scoped-synchronization-using-the-azure-portal) | [电源外壳](#modify-scoped-synchronization-using-powershell) |
| 禁用作用域同步 | [Azure 门户](#disable-scoped-synchronization-using-the-azure-portal) | [电源外壳](#disable-scoped-synchronization-using-powershell) |

> [!WARNING]
> 更改同步范围会导致 Azure AD DS 托管域重新同步所有数据。
> 
>  * 更改 Azure AD DS 托管域的同步范围时，将发生完全重新同步。
>  * Azure AD DS 托管域中不再需要的对象将被删除。 托管域中会新建对象。
>  * 重新同步可能需要很长时间才能完成。 同步时间取决于 Azure AD DS 托管域和 Azure AD 目录中的用户、组和组成员身份等对象的数量。 对于包含数十万个对象的大型目录，重新同步可能需要几天时间才能完成。

## <a name="enable-scoped-synchronization-using-the-azure-portal"></a>使用 Azure 门户启用作用域同步

1. 按照[本教程创建和配置 Azure AD DS 实例](tutorial-create-instance-advanced.md)。 完成同步范围以外的所有先决条件和部署步骤。
1. 选择同步步骤的 **"范围"，** 然后选择 Azure AD 组以同步到 Azure AD DS 实例。

Azure AD DS 托管域最多可能需要一个小时才能完成部署。 在 Azure 门户中，Azure AD DS 托管域的 **"概述"** 页显示整个部署阶段的当前状态。

当 Azure 门户显示 Azure AD DS 托管域已完成预配时，需要完成以下任务：

* 为虚拟网络更新 DNS 设置，以使虚拟机能够找到用于域加入或身份验证的托管域。
    * 要配置 DNS，请在门户中选择 Azure AD DS 托管域。 在 **"概述"** 窗口中，系统会提示您自动配置这些 DNS 设置。
* [将密码同步启用到 Azure AD 域服务](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds)，以便最终用户可以使用其公司凭据登录到托管域。

## <a name="modify-scoped-synchronization-using-the-azure-portal"></a>使用 Azure 门户修改作用域同步

要修改其用户应同步到 Azure AD DS 托管域的组列表，请完成以下步骤：

1. 在 Azure 门户中，搜索并选择**Azure AD 域服务**。 选择实例，如*aaddscontoso.com*。
1. 从左侧的菜单中选择 **"同步**"。
1. 要添加组，请选择 **"选择**顶部的组"，然后选择要添加的组。
1. 要从同步作用域中删除组，请从当前同步组列表中选择该组，然后选择 **"删除组**"。
1. 进行所有更改时，选择 **"保存同步范围**"。

更改同步范围会导致 Azure AD DS 托管域重新同步所有数据。 Azure AD DS 托管域中不再需要的对象将被删除，并且重新同步可能需要很长时间才能完成。

## <a name="disable-scoped-synchronization-using-the-azure-portal"></a>使用 Azure 门户禁用作用域同步

要禁用 Azure AD DS 托管域的基于组的范围同步，请完成以下步骤：

1. 在 Azure 门户中，搜索并选择**Azure AD 域服务**。 选择实例，如*aaddscontoso.com*。
1. 从左侧的菜单中选择 **"同步**"。
1. 将同步范围从 **"范围"** 设置为 **"全部**"，然后选择 **"保存同步范围**"。

更改同步范围会导致 Azure AD DS 托管域重新同步所有数据。 Azure AD DS 托管域中不再需要的对象将被删除，并且重新同步可能需要很长时间才能完成。

## <a name="powershell-script-for-scoped-synchronization"></a>用于作用域同步的 PowerShell 脚本

要使用 PowerShell 配置作用域同步，请先将以下脚本保存到名为`Select-GroupsToSync.ps1`的文件。 此脚本配置 Azure AD DS 以同步 Azure AD 中选定的组。 属于指定组的所有用户帐户都同步到 Azure AD DS 托管域。

本文的其他步骤中使用此脚本。

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

## <a name="enable-scoped-synchronization-using-powershell"></a>使用 PowerShell 启用范围同步

使用 PowerShell 完成这一组步骤。 请参阅[使用 PowerShell 启用 Azure Active Directory 域服务](powershell-create-instance.md)中的说明。 为了配置范围内同步，我们对这篇文章中的几个步骤略作修改。

1. 完成本文中的以下任务，以便使用 PowerShell 启用 Azure AD DS。 停止执行步骤以实际创建托管域。 配置创建 Azure AD DS 托管域的作用域同步。

   * [安装所需的 PowerShell 模块](powershell-create-instance.md#prerequisites)。
   * [为管理访问创建所需的服务主体和 Azure AD 组](powershell-create-instance.md#create-required-azure-ad-resources)。
   * [创建支持 Azure 资源（如虚拟网络和子网](powershell-create-instance.md#create-supporting-azure-resources)）。

1. 确定要从 Azure AD 同步的组和用户。 列出要同步到 Azure AD DS 的组的显示名称。

1. [从上一节运行脚本](#powershell-script-for-scoped-synchronization)，并使用 *-groupsToAdd*参数传递要同步的组列表。

   > [!WARNING]
   > 您必须在作用域同步的组列表中包括*AAD DC 管理员*组。 如果不包括此组，则 Azure AD DS 托管域不可用。

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

1. 现在创建 Azure AD DS 托管域并启用基于组的范围同步。 在 *-属性*参数中包括 *"筛选同步"和"已启用"。*

    设置 Azure 订阅 ID，然后为托管域提供名称，如*aaddscontoso.com*。 您可以使用[获取-Az 订阅][Get-AzSubscription]cmdlet 获取订阅 ID。 将资源组名称、虚拟网络名称和地区设置为前面步骤中使用的创建支持 Azure 资源的值：

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "aaddscontoso.com"
   $ResourceGroupName = "myResourceGroup"
   $VnetName = "myVnet"
   $AzureLocation = "westus"

   # Enable Azure AD Domain Services for the directory.
   New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
   -Location $AzureLocation `
   -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
   -Force -Verbose
   ```

创建资源并将控件返回到 PowerShell 提示符需要几分钟时间。 Azure AD DS 托管域继续在后台预配，最多可能需要一个小时才能完成部署。 在 Azure 门户中，Azure AD DS 托管域的 **"概述"** 页显示整个部署阶段的当前状态。

当 Azure 门户显示 Azure AD DS 托管域已完成预配时，需要完成以下任务：

* 为虚拟网络更新 DNS 设置，以使虚拟机能够找到用于域加入或身份验证的托管域。
    * 要配置 DNS，请在门户中选择 Azure AD DS 托管域。 在 **"概述"** 窗口中，系统会提示您自动配置这些 DNS 设置。
* 如果在支持可用性区域的区域中创建了 Azure AD DS 托管域，请创建网络安全组以限制 Azure AD DS 托管域的虚拟网络中的流量。 创建 Azure 标准负载均衡器，需要放置这些规则。 此网络安全组保护 Azure AD DS，并且托管域正常工作是必需的。
    * 要创建网络安全组和所需规则，请在门户中选择 Azure AD DS 托管域。 在 **"概述"** 窗口中，系统会提示您自动创建和配置网络安全组。
* [将密码同步启用到 Azure AD 域服务](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds)，以便最终用户可以使用其公司凭据登录到托管域。

## <a name="modify-scoped-synchronization-using-powershell"></a>使用 PowerShell 修改作用域同步

要修改其用户应同步到 Azure AD DS 托管域的组列表，请重新运行[PowerShell 脚本](#powershell-script-for-scoped-synchronization)并指定新的组列表。 在下面的示例中，要同步的组不再包括*GroupName2*，现在包括*GroupName3*。

> [!WARNING]
> 您必须在作用域同步的组列表中包括*AAD DC 管理员*组。 如果不包括此组，则 Azure AD DS 托管域不可用。

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

更改同步范围会导致 Azure AD DS 托管域重新同步所有数据。 Azure AD DS 托管域中不再需要的对象将被删除，并且重新同步可能需要很长时间才能完成。

## <a name="disable-scoped-synchronization-using-powershell"></a>使用 PowerShell 禁用作用域同步

要禁用 Azure AD DS 托管域的基于组的范围同步，请在 Azure AD DS 资源上设置 *"筛选同步"="已禁用"，* 然后更新托管域。 完成后，所有用户和组都设置为从 Azure AD 同步。

```powershell
// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

更改同步范围会导致 Azure AD DS 托管域重新同步所有数据。 Azure AD DS 托管域中不再需要的对象将被删除，并且重新同步可能需要很长时间才能完成。

## <a name="next-steps"></a>后续步骤

要了解有关同步过程的更多，请参阅了解[Azure AD 域服务中的同步](synchronization.md)。

<!-- EXTERNAL LINKS -->
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
