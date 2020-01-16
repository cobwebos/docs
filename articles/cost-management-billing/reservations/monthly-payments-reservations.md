---
title: 通过提前付款或按月付款的方式购买 Azure 预留
description: 了解如何通过提前付款或按月付款的方式购买 Azure 预留。
services: billing
author: bandersmsft
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: banders
ms.openlocfilehash: cf9bb7f3b9661d5957ad569fce7112fe16659761
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75995944"
---
# <a name="purchase-reservations-with-monthly-payments"></a>通过按月付款的方式购买预留

以前，Azure 预留需要提前付款。 而现在，你可以通过按月付款的方式购买预留。 与需要全额付款的提前付款购买不同，按月付款选项将预留的总费用平均分配到期限的每个月。 预付和每月预留的总费用相同，选择每月支付不会产生额外的费用。

每月付款金额可能会有所不同，具体取决于本地货币的当月市场汇率。

每月付款适用于：

- 虚拟机
- Azure 存储器
- SQL Database
- SQL 数据仓库
- Azure Cosmos DB
- 应用服务印花费

在 [Azure 门户](https://portal.azure.com/?Microsoft_Azure_Reservations_EnableMultiCart=true&amp;paymentPlan=true#blade/Microsoft_Azure_Reservations/CreateBlade)中购买预留。

![显示预留购买的示例](./media/monthly-payments-reservations/purchase-reservation.png)

进行预留购买时，可以查看付款计划。 单击“查看完整的付款计划”。

![显示预留付款计划的示例](./media/monthly-payments-reservations/prepurchase-schedule.png)

若要在购买后查看付款计划，请选择一个预留，单击“预留订单 ID”，然后单击“付款”选项卡。

## <a name="view-payments-made"></a>查看已进行的付款

可以查看通过 API、使用数据和成本分析进行的付款。 对于按月付款的预留，频率值在使用数据和预留费用 API 中显示为“定期”。 对于提前付款的预留，该值显示为“一次性”。

成本分析在默认视图中显示按月购买。 对“费用类型”应用“购买”筛选器，对“频率”应用“定期”筛选器，即可查看所有购买项。 若只查看预留，请应用“预留”筛选器。

![在成本分析中显示预留购买成本的示例](./media/monthly-payments-reservations/cost-analysis.png)

## <a name="switch-to-monthly-payments-at-renewal"></a>在续订时转为按月付款

续订预留时，可以将计费频率更改为“按月”。

## <a name="exchange-and-refunds"></a>交换和退款

与其他预留一样，可以通过每月计费对购买的预留执行退款或交换操作。 目前，提交一个支持请求即可通过每月计费对购买的预留执行交换或退款操作。

交换一个按月付款的预留时，新购买的生存期费用总计应该大于已取消的针对退回的预留的剩余付款。 对于交换，没有任何其他限制或费用。 可以交换一个提前付款的预留，购买一个新的按月计费的预留。 但是，新预留的生存期价值应该大于退回的预留的按比例计算的价值。

如果取消按月付费的预留，Microsoft 可能会对已取消的未来承诺付款收取一项取消费。 剩余的承诺付款存在 50,000 美元的退款限制。

有关交换和退款的详细信息，请参阅 [Azure 预留的自助交换和退款](exchange-and-refund-azure-reservations.md)。

## <a name="next-steps"></a>后续步骤

- 若要详细了解预留，请参阅[什么是 Azure 保留？](save-compute-costs-reservations.md)
- 若要了解在购买预留之前应该完成的任务，请参阅[在购买前确定正确的 VM 大小](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)
