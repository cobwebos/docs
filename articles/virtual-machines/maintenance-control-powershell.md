---
title: 使用 PowerShell 对 Azure 虚拟机进行维护控制
description: 了解如何使用维护控制和 PowerShell 控制何时将维护应用到 Azure Vm。
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: cynthn
ms.openlocfilehash: b1c72c2f606ab653d7e3f1d81f7278571e8e4978
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82136526"
---
# <a name="control-updates-with-maintenance-control-and-azure-powershell"></a>控制包含维护控制的更新和 Azure PowerShell

维护控制允许您决定何时将更新应用于隔离的 Vm 和 Azure 专用主机。 本主题介绍用于维护控制的 Azure PowerShell 选项。 有关使用维护控制、其限制和其他管理选项的好处的详细信息，请参阅[使用维护控制管理平台更新](maintenance-control.md)。
 
## <a name="enable-the-powershell-module"></a>启用 PowerShell 模块

请确保`PowerShellGet`是最新的。

```azurepowershell-interactive
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```

如果要在本地安装，请确保以管理员身份打开 PowerShell 提示符。

还可能会要求你确认是否要从不*受信任的存储库*进行安装。 键入`Y`或选择 **"是" 以**安装模块。


## <a name="create-a-maintenance-configuration"></a>创建维护配置

创建资源组作为配置的容器。 在此示例中，在*eastus*中创建名为*myMaintenanceRG*的资源组。 如果你已有一个要使用的资源组，则可以跳过此部分，并将资源组名称替换为你在其余示例中所拥有的名称。

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

使用[AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/new-azmaintenanceconfiguration)创建维护配置。 此示例将创建一个名为*myconfig.xml*的维护配置，范围为主机。 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

使用`-MaintenanceScope host`可确保使用维护配置控制主机的更新。

如果尝试创建具有相同名称但位于不同位置的配置，则会出现错误。 配置名称在你的订阅中必须是唯一的。

可以使用[AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceconfiguration)查询可用的维护配置。

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="assign-the-configuration"></a>分配配置

使用[AzConfigurationAssignment](https://docs.microsoft.com/powershell/module/az.maintenance/new-azconfigurationassignment)将配置分配给独立 VM 或 Azure 专用主机。

### <a name="isolated-vm"></a>独立 VM

使用配置的 ID 将配置应用到 VM。 为`-ResourceType VirtualMachines`指定 vm 的名称`-ResourceName`，并为其提供虚拟机的名称和资源组。 `-ResourceGroupName` 

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

### <a name="dedicated-host"></a>专用主机

若要将配置应用到专用主机，还需要包括， `-ResourceType hosts` `-ResourceParentName`其中包含主机组的名称和。 `-ResourceParentType hostGroups` 


```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

## <a name="check-for-pending-updates"></a>检查挂起的更新

使用[AzMaintenanceUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceupdate)查看是否存在挂起的更新。 用于`-subscription`指定 VM 的 Azure 订阅（如果 VM 不同于你登录的 Azure 订阅）。

如果没有要显示的更新，则此命令不会返回任何内容。 否则，它将返回一个 PSApplyUpdate 对象：

```json
{
   "maintenanceScope": "Host",
   "impactType": "Freeze",
   "status": "Pending",
   "impactDurationInSec": 9,
   "notBefore": "2020-02-21T16:47:44.8728029Z",
   "properties": {
      "resourceId": "/subscriptions/39c6cced-4d6c-4dd5-af86-57499cd3f846/resourcegroups/Ignite2019/providers/Microsoft.Compute/virtualMachines/MCDemo3"
} 
```

### <a name="isolated-vm"></a>独立 VM

检查独立 VM 的挂起更新。 在此示例中，输出的格式为表格以便于阅读。

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```


### <a name="dedicated-host"></a>专用主机

检查专用主机的挂起更新。 在此示例中，输出的格式为表格以便于阅读。 将资源的值替换为自己的值。

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute | Format-Table
```


## <a name="apply-updates"></a>应用更新

使用[AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate)应用挂起的更新。

### <a name="isolated-vm"></a>独立 VM

创建将更新应用到隔离 VM 的请求。

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

成功时，此命令将返回一个`PSApplyUpdate`对象。 您可以使用`Get-AzApplyUpdate`命令中的 Name 特性来检查更新状态。 请参阅[检查更新状态](#check-update-status)。

### <a name="dedicated-host"></a>专用主机

将更新应用到专用主机。

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute
```

## <a name="check-update-status"></a>检查更新状态
使用[AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azapplyupdate)检查更新状态。 下面显示的命令使用`default`为`-ApplyUpdateName`参数显示最新更新的状态。 可以替换更新的名称（由[AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate)命令返回）以获取特定更新的状态。

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime 将是完成更新的时间，这是由你或平台在未使用的情况下启动的。 如果从未使用过维护控制来应用更新，它将显示默认值。

### <a name="isolated-vm"></a>独立 VM

检查特定虚拟机的更新。

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName default
```

### <a name="dedicated-host"></a>专用主机

检查专用主机的更新。

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName myUpdateName
```

## <a name="remove-a-maintenance-configuration"></a>删除维护配置

使用[AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/remove-azmaintenanceconfiguration)可删除维护配置。

```azurepowershell-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>后续步骤
若要了解详细信息，请参阅[维护和更新](maintenance-and-updates.md)。
