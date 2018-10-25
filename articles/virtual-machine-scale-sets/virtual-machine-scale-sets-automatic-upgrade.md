---
title: Azure 虚拟机规模集的自动 OS 映像升级 | Microsoft Docs
description: 了解如何自动升级规模集中 VM 实例上的 OS 映像
services: virtual-machine-scale-sets
documentationcenter: ''
author: rajsqr
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: rajraj
ms.openlocfilehash: cf25d08fc9a0e1ae458d350be93af31447928ecb
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2018
ms.locfileid: "49069448"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Azure 虚拟机规模集自动 OS 映像升级

自动 OS 映像升级是 Azure 虚拟机规模集的一项功能，可将所有 VM 自动升级到最新的 OS 映像。

自动 OS 升级具有以下特征：

- 配置后，映像发布者发布的最新 OS 映像将自动应用于规模集，无需用户干预。
- 每次发布者发布新的平台映像时，以滚动方式分批升级实例。
- 集成了应用程序运行状况探测。
- 适用于所有 VM 大小，同时适用于 Windows 和 Linux 平台映像。
- 可以随时取消自动升级（也可以手动启动 OS 升级）。
- VM 的 OS 磁盘将被替换为使用最新映像版本创建的新 OS 磁盘。 运行配置的扩展和自定义数据脚本，同时保留永久性数据磁盘。
- 当前不支持 Azure 磁盘加密（预览版）。  

## <a name="how-does-automatic-os-image-upgrade-work"></a>自动 OS 映像升级的工作原理是什么？

升级工作原理是将 VM 的 OS 磁盘替换为使用最新映像版本创建的新磁盘。 将运行所配置的任何扩展和自定义数据脚本，同时保留永久性数据磁盘。 为了尽量减少应用程序停机时间，将分批对计算机进行升级，在任一时间，规模集内正在进行升级的计算机不会超过 20%。 还可以选择集成 Azure 负载均衡器应用程序运行状况探测。 强烈建议在升级过程中使用应用程序检测信号，并针对每个批次验证升级是否成功。 执行步骤如下： 

1. 在开始升级过程之前，业务流程协调程序将确保不正常运行的实例不会超过 20%。 
2. 确定要升级的 VM 实例的批，每批最多可以包括占实例总数的 20% 的实例。
3. 升级这一批 VM 实例的 OS 映像。
4. 如果客户配置了应用程序运行状况探测，则升级最多等待 5 分钟来等待探测变得正常运行，然后继续升级一下批。 
5. 如果有剩余未升级的实例，则回到步骤 1) 对下一批升级；否则升级完成。

规模集 OS 升级业务流程协调程序在升级每个批次前会检查 VM 实例总体运行状况。 升级一批实例时，Azure 数据中心可能存在其他并发的计划内或计划外维护，可能会影响 VM 的可用性。 因此，可能会临时关闭超过 20% 的实例。 在这种情况下，当前这批处理结束时，规模集升级会停止。

## <a name="supported-os-images"></a>支持的 OS 映像
当前仅支持特定 OS 平台映像。 目前无法使用自己创建的自定义映像。 

目前支持以下 SKU（将来会添加更多）：
    
| 发布者               | OS 产品/服务      |  SKU               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04-LTS *        | 
| Rogue Wave (OpenLogic)  | CentOS        | 7.5 *              | 
| CoreOS                  | CoreOS        | Stable             | 
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter | 
| Microsoft Corporation   | WindowsServer | 2016-Datacenter    | 
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-with-Containers |

* 当前正在推出对这些映像的支持，很快将会在所有 Azure 区域中提供。 

## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>配置自动 OS 映像升级时的要求

- 平台映像的版本属性必须设置为“最新”。
- 请为非 Service Fabric 规模集使用应用程序运行状况探测。
- 请确保规模集模型引用的资源可用并且保持最新。 
  例如，VM 扩展属性中用于引导有效负载的 SAS URI、存储帐户中的有效负载、对模型中的机密的引用。 

## <a name="configure-automatic-os-image-upgrade"></a>配置自动 OS 映像升级
若要配置自动 OS 映像升级，请确保在规模集模型定义中将 *automaticOSUpgradePolicy.enableAutomaticOSUpgrade* 属性设置为 *true*。 

```
PUT or PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version=2018-10-01`
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

以下示例使用 Azure CLI（2.0.47 或更高版本）为 *myResourceGroup* 资源组中的 *myVMSS* 规模集配置自动升级：

```azurecli
az vmss update --name myVMSS --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```
很快将支持通过 Azure PowerShell 配置此属性。

## <a name="using-application-health-probes"></a>使用应用程序运行状况探测 

