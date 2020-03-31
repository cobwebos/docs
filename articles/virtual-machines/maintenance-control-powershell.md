---
title: 使用 PowerShell 的 Azure 虚拟机的维护控制
description: 了解如何使用维护控制和 PowerShell 控制何时将维护应用于 Azure VM。
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: cynthn
ms.openlocfilehash: dc47afe9cb6eca1b10f8caca7b85087023c5eadf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060140"
---
# <a name="preview-control-updates-with-maintenance-control-and-azure-powershell"></a>预览：使用维护控制和 Azure 电源外壳控制更新

使用维护控制管理不需要重新启动的平台更新。 Azure 经常更新其基础结构以提高可靠性、性能、安全性或启动新功能。 大多数更新对用户都是透明的。 某些敏感工作负载（如游戏、媒体流和金融交易）甚至无法容忍几秒钟的 VM 冻结或断开连接以进行维护。 维护控制允许您选择等待平台更新并在 35 天滚动窗口中应用它们。 

通过维护控制，您可以决定何时将更新应用于隔离的 VM。

通过维护控制，您可以：
- 批量更新到一个更新包中。
- 最多等待 35 天才能应用更新。 
- 使用 Azure 函数自动为维护窗口执行平台更新。
- 维护配置跨订阅和资源组工作。 

> [!IMPORTANT]
> 维护控制当前处于公共预览版中。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
> 

## <a name="limitations"></a>限制

- VM 必须位于[专用主机](./linux/dedicated-hosts.md)上，或者使用[隔离的 VM 大小](./linux/isolation.md)创建。
- 35 天后，将自动应用更新。
- 用户必须具有**资源参与者**访问权限。


## <a name="enable-the-powershell-module"></a>启用 PowerShell 模块

确保`PowerShellGet`是最新的。

```azurepowershell-interactive
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```

Az.维护 PowerShell cmdlet 处于预览版中，因此您需要在云壳或`AllowPrerelease`本地 PowerShell 安装中安装具有参数的模块。   

```azurepowershell-interactive
Install-Module -Name Az.Maintenance -AllowPrerelease
```

如果要在本地安装，请确保以管理员身份打开 PowerShell 提示。

可能还会要求您确认要从*不受信任的存储库*进行安装。 键入`Y`或选择 **"是全部"** 以安装模块。



## <a name="create-a-maintenance-configuration"></a>创建维护配置

将资源组创建为配置的容器。 在此示例中，在*Eastus*中创建了名为*myMaintenanceRG*的资源组。 如果已有要使用的资源组，则可以跳过此部分，并在其余示例中将资源组名称替换为您自己的资源组名称。

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

使用[新-Az 维护配置](https://docs.microsoft.com/powershell/module/az.maintenance/new-azmaintenanceconfiguration)创建维护配置。 本示例创建名为*myConfig*的维护配置，该配置范围为主机。 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

使用`-MaintenanceScope host`可确保维护配置用于控制对主机的更新。

如果尝试创建具有相同名称的配置，但在不同的位置，将出现错误。 配置名称必须对订阅是唯一的。

您可以使用[Get-Az 维护配置](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceconfiguration)查询可用的维护配置。

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="assign-the-configuration"></a>分配配置

使用["新配置分配"](https://docs.microsoft.com/powershell/module/az.maintenance/new-azconfigurationassignment)将配置分配给隔离的 VM 或 Azure 专用主机。

### <a name="isolated-vm"></a>隔离的 VM

使用配置的 ID 将配置应用于 VM。 指定`-ResourceType VirtualMachines`和提供 的`-ResourceName`VM 的名称 以及 的`-ResourceGroupName`VM 的资源组。 

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

要将配置应用于专用主机，还需要包含`-ResourceType hosts``-ResourceParentName`具有主机组名称 和`-ResourceParentType hostGroups`。 


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

使用[获取-Az 维护更新](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceupdate)以查看是否有挂起的更新。 如果`-subscription`VM 与登录的订阅不同，则用于指定 VM 的 Azure 订阅。

如果没有要显示的更新，则此命令将不返回任何内容。 否则，它将返回 PSApplyUpdate 对象：

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

### <a name="isolated-vm"></a>隔离的 VM

检查隔离 VM 的挂起更新。 在此示例中，输出被格式化为可读性表。

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```


### <a name="dedicated-host"></a>专用主机

检查专用主机的挂起更新。 在此示例中，输出被格式化为可读性表。 将资源的值替换为您自己的值。

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

使用[New-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate)应用挂起的更新。

### <a name="isolated-vm"></a>隔离的 VM

创建将更新应用于隔离 VM 的请求。

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

成功后，此命令将返回对象`PSApplyUpdate`。 可以使用`Get-AzApplyUpdate`命令中的 Name 属性检查更新状态。 请参阅[检查更新状态](#check-update-status)。

### <a name="dedicated-host"></a>专用主机

将更新应用于专用主机。

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
使用[获取-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azapplyupdate)检查更新的状态。 下面显示的命令显示了使用`default``-ApplyUpdateName`参数的最新更新的状态。 您可以替换更新的名称（由[New-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate)命令返回）来获取特定更新的状态。

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
上次更新时间将是更新完成的时间，由您启动，或者由平台启动，以防未使用自我维护窗口。 如果从未通过维护控制应用更新，则将显示默认值。

### <a name="isolated-vm"></a>隔离的 VM

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

检查对专用主机的更新。

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

使用["删除-Az 维护配置"](https://docs.microsoft.com/powershell/module/az.maintenance/remove-azmaintenanceconfiguration)删除维护配置。

```azurepowershell-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>后续步骤
要了解更多信息，请参阅[维护和更新](maintenance-and-updates.md)。
