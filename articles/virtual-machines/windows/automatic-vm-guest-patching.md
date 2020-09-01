---
title: Azure 中 Windows Vm 的自动 VM 来宾修补
description: 了解如何在 Azure 中自动修补 Windows 虚拟机
author: mayanknayar
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 08/31/2020
ms.author: manayar
ms.openlocfilehash: 720f9dfc09d27a18460037bfbbd15ae4bfc88707
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236642"
---
# <a name="preview-automatic-vm-guest-patching-for-windows-vms-in-azure"></a>预览： Azure 中 Windows Vm 的自动 VM 来宾修补

为 Windows Vm 启用自动 VM 来宾修补有助于通过安全、自动修补虚拟机来保持安全符合性，从而简化更新管理。

自动 VM 来宾修补具有以下特征：
- 分类为 " *关键* " 或 " *安全* " 的修补程序会自动下载并应用于 VM。
- 修补程序在 VM 时区的非高峰时段内应用。
- 修补业务流程由 Azure 管理，修补程序应用于可用性优先原则。
- 虚拟机运行状况由平台运行状况信号确定，用于检测修补故障。
- 适用于所有大小的 VM。

> [!IMPORTANT]
> 自动 VM 来宾修补目前为公共预览版。 使用下述公共预览功能需要使用选择过程。
> 此预览版不附带服务级别协议，我们不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="how-does-automatic-vm-guest-patching-work"></a>自动 VM 来宾修补如何工作？

如果在 VM 上启用了自动 VM 来宾修补，则可在 VM 上自动下载并应用可用的 *关键* 修补程序和 *安全* 修补程序。 如果通过 Windows 更新发布新的修补程序，则此过程将每月自动启动一次。 修补程序评估和安装是自动的，该过程包括根据需要重新启动 VM。

定期评估 VM，确定适用于该 VM 的修补程序。 在 vm 的非高峰时段，可以在 VM 上安装任何一天的修补程序。 这种自动评估可确保以尽可能早的机会发现任何缺失的修补程序。

修补程序将在每月 Windows 更新版本的每月30天内安装，遵循下面所述的可用性优先的业务流程。 仅在非高峰时间为 VM 安装修补程序，具体取决于 VM 的时区。 VM 必须在非高峰时段运行，才能自动安装修补程序。 如果 VM 在定期评估期间关闭，将自动评估 VM，并在下一次定期评估期间自动安装适用的修补程序。

