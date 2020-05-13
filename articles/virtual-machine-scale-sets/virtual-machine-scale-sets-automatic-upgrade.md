---
title: Azure 虚拟机规模集的自动 OS 映像升级
description: 了解如何自动升级规模集中 VM 实例上的 OS 映像
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 04/14/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: c06ad5ab2688bd62fdf898950a8f64cd655a9fcc
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124969"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Azure 虚拟机规模集自动 OS 映像升级

在规模集上启用自动 OS 映像升级有助于简化更新管理，这样可以安全自动升级规模集中所有实例的 OS 磁盘。

自动 OS 升级具有以下特征：

- 配置后，映像发布者发布的最新 OS 映像将自动应用于规模集，无需用户干预。
- 在发布服务器每次发布新映像时，按滚动方式升级实例的批次。
- 与应用程序运行状况探测和[应用程序运行状况扩展](virtual-machine-scale-sets-health-extension.md)集成。
- 适用于所有 VM 大小，适用于 Windows 和 Linux 映像。
- 可以随时取消自动升级（也可以手动启动 OS 升级）。
- VM 的 OS 磁盘将被替换为使用最新映像版本创建的新 OS 磁盘。 运行配置的扩展和自定义数据脚本，同时保留永久性数据磁盘。
- 支持[扩展定序](virtual-machine-scale-sets-extension-sequencing.md)。
- 可在任意大小的规模集上启用自动 OS 映像升级。

## <a name="how-does-automatic-os-image-upgrade-work"></a>自动 OS 映像升级的工作原理是什么？

升级工作原理是将 VM 的 OS 磁盘替换为使用最新映像版本创建的新磁盘。 所配置的任何扩展和自定义数据脚本将在 OS 磁盘上运行，同时保留永久性数据磁盘。 为了尽量减少应用程序停机时间，将分批进行升级，在任一时间，规模集内正在进行升级的计算机不会超过 20%。 还可以集成 Azure 负载均衡器应用程序运行状况探测或[应用程序运行状况扩展](virtual-machine-scale-sets-health-extension.md)。 我们建议在升级过程中使用应用程序检测信号，并针对每个批次验证升级是否成功。

升级过程如下所述：
1. 在开始升级过程之前，业务流程协调程序将确保整个规模集中（出于任何原因）不正常的实例不会超过 20%。
2. 升级 orchestrator 会标识要升级的 VM 实例的批，其中任何一个批处理最多可包含一个虚拟机的最小批处理大小。
3. 所选批次 VM 实例的 OS 磁盘将替换为从最新映像创建的新 OS 磁盘。 规模集模型中的所有指定扩展和配置将应用于已升级的实例。
4. 对于配置了应用程序运行状况探测或应用程序运行状况扩展的规模集，升级最多等待 5 分钟来等待实例变得正常，然后继续升级下一批。 如果在升级后的5分钟内实例未恢复其运行状况，则默认情况下将还原该实例的以前的 OS 磁盘。
5. 升级业务流程协调程序还会跟踪升级后不正常的实例百分比。 如果升级过程中超过 20% 的已升级实例变得不正常，升级将会停止。
6. 上述过程会持续到升级了规模集中的所有实例为止。

规模集 OS 升级业务流程协调程序在升级每个批之前会检查规模集总体运行状况。 升级某一批时，可能有其他并发的计划内或计划外维护活动影响规模集实例的运行状况。 在这种情况下，如果超过 20% 的规模集实例不正常，则当前批结束时，规模集升级将会停止。

