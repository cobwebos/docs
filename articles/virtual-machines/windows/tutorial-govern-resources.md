---
title: 教程 - 使用 Azure PowerShell 控制 Azure 虚拟机 | Microsoft Docs
description: 本教程介绍如何通过使用 Azure PowerShell 应用 RBAC、策略、锁和标记管理 Azure 虚拟机
services: virtual-machines-windows
documentationcenter: virtual-machines
author: tfitzmac
manager: jeconnoc
editor: tysonn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: tutorial
ms.date: 05/21/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 42f7d767162f2f403b2cf921e31a38b711a3c773
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37438222"
---
# <a name="tutorial-learn-about-windows-virtual-machine-governance-with-azure-powershell"></a>教程：了解如何使用 Azure PowerShell 控制 Windows 虚拟机

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

本文中的示例需要版本 6.0 或更高版本的 Azure PowerShell。 如果在本地运行 PowerShell，并且未使用 6.0 版或更高版本，请[更新版本](/powershell/azure/install-azurerm-ps)。 此外，还需要运行 `Connect-AzureRmAccount` 以创建与 Azure 的连接。 对于本地安装，还必须[下载 Azure AD PowerShell 模块](https://www.powershellgallery.com/packages/AzureAD/)来创建新的 Azure Active Directory 组。

## <a name="understand-scope"></a>了解范围

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

在本教程中，你将所有管理设置应用于一个资源组，以便在完成后可以轻松地删除这些设置。

让我们创建该资源组。

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

目前，资源组为空。

## <a name="role-based-access-control"></a>基于角色的访问控制

你希望确保你的组织中的用户对这些资源具有合适级别的访问权限。 你不希望向用户授予不受限的访问权限，但还需要确保他们可以执行其工作。 使用[基于角色的访问控制](../../role-based-access-control/overview.md)，你可以管理哪些用户有权在某个范围内完成特定操作。

若要创建和删除角色分配，用户必须具有 `Microsoft.Authorization/roleAssignments/*` 访问权限。 此访问权限是通过“所有者”或“用户访问”管理员角色授权的。

若要管理虚拟机解决方案，可以使用三种特定于资源的角色来进行通常所需的访问：

* [虚拟机参与者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [网络参与者](../../role-based-access-control/built-in-roles.md#network-contributor)
* [存储帐户参与者](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

通常情况下，与其向单个用户分配角色，不如为需要进行相似操作的用户[创建一个 Azure Active Directory 组](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)， 然后向该组分配相应的角色。 为了简单起见，本文创建一个没有成员的 Azure Active Directory 组。 仍然可以为该组分配一个负责某个范围的角色。 

以下示例创建一个邮件别名为 *vmDemoGroup* 且名为 *VMDemoContributors* 的 Azure Active Directory 组。 邮件别名用作组的别名。

```azurepowershell-interactive
$adgroup = New-AzureADGroup -DisplayName VMDemoContributors `
  -MailNickName vmDemoGroup `
  -MailEnabled $false `
  -SecurityEnabled $true
```

在命令提示返回后，组需要花费一段时间来在整个 Azure Active Directory 中传播。 等待 20 或 30 秒后，使用 [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) 命令将新的 Azure Active Directory 组分配到资源组的“虚拟机参与者”角色。  如果在它已传播之前运行以下命令，则会收到一个错误，指出**主体<guid>在目录中不存在**。 请尝试再次运行命令。

```azurepowershell-interactive
New-AzureRmRoleAssignment -ObjectId $adgroup.ObjectId `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

通常情况下，请对*网络参与者*和*存储帐户参与者*重复执行此过程，确保分配用户来管理已部署的资源。 在本文中，可以跳过这些步骤。

## <a name="azure-policies"></a>Azure 策略

[!INCLUDE [Resource Manager governance policy](../../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>应用策略

订阅已经有多个策略定义。 若要查看可用的策略定义，请使用 [Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition) 命令：

```azurepowershell-interactive
(Get-AzureRmPolicyDefinition).Properties | Format-Table displayName, policyType
```

可以看到现有的策略定义。 策略类型为“内置”或“自定义”。 在这些定义中查找所述条件正是你要分配的条件的定义。 在本文中，分配的策略要符合以下条件：

* 限制所有资源的位置。
* 限制虚拟机的 SKU。
* 审核不使用托管磁盘的虚拟机。

在下面的示例中，你将基于显示名称检索三个策略定义。 并且使用 [New-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment) 命令将这些定义分配到资源组。 对于某些策略，你将提供参数值来指定允许的值。

```azurepowershell-interactive
# Values to use for parameters
$locations ="eastus", "eastus2"
$skus = "Standard_DS1_v2", "Standard_E2s_v2"

# Get the resource group
$rg = Get-AzureRmResourceGroup -Name myResourceGroup

# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
$locationDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed locations"}
$skuDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed virtual machine SKUs"}
$auditDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Audit VMs that do not use managed disks"}

# Assign policy for allowed locations
New-AzureRMPolicyAssignment -Name "Set permitted locations" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $locationDefinition `
  -listOfAllowedLocations $locations

# Assign policy for allowed SKUs
New-AzureRMPolicyAssignment -Name "Set permitted VM SKUs" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $skuDefinition `
  -listOfAllowedSKUs $skus

# Assign policy for auditing unmanaged disks
New-AzureRMPolicyAssignment -Name "Audit unmanaged disks" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $auditDefinition
```

## <a name="deploy-the-virtual-machine"></a>部署虚拟机

分配角色和策略以后，即可部署解决方案。 默认大小为 Standard_DS1_v2，这是允许的 SKU 之一。 运行此步骤时，会提示输入凭据。 输入的值将配置为用于虚拟机的用户名和密码。

```azurepowershell-interactive
New-AzureRmVm -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -VirtualNetworkName "myVnet" `
     -SubnetName "mySubnet" `
     -SecurityGroupName "myNetworkSecurityGroup" `
     -PublicIpAddressName "myPublicIpAddress" `
     -OpenPorts 80,3389
```

部署完成后，可以对解决方案应用更多的管理设置。

## <a name="lock-resources"></a>锁定资源

[资源锁](../../azure-resource-manager/resource-group-lock-resources.md)可以防止组织中的用户意外删除或修改重要资源。 与基于角色的访问控制不同，资源锁对所有用户和角色应用限制。 可以将锁定级别设置为 *CanNotDelete* 或 *ReadOnly*。

若要锁定虚拟机和网络安全组，请使用 [New-AzureRmResourceLock](/powershell/module/azurerm.resources/new-azurermresourcelock) 命令：

```azurepowershell-interactive
# Add CanNotDelete lock to the VM
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup

# Add CanNotDelete lock to the network security group
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

若要测试锁，请尝试运行以下命令：

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup
```

将会显示一个错误，指出删除操作由于某个锁而无法执行。 只有在明确删除锁以后，才能删除资源组。 该步骤显示在[清理资源](#clean-up-resources)中。

## <a name="tag-resources"></a>标记资源

可以将[标记](../../azure-resource-manager/resource-group-using-tags.md)应用于 Azure 资源，以逻辑方式按类别对其进行组织。 每个标记包含一个名称和一个值。 例如，可以对生产中的所有资源应用名称“Environment”和值“Production”。

[!INCLUDE [Resource Manager governance tags Powershell](../../../includes/resource-manager-governance-tags-powershell.md)]

若要将标记应用于虚拟机，请使用 [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) 命令：

```azurepowershell-interactive
# Get the virtual machine
$r = Get-AzureRmResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines

# Apply tags to the virtual machine
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>按标记查找资源

若要通过标记名称和值查找资源，请使用 [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) 命令：

```azurepowershell-interactive
(Get-AzureRmResource -Tag @{ Environment="Test"}).Name
```

可以将返回的值用于管理任务，例如停止带有某个标记值的所有虚拟机。

```azurepowershell-interactive
Get-AzureRmResource -Tag @{ Environment="Test"} | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzureRmVM
```

### <a name="view-costs-by-tag-values"></a>按标记值查看成本

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>清理资源

在解除锁定之前，不能删除锁定的网络安全组。 若要删除锁，请使用 [Remove-AzureRmResourceLock](/powershell/module/azurerm.resources/remove-azurermresourcelock) 命令：

```azurepowershell-interactive
Remove-AzureRmResourceLock -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
Remove-AzureRmResourceLock -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

如果不再需要资源组、VM 和所有相关的资源，可以使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 命令将其删除。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本教程中，已创建自定义 VM 映像。 你已了解如何：

> [!div class="checklist"]
> * 为用户分配角色
> * 应用强制实施标准的策略
> * 使用锁保护重要资源
> * 标记用于计费和管理的资源

请转到下一教程，了解如何创建高度可用的虚拟机。

> [!div class="nextstepaction"]
> [监视虚拟机](tutorial-monitoring.md)

