---
title: 创建使用可用性区域的 Azure 规模集
description: 了解如何创建使用可用性区域的 Azure 虚拟机规模集以增加冗余防止中断
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 08/08/2018
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: daa469bef999f33feb44983e3b5a7073b4df655e
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83197358"
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones"></a>创建使用可用性区域的虚拟机规模集

若要防止虚拟机规模集发生数据中心级故障，可以跨可用性区域创建规模集。 支持可用性区域的 Azure 区域最少具有三个单独的区域，每个都有其自己的独立电源、网络和冷却设备。 有关详细信息，请参阅[可用性区域概述](../availability-zones/az-overview.md)。

## <a name="availability-considerations"></a>可用性注意事项

从 API 版本 2017-12-01 开始，将某个规模集部署到一个或多个区域时，可以选择使用“最大分布”或“静态 5 容错域分布”进行部署**。 使用“最大分布”时，规模集会在每个区域中将 VM 分布到尽可能多的容错域。 这种分布可能会在每个区域中横跨五个以上或五个以下的容错域。 使用“静态 5 容错域分布”时，规模集会在每个区域中将 VM 分布到刚好 5 个容错域中。 如果规模集在每个区域中找不到 5 个不同的容错域来满足分配请求，则请求会失败。

建议对大多数工作负荷使用“最大分布”进行部署，因为此方法可在大多数情况下提供最佳分布****。 如果需要将副本跨不同的硬件隔离单元进行分布，建议先跨可用性区域进行分布，然后在每个区域中使用“最大分布”。

使用“最大分布”时，无论 VM 实际分布到多少个容错域，在规模集 VM 实例视图和实例元数据中都只能看到一个容错域。 每个区域中的分布是隐式的。

要使用“最大分布”，请将“platformFaultDomainCount”设置为 1****。 要使用“静态 5 容错域分布”，请将“platformFaultDomainCount”设置为 5****。 在 API 版本 2017-12-01 中，单区域规模集和跨区域规模集的“platformFaultDomainCount”默认设置为 1******。 目前，区域（非区域性）规模集仅支持静态五容错域分配。

### <a name="placement-groups"></a>放置组

部署规模集时，还可以选择为每个可用性区域部署单个[放置组](./virtual-machine-scale-sets-placement-groups.md)或多个放置组。 对于区域（非区域性）规模集，选择是在区域中使用单个放置组，或在区域中包含多个放置组。 对于大多数工作负荷，建议使用多个放置组，这样可以增大规模。 在 API 版本*2017-12-01*中，规模集默认为单区域和跨区域规模集的多个放置组，但对于区域（非区域性）规模集，它们默认为单个放置组。

> [!NOTE]
> 如果使用“最大分布”，则必须使用多个放置组。

### <a name="zone-balancing"></a>区域均衡

最后，对于跨多个区域部署的规模集，还可以选择“尽量实现区域均衡”或“严格执行区域均衡”。 如果每个区域中的 VM 数相同或与规模集的所有其他区域中的 VM 数只相差 \\ 1，则可以认为该规模集“已实现均衡”。 例如：

- 某个规模集的区域 1 中有 2 个 VM，区域 2 中有 3 个 VM，区域 3 中有 3 个 VM，则可以认为该规模集已实现均衡。 只有一个区域具有不同的 VM 计数，它只比其他区域少 1 个 VM。 
- 某个规模集的区域 1 中有 1 个 VM，区域 2 中有 3 个 VM，区域 3 中有 3 个 VM，则可以认为该规模集未实现均衡。 区域 1 比区域 2 和区域 3 少 2 个 VM。

一个可能的情况是，规模集中的 VM 已成功创建，但这些 VM 的扩展部署失败。 在确定某个规模集是否已实现均衡时，这些 VM 虽然扩展失败，但仍计在内。 例如，如果某个规模集的区域 1 中有 3 个 VM，区域 2 中有 3 个 VM，区域 3 中有 3 个 VM，则可以认为该规模集已实现均衡，即使区域 1 中的所有扩展都失败而区域 2 和 3 中的所有扩展都成功。

使用“尽量实现区域均衡”时，规模集会在维持均衡时尝试进行横向收缩和扩展。 但是，如果出于某种原因无法实现此目的（例如，如果一个区域出现故障，规模集不能在该区域中创建新的 VM），规模集就允许暂时不平衡成功地放大或缩小。在后续的横向扩展尝试中，规模集向需要更多 Vm 才能实现规模集均衡的区域添加 Vm。 同样，在后续的横向收缩尝试中，规模集会在需要减少 VM 才能实现规模集均衡的区域中删除 VM。 使用“严格执行区域均衡”时，如果在进行横向收缩或扩展尝试时会导致不均衡，规模集会放弃这样的尝试。

要使用“尽量实现区域均衡”，请将“zoneBalance”设置为 false****。 此设置是 API 版本 2017-12-01 中的默认设置**。 要使用“严格执行区域均衡”，请将“zoneBalance”设置为 true****。

## <a name="single-zone-and-zone-redundant-scale-sets"></a>单区域和区域冗余规模集

部署虚拟机规模集时，可选择在区域中使用单个可用性区域或多个区域。

在单个区域中创建规模集时，可控制要运行所有这些 VM 实例的区域，规模集仅在该区域中进行托管和自动缩放。 使用区域冗余规模集，可创建跨越多个区域的单个规模集。 创建 VM 实例时，默认其在各区域中同等均衡。 如果其中一个区域中发生中断，规模集不会自动扩大以增加容量。 最佳做法是基于 CPU 或内存使用情况配置自动缩放规则。 借助自动缩放规则，规模集能够通过在其余可操作区域中横向扩展新实例，来应对该区域中的 VM 实例损失。

