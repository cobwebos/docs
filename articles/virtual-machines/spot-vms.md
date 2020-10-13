---
title: 使用 Azure 点 Vm
description: 了解如何使用 Azure 点 Vm 节省成本。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/05/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: b8e5c6b6b755134772cc8eaea3dab3af7f5346c9
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91963357"
---
# <a name="use-spot-vms-in-azure"></a>使用 Azure 中的专色 Vm

使用污点 Vm，你可以显著节省成本。 当 Azure 需要恢复容量时，Azure 基础结构将逐出点 Vm。 因此，专色 Vm 非常适合用于处理中断的工作负荷，如批处理作业、开发/测试环境、大型计算工作负荷等。

可用容量可能因大小、区域、一天内的时间等因素而异。 部署专色 Vm 时，如果有可用的容量，Azure 将分配 Vm，但这些 Vm 没有 SLA。 点 VM 不提供高可用性保证。 当 Azure 需要恢复容量时，Azure 基础结构会在30秒的时间内逐出点 Vm。 


## <a name="eviction-policy"></a>逐出策略

Vm 可根据容量或设置的最大价格进行逐出。 创建点 VM 时，可以将逐出策略设置为 *释放* (默认) 或 *删除*。 

*解除分配*策略会将 VM 移到停止解除分配状态，以便以后重新部署它。 但是，不保证分配将成功。 已释放的 Vm 将计入你的配额，并将对基础磁盘的存储成本进行收费。 

如果希望在逐出 VM 时删除 VM，可以将逐出策略设置为 " *删除*"。 逐出的 Vm 将连同它们的基础磁盘一起删除，因此你不会继续为存储付费。 

你可以选择通过 [Azure Scheduled Events](./linux/scheduled-events.md)接收 VM 内通知。 这样，系统就会在你的 VM 被逐出时向你发送通知。在逐出之前，你将有 30 秒的时间来完成任何作业并执行关闭任务。 


| 选项 | 业务成效 |
|--------|---------|
| 最大价格设置为 >= 当前价格。 | 如果容量和配额可用，则部署 VM。 |
| 最大价格设置为 < 当前价格。 | VM 未部署。 您将收到一条错误消息，指出最大价格需要 >= 当前价格。 |
| 如果最大价格 >= 当前价格，则重新启动停止/解除分配 VM | 如果有容量和配额，则部署 VM。 |
| 如果最大价格 < 当前价格，则重新启动停止/解除分配 VM | 您将收到一条错误消息，指出最大价格需要 >= 当前价格。 | 
| VM 的价格已开始，现在 > 最大价格。 | VM 将被逐出。 在实际逐出之前，会收到30秒通知。 | 
| 逐出后，VM 的价格会退回 < 最大价格。 | VM 不会自动重新启动。 你可以自行重新启动 VM，并将按当前价格收费。 |
| 如果最大价格设置为 `-1` | 出于定价原因，不会逐出 VM。 最大价格为当前价格，最高可达标准 Vm 的价格。 永远不会按标准价格收费。| 
| 更改最大价格 | 需要解除分配 VM 以更改最大价格。 解除分配 VM，设置新的最大价格，并更新 VM。 |


## <a name="limitations"></a>限制

对于污点 Vm，不支持以下 VM 大小：
 - B 系列
 - 任意大小 (促销版本，如 Dv2、NV、NC、H 促销大小) 

污点 Vm 可以部署到任何区域，但 Microsoft Azure 中国世纪互联。

<a name="channel"></a>

目前支持以下 [产品/服务类型](https://azure.microsoft.com/support/legal/offer-details/) ：

-   企业协议
-   即用即付
-   赞助
- 对于云服务提供商 (CSP) ，请联系你的合作伙伴


## <a name="pricing"></a>定价

基于区域和 SKU，污点 Vm 的定价是可变的。 有关详细信息，请参阅适用于 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 和 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的 VM 定价。 

你还可以使用 [Azure 零售价格 API](/rest/api/cost-management/retail-prices/azure-retail-prices) 查询定价信息，以查询有关专色定价的信息。 `meterName`和 `skuName` 都包含 `Spot` 。

使用可变定价，你可以设置最高价格，以美元 (USD) 为单位，最多可使用 5 个小数位。 例如，值 `0.98765` 表示最高价格为 0.98765 美元/小时。 如果将最大价格设置为 `-1` ，则不会根据价格收回 VM。 VM 的价格将是当前的价格价格或标准 VM 的价格，只要容量和配额可用，此价格就越小。

## <a name="pricing-and-eviction-history"></a>定价和逐出历史记录

可以在门户中的某个区域中查看每个大小的历史价格和逐出费率。 选择 " **查看定价历史记录" 和 "比较附近地区的价格"** 可查看表或特定大小的定价关系图。  以下图像中的定价和逐出率只是示例。 

**图表**：

:::image type="content" source="./media/spot-chart.png" alt-text="区域选项的屏幕截图，其中的定价和逐出率不同于图表。":::

**表**：

:::image type="content" source="./media/spot-table.png" alt-text="区域选项的屏幕截图，其中的定价和逐出率不同于图表。":::



##  <a name="frequently-asked-questions"></a>常见问题

**问：** 创建后，点 VM 与常规标准 VM 是否相同？

**答:** 是，但 Spot VM 没有 SLA，可以随时将其逐出。


**问：** 当被逐出但仍然需要容量时，该怎么办？

**答:** 如果马上需要容量，建议使用标准 VM，而不要使用 Spot VM。


**问：** 如何为专色 Vm 管理配额？

**答：** 污点 Vm 将有单独的配额池。 将在 VM 与规模集实例之间共享 Spot 配额。 有关详细信息，请参阅 [Azure 订阅和服务限制、配额与约束](../azure-resource-manager/management/azure-subscription-service-limits.md)。


**问：** 是否可以为 Spot 申请额外的配额？

**答:** 是的，你可以通过[标准配额申请流程](../azure-portal/supportability/per-vm-quota-requests.md)提交申请，请求提高 Spot VM 的配额。


**问：** 我可以在何处发布问题？

**答:** 你可以在[问答](/answers/topics/azure-spot.html)中发布问题并使用 `azure-spot` 来标记问题。 

## <a name="next-steps"></a>后续步骤
使用 [CLI](./linux/spot-cli.md)、 [门户](spot-portal.md)、 [ARM 模板](./linux/spot-template.md)或 [PowerShell](./windows/spot-powershell.md) 部署专色 vm。

你还可以 [使用点 VM 实例部署规模集](../virtual-machine-scale-sets/use-spot.md)。

如果遇到错误，请参阅 [错误代码](./error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。