若要在自己的自定义维护时段内安装包含其他修补程序分类或计划修补程序安装的修补程序，可以使用 [更新管理](tutorial-config-management.md#manage-windows-updates)。

### <a name="availability-first-patching"></a>可用性优先修补

Azure 为启用了自动 VM 来宾修补功能的所有 Vm 全局安排修补程序安装过程。 此业务流程遵循 Azure 提供的不同可用性级别上的可用性优先原则。

对于正在进行更新的一组虚拟机，Azure 平台将协调更新：

**跨区域：**
- 每月更新在全球范围内全球范围内，以防止发生全局部署失败。
- 一个阶段可以有一个或多个区域，仅当阶段中符合条件的 Vm 成功更新时，更新才会移动到下一阶段。
- 不会同时更新地域配对区域，也不能在同一区域阶段中进行更新。
- 更新的成功率是通过跟踪 VM 的运行状况更新来衡量的。 VM 运行状况通过 VM 的平台运行状况指标进行跟踪。

**在区域内：**
- 不同可用性区域中的 Vm 不会同时更新。
- 不属于可用性集的 Vm 会尽力进行批处理，以避免对订阅中的所有 Vm 进行并发更新。

**在可用性集中：**
- 公共可用性集中的所有 Vm 不会同时更新。
-   公共可用性集中的 Vm 在更新域边界内更新，不会同时更新多个更新域中的 Vm。

给定 VM 的修补程序安装日期可能会有所不同，因为特定 VM 可能会在每月修补周期的不同批次中选取。

## <a name="supported-os-images"></a>支持的 OS 映像
目前，预览版仅支持从特定 OS 平台映像创建的 Vm。 预览版目前不支持自定义映像。

目前支持以下平台 Sku (并且会定期添加更多的) ：

| 发布者               | OS 产品/服务      |  SKU               |
|-------------------------|---------------|--------------------|
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter    |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Server-Core |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-Server-核心 |

## <a name="patch-orchestration-modes"></a>修补业务流程模式
Azure 上的 Windows Vm 现在支持以下修补业务流程模式：

**AutomaticByPlatform:**
- 此模式为 Windows 虚拟机启用自动 VM 来宾修补，后续修补程序安装由 Azure 进行协调。
- 设置此模式也会在 Windows 虚拟机上禁用本机自动更新以避免重复。
- 只有使用上述受支持的操作系统平台映像创建的 Vm 才支持此模式。
- 若要使用此模式，请设置属性 `osProfile.windowsConfiguration.enableAutomaticUpdates=true` ，并  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatfom` 在 VM 模板中设置属性。

**AutomaticByOS:**
- 此模式在 Windows 虚拟机上启用自动更新，并通过自动更新在 VM 上安装修补程序。
- 如果未指定其他修补程序模式，则默认设置此模式。
- 若要使用此模式 `osProfile.windowsConfiguration.enableAutomaticUpdates=true` ，请设置属性，并  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByOS` 在 VM 模板中设置属性。

**手动：**
- 此模式在 Windows 虚拟机上禁用自动更新。
- 使用自定义修补解决方案时应设置此模式。
- 若要使用此模式 `osProfile.windowsConfiguration.enableAutomaticUpdates=false` ，请设置属性，并  `osProfile.windowsConfiguration.patchSettings.patchMode=Manual` 在 VM 模板中设置属性。

> [!NOTE]
>此属性 `osProfile.windowsConfiguration.enableAutomaticUpdates` 当前只能在首次创建 VM 时设置。 当前不支持从手动模式切换到自动模式或从自动模式切换到手动模式。 支持从 AutomaticByOS 模式切换到 AutomaticByPlatfom 模式

## <a name="requirements-for-enabling-automatic-vm-guest-patching"></a>启用自动 VM 来宾修补的要求

- 虚拟机必须安装了 [AZURE VM 代理](../extensions/agent-windows.md) 。
- Windows 更新服务必须在虚拟机上运行。
- 虚拟机必须能够访问 Windows 更新终结点。 如果你的虚拟机已配置为使用 Windows Server Update Services (WSUS) ，则相关 WSUS 服务器终结点必须是可访问的。
- 使用计算 API 版本2020-06-01 或更高版本。

若要启用预览功能，只需为每个订阅 *InGuestAutoPatchVMPreview* 功能一次，如下所述。

### <a name="rest-api"></a>REST API
下面的示例说明如何为订阅启用预览：

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
```

功能注册可能最多需要15分钟。 若要检查注册状态，请使用以下命令：

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
```

为订阅注册此功能后，通过将更改传播到计算资源提供程序来完成选择加入过程。

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
使用 [AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) cmdlet 为你的订阅启用预览。

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
```

功能注册可能最多需要15分钟。 若要检查注册状态，请使用以下命令：

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
```

为订阅注册此功能后，通过将更改传播到计算资源提供程序来完成选择加入过程。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
使用 [az feature register](/cli/azure/feature#az-feature-register) 为你的订阅启用预览。

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
```

功能注册可能最多需要15分钟。 若要检查注册状态，请使用以下命令：

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
```

为订阅注册此功能后，通过将更改传播到计算资源提供程序来完成选择加入过程。

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```
## <a name="enable-automatic-vm-guest-patching"></a>启用自动 VM 来宾修补
若要启用自动 VM 来宾修补，请确保 VM 模板定义中的属性 *osProfile* 设置为 *true* 。 此属性只能在创建 VM 时设置。

### <a name="rest-api"></a>REST API
以下示例介绍了如何启用自动 VM 来宾修补：

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-06-01`
```

```json
{
  "properties": {
    "osProfile": {
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
创建或更新 VM 时，请使用 [AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) cmdlet 启用自动 VM 来宾修补。

```azurepowershell-interactive
Set-AzVMOperatingSystem -VM $$VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate -PatchMode "AutomaticByPlatform"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
创建新的 VM 时，使用 [az vm create](/cli/azure/vm#az-vm-create) 启用自动 vm 来宾修补。 下面的示例为名为*myResourceGroup*的资源组中名为*myVM*的 VM 配置自动 VM 来宾修补：

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image Win2019Datacenter --enable-agent --enable-auto-update --patch-mode AutomaticByPlatform
```

若要修改现有 VM，请使用 [az VM update](/cli/azure/vm#az-vm-update)

```azurecli-interactive
az vm update --resource-group myResourceGroup --name myVM --set osProfile.windowsConfiguration.enableAutomaticUpdates=true osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform
```

## <a name="enablement-and-assessment"></a>启用和评估

> [!NOTE]
>在 vm 上启用自动 VM 来宾更新可能需要超过三个小时，因为在 VM 的非高峰时间完成启用。 由于评估和修补程序安装仅在非高峰时段进行，因此，VM 还必须在非高峰时段运行才能应用修补程序。

为 VM 启用自动 VM 来宾修补后， `Microsoft.CPlat.Core.WindowsPatchExtension` 会在 vm 上安装类型的 vm 扩展。 此扩展无需手动安装或更新，因为此扩展由 Azure 平台管理，作为自动 VM 来宾修补过程的一部分。

在 vm 上启用自动 VM 来宾更新可能需要超过三个小时，因为在 VM 的非高峰时间完成启用。 此扩展还会在虚拟机的非高峰时段安装和更新。 如果在完成启用之前 VM 的非高峰时间结束，则启用过程将在下一个可用的非高峰时间恢复。 如果 VM 之前已通过 AutomaticByOS 修补程序模式打开了自动 Windows 更新，则安装扩展时，会为 VM 关闭自动 Windows 更新。

若要验证是否已完成自动 VM 来宾修补并在 VM 上安装修补扩展，可以查看 VM 的实例视图。 如果启用过程已完成，将安装扩展，并且 VM 的评估结果将在下提供 `patchStatus` 。 可以通过多种方式访问 VM 的实例视图，如下所述。

### <a name="rest-api"></a>REST API

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/instanceView?api-version=2020-06-01`
```
### <a name="azure-powershell"></a>Azure PowerShell
将 [new-azvm](/powershell/module/az.compute/get-azvm) cmdlet 与参数一起使用， `-Status` 以访问 VM 的实例视图。

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" -Status
```

PowerShell 目前仅提供有关修补程序扩展的信息。 有关 `patchStatus` 的信息也将在 PowerShell 中很快可用。

### <a name="azure-cli-20"></a>Azure CLI 2.0
使用 [az vm get-help](/cli/azure/vm#az-vm-get-instance-view) 访问 vm 的实例视图。

```azurecli-interactive
az vm get-instance-view --resource-group myResourceGroup --name myVM
```

### <a name="understanding-the-patch-status-for-your-vm"></a>了解 VM 的修补程序状态

`patchStatus`实例视图响应部分提供了有关最新评估的详细信息以及最新的 VM 修补程序安装。

可在部分中查看 VM 的评估结果 `availablePatchSummary` 。 定期为启用了自动 VM 来宾修补的 VM 进行评估。 在和结果下提供评估后的可用修补程序的计数 `criticalAndSecurityPatchCount` `otherPatchCount` 。 自动 VM 来宾修补将安装在 " *关键* " 和 " *安全* 修补程序" 分类下评估的所有修补程序。 将跳过任何其他评估的修补程序。

可在部分中查看 VM 的修补程序安装结果 `lastPatchInstallationSummary` 。 本部分提供有关 VM 上最后一次修补程序安装尝试的详细信息，包括已安装、挂起、失败或跳过的修补程序的数量。 仅在 VM 的非高峰时间维护时段内安装修补程序。 "挂起" 和 "失败的修补程序" 将在下一个非高峰时段自动重试。

## <a name="on-demand-patch-assessment"></a>按需修补程序评估
如果已为 VM 启用自动 VM 来宾修补，则在 VM 的非高峰时段内会定期执行修补程序评估。 此过程是自动完成的，并且可以通过 VM 的实例视图查看最新评估的结果，如本文档前面所述。 你还可以随时为 VM 触发按需修补程序评估。 修补程序评估可能需要几分钟才能完成，最新评估的状态会在 VM 的实例视图上更新。

> [!NOTE]
>按需修补程序评估不会自动触发已安装的修补程序。 按照本文档前面所述的可用性优先修补过程操作，VM 的评估和适用的修补程序将仅在 VM 的非高峰时间安装。

### <a name="rest-api"></a>REST API
```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/assessPatches?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
使用 [AzVmPatchAssessment](/powershell/module/az.compute/invoke-azvmpatchassessment) cmdlet 为虚拟机评估可用的修补程序。

```azurepowershell-interactive
Invoke-AzVmPatchAssessment -ResourceGroupName "myResourceGroup" -VMName "myVM"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
使用 [az vm 评估-修补程序](/cli/azure/vm#az-vm-assess-patches) 来评估虚拟机的可用修补程序。

```azurecli-interactive
az vm assess-patches --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [了解有关创建和管理 Windows 虚拟机的详细信息](tutorial-manage-vm.md)
