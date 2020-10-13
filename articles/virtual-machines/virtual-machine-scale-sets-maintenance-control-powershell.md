---
title: 使用 PowerShell 对 Azure 虚拟机规模集的 OS 映像升级进行维护控制
description: 了解如何使用维护控制和 PowerShell 来控制何时使用 Azure 虚拟机规模集推出自动 OS 映像升级。
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 09/11/2020
ms.author: jushiman
ms.openlocfilehash: d8acab17e9d8dfc078b46f6a279cc671a70b0a50
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974832"
---
# <a name="preview-maintenance-control-for-os-image-upgrades-on-azure-virtual-machine-scale-sets-using-powershell"></a>预览：使用 PowerShell 对 Azure 虚拟机规模集的 OS 映像升级进行维护控制

维护控制允许您决定何时将自动来宾 OS 映像升级应用到虚拟机规模集。 本主题介绍维护控制的 Azure PowerShell 选项。 有关使用维护控制的详细信息，请参阅 [Azure 虚拟机规模集的维护控制](virtual-machine-scale-sets-maintenance-control.md)。

> [!IMPORTANT]
> Azure 虚拟机规模集的 OS 映像升级的维护控制当前为公共预览版。
> 此预览版不附带服务级别协议，我们不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


## <a name="enable-the-powershell-module"></a>启用 PowerShell 模块

确保 `PowerShellGet` 为最新版本。    

```azurepowershell-interactive  
Install-Module -Name PowerShellGet -Repository PSGallery -Force 
``` 

安装 `Az.Maintenance` PowerShell 模块。     

```azurepowershell-interactive  
Install-Module -Name Az.Maintenance
``` 

如果在本地安装，请确保以管理员身份打开 PowerShell 提示符。

系统还可能会要求你确认是否要从不受信任的存储库进行安装。 键入 `Y` 或选择“全是”以安装该模块。

## <a name="connect-to-an-azure-account"></a>连接到 Azure 帐户

使用 [AzAccount](/powershell/module/az.accounts/connect-azaccount) 和 [AzAccount](/powershell/module/az.accounts/set-azcontext)连接到所需的 Azure 帐户。

```azurepowershell-interactive
Connect-AzAccount
Set-AzContext 00a000aa-0a00-0a0a-00aa-a00a000aaa00

$RGName="myMaintenanceRG"
$MaintenanceConfig="myMaintenanceConfig"
$location="eastus2"
$vmss="myMaintenanceVMSS"
```

## <a name="create-a-maintenance-configuration"></a>创建维护配置

创建一个资源组作为适用于配置的容器。 在此示例中，在*eastus2*中创建名为*myMaintenanceRG*的资源组。 如果你已有一个要使用的资源组，则可以跳过此部分。 只需将资源组名称替换为其余示例。

```azurepowershell-interactive
New-AzResourceGroup `
   -Location $location `
   -Name $RGName
```

使用 [New-AzMaintenanceConfiguration](/powershell/module/az.maintenance/new-azmaintenanceconfiguration) 创建维护配置。 此示例将创建一个名为 " *myconfig.xml* " 的维护配置，其作用域为操作系统映像。 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup $RGName `
   -Name $MaintenanceConfig `
   -MaintenanceScope OSImage `
   -Location $location `
   -StartDateTime "2020-10-01 00:00" `
   -TimeZone "Pacific Standard Time" `
   -Duration "05:00" `
   -RecurEvery "Day"
```

> [!IMPORTANT]
> 维护 **持续时间** 必须是 *5 小时* 或更长时间。 必须将 "维护 **重复周期** " 设置为 " *天*"。

使用 `-MaintenanceScope OSImage` 可确保使用维护配置来控制来宾操作系统的更新。

如果尝试创建具有相同名称但位于不同位置的配置，则会出现错误。 配置名称在资源组中必须是唯一的。

可以使用 [Get-AzMaintenanceConfiguration](/powershell/module/az.maintenance/get-azmaintenanceconfiguration) 来查询可用的维护配置。

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="associate-your-virtual-machine-scale-set-to-the-maintenance-configuration"></a>将虚拟机规模集关联到维护配置

无论维护配置的区域和订阅如何，虚拟机规模集都可以关联到任何维护配置。 通过选择维护配置，将在下一个可用维护时段自动安排规模集的新 OS 映像更新。

使用 [AzConfigurationAssignment](/powershell/module/az.maintenance/new-azconfigurationassignment) 将虚拟机规模集与维护配置相关联。

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName $RGName `
   -Location $location `
   -ResourceName $vmss `
   -ResourceType VirtualMachineScaleSets `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name`
   -MaintenanceConfigurationId $config.Id
``` 

## <a name="enable-automatic-os-upgrade"></a>启用自动 OS 升级

你可以为将要使用维护控制的每个虚拟机规模集启用自动 OS 升级。 请参阅文档 [Azure 虚拟机规模集自动 os 映像升级](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) ，以便在虚拟机规模集上启用自动 os 升级。 


## <a name="next-steps"></a>后续步骤

了解 Azure 中运行的虚拟机的维护和更新。

> [!div class="nextstepaction"]
> [维护和更新](maintenance-and-updates.md)