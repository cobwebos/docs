---
title: 使用 PowerShell 对 Azure 虚拟机进行维护控制
description: 了解如何使用维护控制和 PowerShell 控制何时将维护应用到 Azure Vm。
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: cynthn
ms.openlocfilehash: fc9cebd24b67e2991e89384e93479beafa889a7a
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964849"
---
# <a name="preview-control-updates-with-maintenance-control-and-azure-powershell"></a>预览：用维护控制和 Azure PowerShell 控制更新

使用维护控制管理平台更新，无需重新启动。 Azure 会频繁更新其基础结构，以提高可靠性、性能、安全性或启动新功能。 大多数更新对用户是透明的。 某些敏感工作负荷（例如游戏、媒体流式处理和财务交易）不能容忍几乎几秒钟的时间冻结或断开连接以进行维护。 维护控制提供了在35天滚动窗口中等待平台更新并将其应用的选项。 

维护控制允许您决定何时将更新应用到隔离 Vm。

借助维护控制，您可以：
- 批处理更新为一个更新包。
- 请等待35天，以应用更新。 
- 使用 Azure Functions 自动执行维护时段的平台更新。
- 维护配置跨订阅和资源组工作。 

> [!IMPORTANT]
> 维护控制目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
> 

## <a name="limitations"></a>限制

- Vm 必须位于[专用主机](./linux/dedicated-hosts.md)上，或使用[独立的 VM 大小](./linux/isolation.md)创建。
- 35天后，将自动应用更新。
- 用户必须具有**资源参与者**访问权限。


## <a name="enable-the-powershell-module"></a>启用 PowerShell 模块

请确保 `PowerShellGet` 是最新的。

```azurepowershell-interactive
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```

Az. 维护 PowerShell cmdlet 处于预览阶段，因此需要在 Cloud Shell 或本地 PowerShell 安装中安装包含 `AllowPrerelease` 参数的模块。   

```azurepowershell-interactive
Install-Module -Name Az.Maintenance -AllowPrerelease
```

如果要在本地安装，请确保以管理员身份打开 PowerShell 提示符。

还可能会要求你确认是否要从不*受信任的存储库*进行安装。 键入 `Y` 或选择 **"是" 以**安装模块。



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

使用 `-MaintenanceScope host` 确保维护配置用于控制主机的更新。

如果尝试创建具有相同名称但位于不同位置的配置，则会出现错误。 配置名称在你的订阅中必须是唯一的。

可以使用[AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceconfiguration)查询可用的维护配置。

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="assign-the-configuration"></a>分配配置

使用[AzConfigurationAssignment](https://docs.microsoft.com/powershell/module/az.maintenance/new-azconfigurationassignment)将配置分配给独立 VM 或 Azure 专用主机。

### <a name="isolated-vm"></a>独立 VM

使用配置的 ID 将配置应用到 VM。 指定 `-ResourceType VirtualMachines` 并为 `-ResourceName`提供 VM 的名称，并为 `-ResourceGroupName`提供 VM 的资源组。 

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

若要将配置应用到专用主机，还需要包含 `-ResourceType hosts`、`-ResourceParentName` 与主机组的名称一起使用，并 `-ResourceParentType hostGroups`。 


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

使用[AzMaintenanceUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceupdate)查看是否存在挂起的更新。 使用 `-subscription` 指定 VM 的 Azure 订阅（如果 VM 不同于你登录的 Azure 订阅）。

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

成功时，此命令将返回一个 `PSApplyUpdate` 对象。 您可以使用 `Get-AzApplyUpdate` 命令中的 Name 特性来检查更新状态。 请参阅[检查更新状态](#check-update-status)。

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
使用[AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azapplyupdate)检查更新状态。 下面显示的命令使用 `-ApplyUpdateName` 参数 `default` 显示最新更新的状态。 可以替换更新的名称（由[AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate)命令返回）以获取特定更新的状态。

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

```azurecli-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>后续步骤
若要了解详细信息，请参阅[维护和更新](maintenance-and-updates.md)。
