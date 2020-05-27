---
title: 创建使用 Azure Spot VM 的规模集
description: 了解如何创建使用 Spot VM 的 Azure 虚拟机规模集以节省成本。
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: spot
ms.date: 03/25/2020
ms.reviewer: jagaveer
ms.custom: jagaveer
ms.openlocfilehash: 59de7a8decef807b548ff4b85f06fc1115ce110b
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125020"
---
# <a name="azure-spot-vms-for-virtual-machine-scale-sets"></a>适用于虚拟机规模集的 Azure Spot VM 

在规模集中使用 Azure Spot 可以利用未使用的容量，同时大幅降低成本。 每当 Azure 需要回收容量时，Azure 基础结构就会逐出 Spot 实例。 因此，Spot 实例非常适合可以处理服务中断的工作负荷，例如批处理作业、开发/测试环境、大型计算工作负荷，等等。

可用容量可能因大小、区域、一天内的时间等因素而异。 在规模集中部署 Spot 实例时，如果有可用的容量，则 Azure 会仅分配该实例，但这些实例没有 SLA。 Spot 规模集部署在单个容错域中，不提供高可用性保证。


## <a name="pricing"></a>定价

Spot 实例的定价是可变的，基于区域和 SKU。 有关详细信息，请参阅针对 [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) 和 [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/) 的定价。 


使用可变定价，你可以设置最高价格，以美元 (USD) 为单位，最多可使用 5 个小数位。 例如，值 `0.98765` 表示最高价格为 0.98765 美元/小时。 如果将最高价格设置为 `-1`，则不会根据价格逐出实例。 实例的价格将是 Spot 的当前价格或是标准实例的价格（两者中的较低者，前提是有可用的容量和配额）。

## <a name="eviction-policy"></a>逐出策略

创建 Spot 规模集时，可将逐出策略设置为“解除分配”（默认值）或“删除” 。 

“解除分配”策略可将逐出的实例移到已停止-已解除分配状态，以允许重新部署逐出的实例。 但是，不保证分配将成功。 已解除分配的 VM 将计入规模集实例配额，基础磁盘仍会产生费用。 

如果希望在逐出 Spot 规模集中的实例后将其删除，可以将逐出策略设置为删除。 将逐出策略设置为删除后，可以通过增大规模集实例计数属性来创建新的 VM。 逐出的 VM 会连同其基础磁盘一起删除，因此可以避免存储费用。 还可以使用规模集的自动缩放功能来自动尝试补偿逐出的 VM，但是，无法保证分配成功。 我们建议仅当逐出策略设置为删除时才对 Spot 规模集使用自动缩放功能，以免产生磁盘费用和达到配额限制。 

用户可以选择通过 [Azure Scheduled Events](../virtual-machines/linux/scheduled-events.md) 来接收 VM 内通知。 这样，系统就会在你的 VM 被逐出时向你发送通知。在逐出之前，你将有 30 秒的时间来完成任何作业并执行关闭任务。 


## <a name="deploying-spot-vms-in-scale-sets"></a>在规模集中部署 Spot VM

若要在规模集中部署 Spot VM，可将新的 *Priority* 标志设置为 *Spot*。 规模集中的所有 VM 都将设置为 Spot。 若要创建包含 Spot VM 的规模集，请使用以下方法之一：
- [Azure 门户](#portal)
- [Azure CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Azure 资源管理器模板](#resource-manager-templates)

## <a name="portal"></a>门户

创建使用 Spot VM 的规模集的过程与[入门文章](quick-create-portal.md)中详述的过程一样。 部署规模集时，可以选择设置 Spot 标志和逐出策略：![创建包含 Spot VM 的规模集](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure CLI

创建包含 Spot VM 的规模集的过程与[入门文章](quick-create-cli.md)中详述的过程一样。 只需添加“--Priority Spot”，并添加 `--max-price`。 在此示例中，对于 `--max-price`，我们使用了 `-1`，因此系统不会基于价格来逐出实例。

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 
```

## <a name="powershell"></a>PowerShell

创建包含 Spot VM 的规模集的过程与[入门文章](quick-create-powershell.md)中详述的过程一样。
只需向 [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) 中添加“-Priority Spot”并提供 `-max-price`。

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    --max-price -1
```

## <a name="resource-manager-templates"></a>资源管理器模板

创建使用 Spot VM 的规模集的过程与适用于 [Linux](quick-create-template-linux.md) 或 [Windows](quick-create-template-windows.md) 的入门文章中详述的过程相同。 

对于 Spot 模板部署，请使用 `"apiVersion": "2019-03-01"` 或更高版本。 在模板的 `"virtualMachineProfile":` 部分中添加 `priority`、`evictionPolicy` 和 `billingProfile` 属性： 

```json
                "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
```

若要在逐出实例后将其删除，请将 `evictionPolicy` 参数更改为 `Delete`。

## <a name="faq"></a>常见问题解答

**问：** 创建后，Spot 实例是否与标准实例相同？

**答:** 是，但 Spot VM 没有 SLA，可以随时将其逐出。


**问：** 当被逐出但仍然需要容量时，该怎么办？

**答:** 如果马上需要容量，建议使用标准 VM，而不要使用 Spot VM。


**问：** 如何管理 Spot 的配额？

**答:** Spot 实例和标准实例会有不同的配额池。 将在 VM 与规模集实例之间共享 Spot 配额。 有关详细信息，请参阅 [Azure 订阅和服务限制、配额与约束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)。


**问：** 是否可以为 Spot 申请额外的配额？

**答:** 是的，你可以通过[标准配额申请流程](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)提交申请，请求提高 Spot VM 的配额。


**问：** 是否可以将现有的规模集转换为 Spot 规模集？

**答:** 不可以，只有在创建时才支持设置 `Spot` 标志。


**问：** 如果我过去使用的是表示低优先级规模集的 `low`，是否需要开始改用 `Spot`？

**答:** 目前，`low` 和 `Spot` 都有效，但你应该开始改用 `Spot`。


**问：** 是否可以创建一个同时包含常规 VM 和 Spot VM 的规模集？

**答:** 不可以，一个规模集只能支持一种优先级类型。


**问：** 是否可以对 Spot 规模集使用自动缩放？

**答:** 是的，可以为 Spot 规模集设置自动缩放规则。 如果你的 VM 被逐出，则自动缩放可以尝试创建新的 Spot VM。 但记住，不保证会创建成功。 


**问：** 自动缩放是否可用于这两种收回策略（解除分配和删除）？

**答:** 建议使用自动缩放时将收回策略设置为删除。 这是因为解除分配的实例将计入规模集的容量计数。 如果使用自动缩放，由于实例已解除分配且已收回，实例很可能会快速达到目标数。 


**问：** 哪些渠道支持 Spot VM？

**答:** 若要了解 Spot VM 的可用性，请参阅下表。

<a name="channel"></a>

| Azure 渠道               | Azure Spot VM 的可用性       |
|------------------------------|-----------------------------------|
| 企业协议         | 是                               |
| 即用即付                | 是                               |
| 云服务提供商 (CSP) | [联系你的合作伙伴](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| 优点                     | 不可用                     |
| 赞助                    | 不可用                     |
| 免费试用版                   | 不可用                     |


**问：** 我可以在何处发布问题？

**答:** 你可以在[问答](https://docs.microsoft.com/answers/topics/azure-spot.html)中发布问题并使用 `azure-spot` 来标记问题。 

## <a name="next-steps"></a>后续步骤

有关价格详细信息，请查看[虚拟机规模集定价页](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/)。
