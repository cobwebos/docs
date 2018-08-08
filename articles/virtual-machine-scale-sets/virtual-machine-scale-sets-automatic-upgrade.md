---
title: Azure 虚拟机规模集的自动 OS 升级 | Microsoft Docs
description: 了解如何自动升级规模集中 VM 实例上的 OS
services: virtual-machine-scale-sets
documentationcenter: ''
author: yeki
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/03/2018
ms.author: yeki
ms.openlocfilehash: 6b20ef98e008d9c5d984ba29eed894b1c5ec8c09
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2018
ms.locfileid: "39263242"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-upgrades"></a>Azure 虚拟机规模集自动 OS 升级

自动 OS 映像升级是 Azure 虚拟机规模集的预览功能，可将所有 VM 自动升级到最新的 OS 映像。

自动 OS 升级具有以下特征：

- 配置后，映像发布者发布的最新 OS 映像将自动应用于规模集，无需用户干预。
- 每次发布者发布新的平台映像时，以滚动方式分批升级实例。
- 与应用程序运行状况探测集成（可选，但强烈推荐使用，因为可增加安全性）。
- 适用于所有大小的 VM。
- 适用于 Windows 和 Linux 平台映像。
- 可以随时取消自动升级（也可以手动启动 OS 升级）。
- VM 的 OS 磁盘将被替换为使用最新映像版本创建的新 OS 磁盘。 运行配置的扩展和自定义数据脚本，同时保留永久性数据磁盘。


## <a name="preview-notes"></a>预览说明 
预览期间，存在以下限制和局限：

