---
title: 创建使用 Azure Spot VM 的比例集
description: 了解如何创建使用 Spot VM 节省成本的 Azure 虚拟机缩放集。
author: cynthn
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: a7bd22032a554c83a2ea2323ffdb3ae52dfe4faf
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545937"
---
# <a name="azure-spot-vms-for-virtual-machine-scale-sets"></a>用于虚拟机缩放集的 Azure Spot VM 

在规模集中使用 Azure Spot 使您能够利用我们未使用的容量，从而显著节省成本。 在 Azure 需要返回容量的任何时间点，Azure 基础结构将驱逐 Spot 实例。 因此，Spot 实例非常适合处理批处理作业、开发/测试环境、大型计算工作负载等中断的工作负载。

可用容量的数量可能因大小、区域、一天中的时间等而异。 在规模集中部署 Spot 实例时，Azure 仅在存在可用容量但这些实例没有 SLA 时才会分配实例。 Spot 比例集部署在单个容错域中，并且不提供高可用性保证。


## <a name="pricing"></a>定价

基于区域和 SKU 的 Spot 实例定价是可变的。 有关详细信息，请参阅[Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/)和[Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/)的定价。 


使用可变定价，您可以选择使用最多 5 个小数位设置最高价格（美元 （USD）。 例如，该值`0.98765`将是每小时 0.98765 美元的最高价格。 如果将最高价格设置为`-1`，则实例不会根据价格被逐出。 实例的价格将是 Spot 的当前价格或标准实例的价格，只要有容量和配额可用，价格就更少。

## <a name="eviction-policy"></a>驱逐政策

创建 Spot 比例集时，可以将逐出策略设置为 *"取消分配*"（默认 *）或删除*。 

*"取消分配"* 策略会将被逐出的实例移动到已停止的已停止的已转移状态，从而允许您重新部署已展开的实例。 但是，不保证分配将成功。 已解除分配的 VM 将计入规模集实例配额，基础磁盘仍会产生费用。 

如果希望在 Spot 比例集中的实例在被逐出时删除它们，则可以将逐出策略设置为*删除*。 将逐出策略设置为删除后，可以通过增大规模集实例计数属性来创建新的 VM。 逐出的 VM 会连同其基础磁盘一起删除，因此可以避免存储费用。 还可以使用规模集的自动缩放功能来自动尝试补偿逐出的 VM，但是，无法保证分配成功。 建议您仅在将删除策略设置为删除时才在 Spot 比例集上使用自动缩放功能，以避免磁盘的成本并达到配额限制。 

用户可以选择加入通过[Azure 计划事件](../virtual-machines/linux/scheduled-events.md)接收 VM 内通知。 如果 VM 被逐出，您将通知您，并且在驱逐之前，您将有 30 秒的时间完成任何作业并执行关闭任务。 


## <a name="deploying-spot-vms-in-scale-sets"></a>在比例集中部署 Spot VM

要在比例集中部署 Spot VM，可以将新的*优先级*标志设置为 *"Spot*"。 缩放集中的所有 VM 都将设置为"Spot"。 要使用 Spot VM 创建比例集，请使用以下方法之一：
- [Azure 门户](#portal)
- [Azure CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Azure 资源管理器模板](#resource-manager-templates)

## <a name="portal"></a>门户

创建使用 Spot VM 的比例集的过程与[入门文章中](quick-create-portal.md)详述的过程相同。 部署比例集时，可以选择设置"Spot"标志和逐出策略：![使用 Spot VM 创建比例集](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure CLI

使用 Spot VM 创建比例集的过程与[入门文章中](quick-create-cli.md)详述的过程相同。 只需添加"-优先级点"，然后添加`--max-price`。 在此示例中，我们使用`-1`，`--max-price`以便实例不会根据价格被逐出。

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

使用 Spot VM 创建比例集的过程与[入门文章中](quick-create-powershell.md)详述的过程相同。
只需添加"-优先级点"，然后向`-max-price`[New-AzVmsConfig 提供](/powershell/module/az.compute/new-azvmssconfig)。

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

创建使用 Spot VM 的比例集的过程与[Linux](quick-create-template-linux.md)或[Windows](quick-create-template-windows.md)入门文章中详述的过程相同。 

对于 Spot 模板部署，`"apiVersion": "2019-03-01"`请使用或更高版本。 将`priority`和`evictionPolicy``billingProfile`属性添加到模板`"virtualMachineProfile":`中的部分： 

```json
                "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
```

要在实例被逐出后将其删除，请将`evictionPolicy`参数更改为`Delete`。

## <a name="faq"></a>常见问题解答

**问：** 创建后，Spot 实例是否与标准实例相同？

**答：** 是，除了 Spot VM 没有 SLA，它们可以随时被逐出。


**问：** 当您被逐出时该怎么办，但仍需要容量？

**答：** 如果您需要容量，我们建议您使用标准 VM 而不是 Spot VM。


**问：** 如何管理 Spot 的配额？

**答：** 竞价实例和标准实例将具有单独的配额池。 竞价配额将在 VM 和缩放设置实例之间共享。 有关详细信息，请参阅 [Azure 订阅和服务限制、配额与约束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)。


**问：** 我可以申请 Spot 的额外配额吗？

**答：** 是的，您将能够通过[标准配额请求流程](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)提交请求，以增加 Spot VM 的配额。


**问：** 是否可以将现有比例集转换为"点"比例集？

**答：** 否，`Spot`仅在创建时支持设置标志。


**问：** 如果我用于`low`低优先级缩放集，是否需要开始使用`Spot`？

**答：** 现在，两`low``Spot`者都工作，但您应该开始过渡到使用`Spot`。


**问：** 是否可以创建具有常规 VM 和 Spot VM 的缩放集？

**答：** 否，比例集不能支持多个优先级类型。


**问：** 是否可以将自动缩放与竞量缩放集一起使用？

**答：** 可以，您可以在"竞量"比例集中设置自动缩放规则。 如果 VM 被逐出，自动缩放可以尝试创建新的 Spot VM。 但记住，不保证会创建成功。 


**问：** 自动缩放是否同时适用于逐出策略（取消分配和删除）？

**答：** 建议您在使用自动缩放时将逐出策略设置为删除。 这是因为解除分配的实例将计入规模集的容量计数。 如果使用自动缩放，由于实例已解除分配且已收回，实例很可能会快速达到目标数。 


**问：** 哪些通道支持 Spot VM？

**答：** 有关 Spot VM 可用性，请参阅下表。

<a name="channel"></a>

| Azure 通道               | Azure Spot VM 可用性       |
|------------------------------|-----------------------------------|
| 企业协议         | 是                               |
| Pay As You Go                | 是                               |
| 云服务提供商 (CSP) | [联系您的合作伙伴](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| 优点                     | 不可用                     |
| 赞助                    | 不可用                     |
| 免费试用版                   | 不可用                     |


**问：** 我在哪里可以发布问题？

**答：** 您可以在[Q&A](https://docs.microsoft.com/answers/topics/azure-spot.html)`azure-spot`上发布和标记您的问题。 

## <a name="next-steps"></a>后续步骤

有关价格详细信息，请查看[虚拟机规模集定价页](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/)。