若要使用可用性区域，必须在[受支持的 Azure 区域](../availability-zones/az-region.md)中创建规模集。 可以使用下列方法之一创建使用可用性区域的规模集：

- [Azure 门户](#use-the-azure-portal)
- Azure CLI
- [Azure PowerShell](#use-azure-powershell)
- [Azure 资源管理器模板](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>使用 Azure 门户

创建使用可用性区域的规模集的过程与[入门文章](quick-create-portal.md)中详述的过程相同。 选择受支持的 Azure 区域时，可在一个或多个可用性区域中创建规模集，如下面的示例所示：

![在单个可用性区域中创建规模集](media/virtual-machine-scale-sets-use-availability-zones/vmss-az-portal.png)

将在你指定的单个区域中创建规模集和支持资源，例如 Azure 负载均衡器和公共 IP 地址。

## <a name="use-the-azure-cli"></a>使用 Azure CLI

创建使用可用性区域的规模集的过程与[入门文章](quick-create-cli.md)中详述的过程相同。 若要使用可用性区域，必须在受支持的 Azure 区域中创建规模集。

在 [az vmss create](/cli/azure/vmss) 命令中添加 `--zones` 参数并指定要使用的区域（例如区域 *1*、*2* 或 *3*）。 以下示例在区域 1 中创建名为 myScaleSet 的单个区域规模集****：

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1
```

有关单区域规模集和网络资源的完整示例，请参阅[此示例 CLI 脚本](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh)

### <a name="zone-redundant-scale-set"></a>区域冗余规模集

若要创建区域冗余规模集，请使用标准 SKU 公共 IP 地址和负载均衡器**。 为增强冗余，标准 SKU 创建区域冗余网络资源**。 有关详细信息，请参阅 [Azure 负载均衡器标准概述](../load-balancer/load-balancer-standard-overview.md)和[标准负载均衡器和可用性区域](../load-balancer/load-balancer-standard-availability-zones.md)。

若要创建区域冗余规模集，可使用 `--zones` 参数指定多个区域。 下例跨区域 1、2、3 创建名为 myScaleSet 的区域冗余规模集****：

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1 2 3
```

在指定的区域中创建和配置所有规模集资源和 VM 需要几分钟时间。 有关区域冗余规模集和网络资源的完整示例，请参阅[此示例 CLI 脚本](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh)

## <a name="use-azure-powershell"></a>使用 Azure PowerShell

若要使用可用性区域，必须在受支持的 Azure 区域中创建规模集。 在 [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) 命令中添加 `-Zone` 参数并指定要使用的区域（例如区域 *1*、*2* 或 *3*）。

以下示例在美国东部 2 区域 1 中创建名为 myScaleSet 的单区域规模集******。 虚拟网络、公共 IP 地址和负载均衡器的 Azure 网络资源均会自动创建。 出现提示时，请针对规模集中的 VM 实例提供自己的所需管理凭据：

```powershell
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS2" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -Zone "1"
```

### <a name="zone-redundant-scale-set"></a>区域冗余规模集

若要创建区域冗余规模集，可使用 `-Zone` 参数指定多个区域。 以下示例跨美国东部 2 区域 1、2、3 创建名为 myScaleSet 的区域冗余规模集******。 虚拟网络、公共 IP 地址和负载均衡器的区域冗余 Azure 网络资源均会自动创建。 出现提示时，请针对规模集中的 VM 实例提供自己的所需管理凭据：

```powershell
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS2" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -Zone "1", "2", "3"
```

## <a name="use-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板

创建使用可用性区域的规模集的过程与适用于 [Linux](quick-create-template-linux.md) 或 [Windows](quick-create-template-windows.md) 的入门文章中详述的过程相同。 若要使用可用性区域，必须在受支持的 Azure 区域中创建规模集。 在模板中的 *Microsoft.Compute/virtualMachineScaleSets* 资源类型中添加 `zones` 属性并指定要使用的区域（例如区域 *1*、*2* 或 *3*）。

以下示例在美国东部 2 区域 1 中创建名为 myScaleSet 的 Linux 单区域规模集******：

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": ["1"],
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

有关单区域规模集和网络资源的完整示例，请参阅[此示例资源管理器模板](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json)

### <a name="zone-redundant-scale-set"></a>区域冗余规模集

若要创建区域冗余规模集，则在 Microsoft.Compute/virtualMachineScaleSets 资源类型的 `zones` 属性中指定多个值**。 下例跨美国东部 2 区域 1、2、3 创建名为 myScaleSet 的区域冗余规模集******：

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": [
        "1",
        "2",
        "3"
      ]
}
```

如果创建公共 IP 地址或负载均衡器，则指定 "sku": { "name": "Standard" }" 属性以创建区域冗余网络资源**。 还需要创建网络安全组和规则以允许任意流量。 有关详细信息，请参阅 [Azure 负载均衡器标准概述](../load-balancer/load-balancer-standard-overview.md)和[标准负载均衡器和可用性区域](../load-balancer/load-balancer-standard-availability-zones.md)。

有关区域冗余规模集和网络资源的完整示例，请参阅[此示例资源管理器模板](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json)

## <a name="next-steps"></a>后续步骤

现在，你已在可用性区域中创建了规模集，接下来可以学习如何[在虚拟机规模集上部署应用程序](tutorial-install-apps-cli.md)或[对虚拟机规模集使用自动缩放](tutorial-autoscale-cli.md)。