- 自动 OS 升级仅支持 [4 个 OS SKU](#supported-os-images)。 无 SLA 或保证。 建议预览期间不要在生产关键型工作负载上进行自动升级。
- 虚拟机规模集自动 OS 升级目前不支持 Azure 磁盘加密。


## <a name="register-to-use-automatic-os-upgrade"></a>注册以使用自动 OS 升级
若要使用自动 OS 升级功能，请使用 Azure Powershell 或 Azure CLI 2.0 注册预览提供程序。

### <a name="powershell"></a>PowerShell

1. 使用 [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) 注册：

     ```powershell
     Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName AutoOSUpgradePreview
     ```

2. 约 10 分钟后，注册状态报告为“已注册”。 可以使用 [Get AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature) 检查当前注册状态。 

3. 注册后，请确认 Microsoft.Compute 提供程序已注册。 下面的示例通过 [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) 使用 Azure Powershell：

     ```powershell
     Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
     ```


### <a name="cli-20"></a>CLI 2.0

1. 使用 [az feature register](/cli/azure/feature#az-feature-register) 注册：

     ```azurecli
     az feature register --name AutoOSUpgradePreview --namespace Microsoft.Compute
     ```

2. 约 10 分钟后，注册状态报告为“已注册”。 可以使用 [az feature show](/cli/azure/feature#az-feature-show) 检查当前注册状态。 
 
3. 注册后请确保 Microsoft.Compute 提供程序已注册。 下面的示例通过 [az provider register](/cli/azure/provider#az-provider-register) 使用 Azure CLI（2.0.20 或更高版本）：

     ```azurecli
     az provider register --namespace Microsoft.Compute
     ```

> [!NOTE]
> Service Fabric 群集具有其自己的应用程序运行状况概念，但不带 Service Fabric 的规模集使用负载均衡器运行状况探测监视应用程序运行状况。 
>
> ### <a name="azure-powershell"></a>Azure Powershell
>
> 1. 使用 [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) 注册提供程序功能以实现运行状况探测：
>
>      ```powershell
>      Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVmssHealthProbe
>      ```
>
> 2. 同样，约 10 分钟后，注册状态报告为“已注册”。 可以使用 [Get AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature) 检查当前注册状态
>
> 3. 注册后，请确保使用 [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) 注册 Microsoft.Network 提供程序：
>
>      ```powershell
>      Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
>      ```
>
>
> ### <a name="cli-20"></a>CLI 2.0
>
> 1. 使用 [az feature register](/cli/azure/feature#az-feature-register) 注册提供程序功能以实现运行状况探测：
>
>      ```azurecli
>      az feature register --name AllowVmssHealthProbe --namespace Microsoft.Network
>      ```
>
> 2. 同样，约 10 分钟后，注册状态报告为“已注册”。 可以使用 [az feature show](/cli/azure/feature#az-feature-show) 检查当前注册状态。 
>
> 3. 注册后，请确保使用 [az provider register](/cli/azure/provider#az-provider-register) 注册 Microsoft.Network 提供程序，如下所示：
>
>      ```azurecli
>      az provider register --namespace Microsoft.Network
>      ```

## <a name="portal-experience"></a>门户体验
执行上面的注册步骤后，可以转到 [Azure 门户](https://aka.ms/managed-compute)以启用规模集上的自动 OS 升级和查看升级进度：

![](./media/virtual-machine-scale-sets-automatic-upgrade/automatic-upgrade-portal.png)


## <a name="supported-os-images"></a>支持的 OS 映像
当前仅支持特定 OS 平台映像。 目前无法使用自己创建的自定义映像。 平台映像的版本属性必须设置为“最新”。

目前支持以下 SKU（后续会添加其他 SKU）：
    
| 发布者               | 产品/服务         |  SKU               | 版本  |
|-------------------------|---------------|--------------------|----------|
| Canonical               | UbuntuServer  | 16.04-LTS          | 最新   |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter | 最新   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    | 最新   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Smalldisk | 最新   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-with-Containers | 最新   |



## <a name="application-health-without-service-fabric"></a>不带 Service Fabric 的应用程序运行状况
> [!NOTE]
> 本部分仅适用于不带 Service Fabric 的规模集。 Service Fabric 具有其自己的应用程序运行状况概念。 使用带 Service Fabric 的自动 OS 升级时，更新域将推出新的 OS 映像，以维持 Service Fabric 中运行的服务的高可用性。 有关 Service Fabric 群集的持续性特征的详细信息，请参阅[此文档](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)。

OS 升级过程中，规模集中的 VM 实例每次只升级一批。 只有客户应用程序在升级后的 VM 实例上正常运行时，才会继续升级。 因此，应用程序必须向规模集 OS 升级引擎提供运行状况信号。 在 OS 升级期间，平台会根据 VM 电源状态和扩展预配状态确定升级后 VM 实例是否正常运行。 在 VM 实例的 OS 升级期间，VM 实例上的 OS 磁盘被替换为基于最新版本映像的新磁盘。 OS 升级完毕后，配置的扩展在这些 VM 上运行。 仅当已成功预配 VM 上的所有扩展时，才视应用程序为正常运行。 

此外，必须为规模集配置应用程序运行状况探测，以便为平台提供关于应用程序当前状态的正确信息。 应用程序运行状况探测是用作运行状况信号的自定义负载均衡器探测。 规模集 VM 实例上运行的应用程序可以响应外部的 HTTP 或 TCP 请求，指示其是否正常运行。 若要详细了解自定义负载均衡器探测的工作原理，请参阅[了解负载均衡器探测](../load-balancer/load-balancer-custom-probe-overview.md)。

如果规模集配置为使用多个放置组，则探测需使用[负载均衡器标准版](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)。

### <a name="important-keep-credentials-up-to-date"></a>重要说明：使凭据保持最新
如果规模集使用任何凭据访问外部资源，则需要确保凭据保持最新。 例如，可能会将 VM 扩展配置为使用 SAS 令牌访问存储帐户。 如果任何凭据（包括证书和令牌）过期，则升级将失败，并且第一批 VM 将处于失败状态。

如果发生资源身份验证故障，则建议执行以下步骤来恢复 VM 和重新启用 OS 自动升级：

* 重新生成传递到扩展的令牌（或任何其他凭据）。
* 确保用于从 VM 内部访问外部实体的所有凭据均保持最新。
* 使用任何新令牌更新规模集模型中的扩展。
* 部署更新后的规模集，这将更新包括失败实例在内的所有 VM 实例。 

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>在规模集上将自定义负载均衡器探测配置为应用程序运行状况探测
必须为规模集运行状况显式创建负载均衡器探测。 运行状况探测可使用与现有 HTTP 探测或 TCP 探测相同的终结点，但所需的行为可能与传统负载均衡器探测不同。 例如，传统负载均衡器探测可能会在实例上的负载过高时返回“不正常”。 而这可能并不适用于在 OS 自动升级期间判定实例运行状况。 请将探测配置为不超过两分钟的高探测速率。

可以在规模集的 networkProfile 中引用负载均衡器探测，并可将探测与内部或公共的负载均衡器相关联，如下所示：

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
```


## <a name="enforce-an-os-image-upgrade-policy-across-your-subscription"></a>在订阅中强制实施 OS 映像升级策略
为了安全升级，强烈建议强制实施升级策略。 此策略可能需要跨订阅探测应用程序运行状况。 以下 Azure 资源管理器策略将拒绝未配置自动 OS 映像升级设置的部署：

### <a name="powershell"></a>Powershell
1. 使用 [Get AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition) 获取内置 Azure 资源管理器策略定义，如下所示：

    ```powershell
    $policyDefinition = Get-AzureRmPolicyDefinition -Id "/providers/Microsoft.Authorization/policyDefinitions/465f0161-0087-490a-9ad9-ad6217f4f43a"
    ```

2. 使用 [New-AzureRmPolicyAssignment](/powershell/module/AzureRM.Resources/New-AzureRmPolicyAssignment) 将策略分配到订阅，如下所示：

    ```powershell
    New-AzureRmPolicyAssignment `
        -Name "Enforce automatic OS upgrades with app health checks" `
        -Scope "/subscriptions/<SubscriptionId>" `
        -PolicyDefinition $policyDefinition
    ```

### <a name="cli-20"></a>CLI 2.0
使用内置 Azure 资源管理器策略将策略分配到订阅：

```azurecli
az policy assignment create --display-name "Enforce automatic OS upgrades with app health checks" --name "Enforce automatic OS upgrades" --policy 465f0161-0087-490a-9ad9-ad6217f4f43a --scope "/subscriptions/<SubscriptionId>"
```

## <a name="configure-auto-updates"></a>配置自动更新
要配置自动升级，请确保在规模集模型定义中，将 automaticOSUpgrade 属性设置为 true。 可以使用 Azure PowerShell 或 Azure CLI 2.0 配置此属性。

### <a name="powershell"></a>PowerShell
以下示例使用 Azure PowerShell（4.4.1 或更高版本）为 myResourceGroup 资源组的 myVMSS 规模集配置自动升级：

```powershell
$rgname = myResourceGroup
$vmssname = myVMSS
$vmss = Get-AzureRmVMss -ResourceGroupName $rgname -VmScaleSetName $vmssname
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true
Update-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname -VirtualMachineScaleSet $vmss
```

### <a name="cli-20"></a>CLI 2.0
以下示例使用 Azure CLI（2.0.20 或更高版本）为 myResourceGroup 资源组的 myVMSS 规模集配置自动升级：

```azurecli
rgname="myResourceGroup"
vmssname="myVMSS"
az vmss update --name $vmssname --resource-group $rgname --set upgradePolicy.AutomaticOSUpgrade=true
```


## <a name="check-the-status-of-an-automatic-os-upgrade"></a>检查自动 OS 升级的状态
可以使用 Azure PowerShell、Azure CLI 2.0 或 REST API 检查规模集上最新 OS 升级的执行情况。

### <a name="powershell"></a>PowerShell
以下示例使用 Azure PowerShell（4.4.1 或更高版本）检查 myResourceGroup 资源组的 myVMSS 规模集的状态：

```powershell
Get-AzureRmVmssRollingUpgrade -ResourceGroupName myResourceGroup -VMScaleSetName myVMSS
```

### <a name="cli-20"></a>CLI 2.0
以下示例使用 Azure CLI（2.0.20 或更高版本）检查 myResourceGroup 资源组的 myVMSS 规模集的状态：

```azurecli
az vmss rolling-upgrade get-latest --resource-group myResourceGroup --name myVMSS
```

### <a name="rest-api"></a>REST API
以下示例使用 REST API 检查 myResourceGroup 资源组的 myVMSS 规模集的状态：

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/rollingUpgrades/latest?api-version=2017-03-30`
```

GET 调用将返回类似以下示例输出的属性：

```json
{
  "properties": {
    "policy": {
      "maxBatchInstancePercent": 20,
      "maxUnhealthyInstancePercent": 5,
      "maxUnhealthyUpgradedInstancePercent": 5,
      "pauseTimeBetweenBatches": "PT0S"
    },
    "runningStatus": {
      "code": "Completed",
      "startTime": "2017-06-16T03:40:14.0924763+00:00",
      "lastAction": "Start",
      "lastActionTime": "2017-06-22T08:45:43.1838042+00:00"
    },
    "progress": {
      "successfulInstanceCount": 3,
      "failedInstanceCount": 0,
      "inprogressInstanceCount": 0,
      "pendingInstanceCount": 0
    }
  },
  "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
  "location": "southcentralus"
}
```


## <a name="automatic-os-upgrade-execution"></a>自动 OS 升级执行
为了扩大应用程序运行状况探测的使用范围，规模集 OS 升级将执行以下步骤：

1. 如果超过 20% 的实例处于不正常状态，则停止升级，否则继续升级。
2. 标识下一批要升级的 VM 实例，数量最多占实例总数的 20%。
3. 升级下一批 VM 实例的 OS。
4. 如果超过 20% 的升级实例处于不正常状态，则停止升级，否则继续升级。
5. 对于不属于 Service Fabric 群集的规模集，升级将最长等待 5 分钟以便探测正常运行，然后立即继续执行下一批。 对于是 Service Fabric 群集一部分的规模集，规模集将等待 30 分钟，然后继续处理下一批。
6. 如果有剩余未升级的实例，则回到步骤 1) 对下一批升级；否则升级完成。

规模集 OS 升级引擎在升级每个批次前检查 VM 实例总体运行状况。 升级一批实例时，Azure 数据中心可能存在其他并发的计划内或计划外维护，这可能会影响 VM 的可用性。 因此，可能会临时关闭超过 20% 的实例。 在这种情况下，当前这批处理结束时，规模集升级会停止。


## <a name="deploy-with-a-template"></a>使用模板进行部署

可以使用以下模板部署使用自动升级<a href='https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json'>自动滚动升级 - Ubuntu 16.04-LTS</a> 的规模集

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>


## <a name="next-steps"></a>后续步骤
有关如何将自动 OS 升级用于规模集的更多示例，请参阅[用于预览功能的 GitHub 存储库](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade)。