OS 升级过程中，规模集中的 VM 实例每次只升级一批。 只有客户应用程序在升级后的 VM 实例上正常运行时，才会继续升级。 建议应用程序向规模集 OS 升级引擎提供运行状况信号。 默认情况下，在 OS 升级期间，平台根据 VM 电源状态和扩展预配状态确定升级后 VM 实例是否正常运行。 在 VM 实例的 OS 升级期间，VM 实例上的 OS 磁盘被替换为基于最新版本映像的新磁盘。 OS 升级完毕后，配置的扩展在这些 VM 上运行。 仅当已成功预配 VM 上的所有扩展时，才视应用程序为正常运行。 

可以选择为规模集配置应用程序运行状况探测，用于为平台提供关于应用程序当前状态的准确信息。 应用程序运行状况探测是用作运行状况信号的自定义负载均衡器探测。 规模集 VM 实例上运行的应用程序可以响应外部的 HTTP 或 TCP 请求，指示其是否正常运行。 若要详细了解自定义负载均衡器探测的工作原理，请参阅[了解负载均衡器探测](../load-balancer/load-balancer-custom-probe-overview.md)。 自动 OS 升级不是必须配置应用程序运行状况探测，但强烈建议使用该探测。

如果规模集配置为使用多个放置组，则探测需使用[负载均衡器标准版](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)。

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>在规模集上将自定义负载均衡器探测配置为应用程序运行状况探测
最佳做法是为规模集运行状况显式创建负载均衡器探测。 运行状况探测可使用与现有 HTTP 探测或 TCP 探测相同的终结点，但所需的行为可能与传统负载均衡器探测不同。 例如，如果实例的负载过高，传统负载均衡器探测可能返回“不正常”，然而，这可能不符合自动 OS 升级过程中实际的实例运行状况。 请将探测配置为不超过两分钟的高探测速率。

可以在规模集的 networkProfile 中引用负载均衡器探测，并可将探测与内部或公共的负载均衡器相关联，如下所示：

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
```
> [!NOTE]
> 使用带 Service Fabric 的自动 OS 升级时，更新域将推出新的 OS 映像，以维持 Service Fabric 中运行的服务的高可用性。 有关 Service Fabric 群集的持续性特征的详细信息，请参阅[此文档](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)。

### <a name="keep-credentials-up-to-date"></a>使凭据保持最新
如果规模集使用任何凭据访问外部资源，例如，如果配置了 VM 扩展，它使用 SAS 令牌访问存储帐户，则需要确保凭据保持最新。 如果任何凭据（包括证书和令牌）过期，则升级将失败，并且第一批 VM 将处于失败状态。

如果发生资源身份验证故障，则建议执行以下步骤来恢复 VM 和重新启用 OS 自动升级：

* 重新生成传递到扩展的令牌（或任何其他凭据）。
* 确保用于从 VM 内部访问外部实体的所有凭据均保持最新。
* 使用任何新令牌更新规模集模型中的扩展。
* 部署更新后的规模集，这将更新包括失败实例在内的所有 VM 实例。 

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>获取自动 OS 映像升级的历史记录 
可以使用 Azure PowerShell、Azure CLI 2.0 或 REST API 检查在规模集上执行的最新 OS 升级的历史记录。 可以获取过去两个月内最近五次 OS 升级尝试的历史记录。

### <a name="azure-powershell"></a>Azure PowerShell
以下示例使用 Azure PowerShell 检查*myResourceGroup* 资源组中的 *myVMSS* 规模集的状态：

```powershell
Get-AzureRmVmss -ResourceGroupName myResourceGroup -VMScaleSetName myVMSS -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
以下示例使用 Azure CLI（2.0.47 或更高版本）检查 *myResourceGroup* 资源组中的 *myVMSS* 规模集的状态：

```azurecli
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myVMSS
```

### <a name="rest-api"></a>REST API
以下示例使用 REST API 检查 myResourceGroup 资源组的 myVMSS 规模集的状态：

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2018-10-01`
```
请参阅适用于此 API 的文档，它位于： https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getosupgradehistory。

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

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>如何获取最新版本的平台 OS 映像？ 

可以根据以下示例获取自动 OS 升级支持的 SKU 的映像版本： 

```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2018-10-01`
```

```powershell
Get-AzureRmVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

```azurecli
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="deploy-with-a-template"></a>使用模板进行部署

可以使用以下模板部署使用自动升级<a href='https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json'>自动滚动升级 - Ubuntu 16.04-LTS</a> 的规模集

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="next-steps"></a>后续步骤
有关如何将自动 OS 升级用于规模集的更多示例，请参阅[用于预览功能的 GitHub 存储库](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade)。