## <a name="supported-os-images"></a>支持的 OS 映像
当前仅支持特定 OS 平台映像。 自定义映像支持通过[共享映像库](shared-image-galleries.md)在自定义映像的[预览中](virtual-machine-scale-sets-automatic-upgrade.md#automatic-os-image-upgrade-for-custom-images-preview)可用。

目前支持以下平台 Sku （并且更多是定期添加的）：

| 发布者               | OS 产品/服务      |  SKU               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04-LTS          |
| Rogue Wave (OpenLogic)  | CentOS        | 7.5                |
| CoreOS                  | CoreOS        | Stable             |
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter    |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-with-Containers |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-with-Containers |
| Microsoft Corporation   | WindowsServer | Datacenter-核心-1903-with 容器-smalldisk |


## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>配置自动 OS 映像升级时的要求

- 必须将映像的 "*版本*" 属性设置为 "*最新*"。
- 对非 Service Fabric 规模集使用应用程序运行状况探测或[应用程序运行状况扩展](virtual-machine-scale-sets-health-extension.md)。
- 使用计算 API 版本2018-10-01 或更高版本。
- 确保规模集模型中指定的外部资源可用且已更新。 示例包括，VM 扩展属性中用于引导有效负载的 SAS URI、存储帐户中的有效负载、对模型中的机密的引用，等等。
- 对于使用 Windows 虚拟机的规模集，从计算 API 版本2019-03-01 开始，在规模集模型定义中， *virtualMachineProfile*属性必须设置为*false* 。 以上属性启用 VM 内升级，其中 "Windows 更新" 应用操作系统修补程序，而无需替换操作系统磁盘。 在规模集上启用自动 OS 映像升级后，不需要通过 "Windows 更新" 进行其他更新。

### <a name="service-fabric-requirements"></a>Service Fabric 要求

如果使用 Service Fabric，请确保满足以下条件：
-   Service Fabric[持久性级别](../service-fabric/service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)为银或黄金，而不是青铜。
-   规模集模型定义上的 Service Fabric 扩展必须具有 TypeHandlerVersion 1.1 或更高版本。
-   持久性级别应在 Service Fabric 群集上相同，并在规模集模型定义上 Service Fabric 扩展。

请确保 Service Fabric 群集和 Service Fabric 扩展中的持续性设置不匹配，因为不匹配将导致升级错误。 可根据[本页中所](../service-fabric/service-fabric-cluster-capacity.md#changing-durability-levels)述的指导原则修改持久性级别。


## <a name="automatic-os-image-upgrade-for-custom-images-preview"></a>自定义映像的自动 OS 映像升级（预览版）

> [!IMPORTANT]
> 自定义映像的自动 OS 映像升级目前为公共预览版。 使用下述公共预览功能需要使用选择过程。
> 此预览版不附带服务级别协议，我们不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

自动 OS 映像升级适用于通过[共享映像库](shared-image-galleries.md)部署的自定义映像。 自动 OS 映像升级不支持其他自定义映像。

若要启用预览功能，只需为每个订阅*AutomaticOSUpgradeWithGalleryImage*功能一次，如下所述。

### <a name="rest-api"></a>REST API
下面的示例说明如何为订阅启用预览：

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticOSUpgradeWithGalleryImage/register?api-version=2015-12-01`
```

功能注册可能最多需要15分钟。 检查注册状态：

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticOSUpgradeWithGalleryImage?api-version=2015-12-01`
```

为订阅注册此功能后，通过将更改传播到计算资源提供程序来完成选择加入过程。

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
使用[AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) cmdlet 为你的订阅启用预览。

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AutomaticOSUpgradeWithGalleryImage -ProviderNamespace Microsoft.Compute
```

功能注册可能最多需要15分钟。 检查注册状态：

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AutomaticOSUpgradeWithGalleryImage -ProviderNamespace Microsoft.Compute
```

为订阅注册此功能后，通过将更改传播到计算资源提供程序来完成选择加入过程。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
使用[az feature register](/cli/azure/feature#az-feature-register)为你的订阅启用预览。

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name AutomaticOSUpgradeWithGalleryImage
```

功能注册可能最多需要15分钟。 检查注册状态：

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name AutomaticOSUpgradeWithGalleryImage
```

为订阅注册此功能后，通过将更改传播到计算资源提供程序来完成选择加入过程。

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

### <a name="additional-requirements-for-custom-images"></a>自定义映像的其他要求
- 上面所述的选择过程只需针对每个订阅完成一次。 选择完成后，可以为该订阅中的任何规模集启用自动 OS 升级。
- 共享映像库可以位于任何订阅中，无需单独选择加入。 只有规模集订阅需要选择加入功能。
- 对于所有规模集，自动 OS 映像升级的配置过程是相同的，如本页的 "[配置" 部分](virtual-machine-scale-sets-automatic-upgrade.md#configure-automatic-os-image-upgrade)中所述。
- 当新版本的映像发布并[复制](shared-image-galleries.md#replication)到该规模集的区域时，配置为自动 OS 映像升级的规模集实例将升级到最新版本的共享映像库映像。 如果未将新映像复制到部署规模的区域，则规模集实例将不会升级到最新版本。 区域映像复制允许您控制规模集的新映像的推出。
- 不应从该库映像的最新版本中排除新的映像版本。 不是从库映像的最新版本中排除的映像版本不会通过自动 OS 映像升级向规模集推出。

> [!NOTE]
>规模集为自动 OS 升级配置规模集后，最长可能需要3小时才能触发第一个映像升级推出。 这是每个规模集的一次性延迟。 在30分钟内，将在规模集上触发后续映像的部署。


## <a name="configure-automatic-os-image-upgrade"></a>配置自动 OS 映像升级
若要配置自动 OS 映像升级，请确保在规模集模型定义中将 *automaticOSUpgradePolicy.enableAutomaticOSUpgrade* 属性设置为 *true*。

### <a name="rest-api"></a>REST API
以下示例说明如何在规模集模型上设置自动 OS 升级：

```
PUT or PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version=2019-12-01`
```

```json
{
  "properties": {
    "upgradePolicy": {
      "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade":  true
      }
    }
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
使用[AzVmss](/powershell/module/az.compute/update-azvmss) cmdlet 为规模集配置自动 OS 映像升级。 以下示例在名为*myResourceGroup*的资源组中配置名为*myScaleSet*的规模集的自动升级：

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -AutomaticOSUpgrade $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
使用[az vmss update](/cli/azure/vmss#az-vmss-update)为规模集配置自动 OS 映像升级。 使用 Azure CLI 2.0.47 或更高版本。 以下示例在名为*myResourceGroup*的资源组中配置名为*myScaleSet*的规模集的自动升级：

```azurecli-interactive
az vmss update --name myScaleSet --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```

## <a name="using-application-health-probes"></a>使用应用程序运行状况探测

OS 升级过程中，规模集中的 VM 实例每次只升级一批。 只有客户应用程序在升级后的 VM 实例上正常运行时，才会继续升级。 建议应用程序向规模集 OS 升级引擎提供运行状况信号。 默认情况下，在 OS 升级期间，平台根据 VM 电源状态和扩展预配状态确定升级后 VM 实例是否正常运行。 在 VM 实例的 OS 升级期间，VM 实例上的 OS 磁盘被替换为基于最新版本映像的新磁盘。 OS 升级完毕后，配置的扩展在这些 VM 上运行。 仅当已成功预配实例上的所有扩展时，才将应用程序视为正常。

可以选择为规模集配置应用程序运行状况探测，用于为平台提供关于应用程序当前状态的准确信息。 应用程序运行状况探测是用作运行状况信号的自定义负载均衡器探测。 规模集 VM 实例上运行的应用程序可以响应外部的 HTTP 或 TCP 请求，指示其是否正常运行。 若要详细了解自定义负载均衡器探测的工作原理，请参阅[了解负载均衡器探测](../load-balancer/load-balancer-custom-probe-overview.md)。 Service Fabric 规模集不支持应用程序运行状况探测。 非 Service Fabric 规模集需要负载均衡器应用程序运行状况探测或[应用程序运行状况扩展](virtual-machine-scale-sets-health-extension.md)。

如果规模集配置为使用多个放置组，则探测需使用[负载均衡器标准版](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)。

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>在规模集上将自定义负载均衡器探测配置为应用程序运行状况探测
最佳做法是为规模集运行状况显式创建负载均衡器探测。 运行状况探测可使用与现有 HTTP 探测或 TCP 探测相同的终结点，但所需的行为可能与传统负载均衡器探测不同。 例如，如果实例的负载过高，传统负载均衡器探测可能返回“不正常”，但是，这不符合自动 OS 升级过程中实际的实例运行状况。 请将探测配置为不超过两分钟的高探测速率。

可以在规模集的 networkProfile 中引用负载均衡器探测**，并可将探测与内部或公共的负载均衡器相关联，如下所示：

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
}
```

> [!NOTE]
> 使用带 Service Fabric 的自动 OS 升级时，更新域将推出新的 OS 映像，以维持 Service Fabric 中运行的服务的高可用性。 若要利用 Service Fabric 中的自动 OS 升级，必须将群集配置为使用银级持久性层或更高层级。 有关 Service Fabric 群集的持续性特征的详细信息，请参阅[此文档](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)。

### <a name="keep-credentials-up-to-date"></a>使凭据保持最新
如果规模集使用任何凭据来访问外部资源，例如，配置为对存储帐户使用 SAS 令牌的 VM 扩展，请确保凭据已更新。 如果任何凭据（包括证书和令牌）已过期，则升级将失败，第一批 Vm 将处于失败状态。

如果发生资源身份验证故障，则建议执行以下步骤来恢复 VM 和重新启用 OS 自动升级：

* 重新生成传递到扩展的令牌（或任何其他凭据）。
* 确保用于从 VM 内部访问外部实体的所有凭据均保持最新。
* 使用任何新令牌更新规模集模型中的扩展。
* 部署更新后的规模集，这将更新包括失败实例在内的所有 VM 实例。

## <a name="using-application-health-extension"></a>使用应用程序运行状况扩展
应用程序运行状况扩展部署在虚拟机规模集实例中，并从规模集实例中报告 VM 的运行状况。 可以配置扩展，以探测应用程序终结点并更新该实例上应用程序的状态。 Azure 会检查此实例状态，以确定实例是否符合升级操作的条件。

因为扩展从 VM 中报告运行状况，所以扩展可在无法利用外部探测（例如使用自定义 Azure 负载均衡器[探测](../load-balancer/load-balancer-custom-probe-overview.md)的应用程序运行状况探测）的情况下使用。

可以使用多种方法将应用程序运行状况扩展部署到规模集，如[此文](virtual-machine-scale-sets-health-extension.md#deploy-the-application-health-extension)中的示例所详述。

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>获取自动 OS 映像升级的历史记录
可以使用 Azure PowerShell、Azure CLI 2.0 或 REST API 检查在规模集上执行的最新 OS 升级的历史记录。 可以获取过去两个月内最近五次 OS 升级尝试的历史记录。

### <a name="rest-api"></a>REST API
以下示例使用[REST API](/rest/api/compute/virtualmachinescalesets/getosupgradehistory)来检查名为*myResourceGroup*的资源组中名为*myScaleSet*的规模集的状态：

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2019-12-01`
```

GET 调用将返回类似以下示例输出的属性：

```json
{
    "value": [
        {
            "properties": {
        "runningStatus": {
          "code": "RollingForward",
          "startTime": "2018-07-24T17:46:06.1248429+00:00",
          "completedTime": "2018-04-21T12:29:25.0511245+00:00"
        },
        "progress": {
          "successfulInstanceCount": 16,
          "failedInstanceCount": 0,
          "inProgressInstanceCount": 4,
          "pendingInstanceCount": 0
        },
        "startedBy": "Platform",
        "targetImageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "2016.127.20180613"
        },
        "rollbackInfo": {
          "successfullyRolledbackInstanceCount": 0,
          "failedRolledbackInstanceCount": 0
        }
      },
      "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
      "location": "westeurope"
    }
  ]
}
```

### <a name="azure-powershell"></a>Azure PowerShell
使用 [Get-AzVmss](/powershell/module/az.compute/get-azvmss) cmdlet 检查规模集的 OS 升级历史记录。 以下示例详细说明了如何查看名为*myResourceGroup*的资源组中名为*myScaleSet*的规模集的 OS 升级状态：

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
使用 [az vmss get-os-upgrade-history](/cli/azure/vmss#az-vmss-get-os-upgrade-history) 检查规模集的 OS 升级历史记录。 使用 Azure CLI 2.0.47 或更高版本。 以下示例详细说明了如何查看名为*myResourceGroup*的资源组中名为*myScaleSet*的规模集的 OS 升级状态：

```azurecli-interactive
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myScaleSet
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>如何获取最新版本的平台 OS 映像？

可以使用以下示例获取适用于受支持的 OS 自动升级 Sku 的可用映像版本：

### <a name="rest-api"></a>REST API
```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
Get-AzVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
```azurecli-interactive
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="manually-trigger-os-image-upgrades"></a>手动触发 OS 映像升级
在规模集上启用自动 OS 映像升级后，无需在规模集上手动触发映像更新。 OS 升级 orchestrator 会自动将最新的可用映像版本应用于规模集实例，而无需任何手动干预。

对于不希望等待 orchestrator 应用最新映像的特定情况，可以使用以下示例手动触发 OS 映像升级。

> [!NOTE]
> OS 映像升级的手动触发器不提供自动回滚功能。 如果实例在升级操作后未恢复其运行状况，则无法还原其先前的 OS 磁盘。

### <a name="rest-api"></a>REST API
使用[启动 OS 升级](/rest/api/compute/virtualmachinescalesetrollingupgrades/startosupgrade)API 调用开始滚动升级，将所有虚拟机规模集实例移至最新的可用映像操作系统版本。 已在运行最新可用 OS 版本的实例不会受到影响。 以下示例详细说明了如何在名为*myResourceGroup*的资源组中名为*myScaleSet*的规模集上启动滚动 OS 升级：

```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osRollingUpgrade?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
使用[AzVmssRollingOSUpgrade](/powershell/module/az.compute/Start-AzVmssRollingOSUpgrade) cmdlet 检查规模集的 OS 升级历史记录。 以下示例详细说明了如何在名为*myResourceGroup*的资源组中名为*myScaleSet*的规模集上启动滚动 OS 升级：

```azurepowershell-interactive
Start-AzVmssRollingOSUpgrade -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
使用[az vmss start 升级开始](/cli/azure/vmss/rolling-upgrade#az-vmss-rolling-upgrade-start)检查规模集的 OS 升级历史记录。 使用 Azure CLI 2.0.47 或更高版本。 以下示例详细说明了如何在名为*myResourceGroup*的资源组中名为*myScaleSet*的规模集上启动滚动 OS 升级：

```azurecli-interactive
az vmss rolling-upgrade start --resource-group "myResourceGroup" --name "myScaleSet" --subscription "subscriptionId"
```

## <a name="deploy-with-a-template"></a>使用模板进行部署

可以使用模板通过受支持映像（例如 [Ubuntu 16.04-LTS](https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json)）的自动 OS 升级来部署规模集。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

## <a name="next-steps"></a>后续步骤
有关如何将自动 OS 升级用于规模集的更多示例，请查看 [GitHub 存储库](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade)。
