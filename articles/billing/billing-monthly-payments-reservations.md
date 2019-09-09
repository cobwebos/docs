---
title: 按前或每月支付购买 Azure 预订
description: 了解如何在前或每月支付时购买 Azure 预订。
services: billing
author: bandersmsft
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: banders
ms.openlocfilehash: d211334ad2aa760cd63b98c6827fb2512811a1d3
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806943"
---
# <a name="purchase-reservations-with-monthly-payments"></a>购买包含月度付款的预订

到现在为止，Azure 保留期之前需要支付。 现在，你可以支付每月支付的保留费用。 与您支付了所有金额的前期购买不同，每月支付选项会将预留的总成本平均地除以每个月的期限。 前期和月度预订的总成本相同，并且选择每月支付费用时，不会支付任何额外费用。

每月支付金额可能会有所不同，具体取决于当前月份的本地货币的市场汇率。

每月付款适用于：

- 虚拟机
- SQL 数据库
- SQL 数据仓库
- Cosmos DB
- 应用服务戳记费

[Azure 门户](https://portal.azure.com/?Microsoft_Azure_Reservations_EnableMultiCart=true&amp;paymentPlan=true#blade/Microsoft_Azure_Reservations/CreateBlade)中的采购预订。

![显示预订购买的示例](./media/billing-monthly-payments-reservations/purchase-reservation.png)

在购买预订时，可以查看付款计划。 单击 "**查看完全付款计划**"。

![显示预订付款计划的示例](./media/billing-monthly-payments-reservations/prepurchase-schedule.png)

若要在购买后查看付款计划，请选择预订，单击**预订订单 ID**，然后单击 "**付款**" 选项卡。

## <a name="view-payments-made"></a>查看进行的付款

您可以使用 Api、使用情况数据和成本分析来查看所做的付款。 对于每月支付的预订，频率值在使用情况数据和预订费用 API 中显示为 "**定期**"。 对于前支付的预留，该值显示为 "**一次性**"。

成本分析在默认视图中显示每月购买情况。 应用**费用类型**的**购买**筛选器并**定期**进行 **，以查看**所有购买情况。 若要仅查看预订，请对**预订**应用筛选器。

![显示成本分析中的预订购买成本的示例](./media/billing-monthly-payments-reservations/cost-analysis.png)

## <a name="switch-to-monthly-payments-at-renewal"></a>切换到每月付款续订

续订预订时，可以将计费频率更改为每月。

## <a name="exchange-and-refunds"></a>交换和退款

与其他保留一样，您可以通过每月计费来退款或交换预订。 目前，你可以提交支持请求，以针对每月计费购买的预订开始交换或退款。

当你交换每月支付的预订时，新购买的总生存期成本应大于为退回预订取消的遗留付款。 交换没有其他限制或费用。 可以交换提前支付的预订，以购买按月计费的新预订。 但是，新预订的生存期值应大于返回的预订的按比例值。

如果取消每月支付的预订，Microsoft 可能会对取消的将来承诺付款应用取消费用。 剩余的已提交付款的退款限制为 $50000 美元。

有关 exchange 和退款的详细信息，请参阅[Azure 预订的自助服务交换和退款](billing-azure-reservations-self-service-exchange-and-refund.md)。

## <a name="faq"></a>常见问题

问： Azure 是否提供 "部分前期保留？"<br>
A. 否。 由于在前面和每月支付的预订成本相同，因此 Microsoft 不支持部分前期支付。

问： Microsoft 云解决方案提供商（CSP）计划是否有月支付？<br>
A. 是，合作伙伴可以在 Azure 门户中购买其 CSP 客户的预订。 使用月度帐单购买预订的功能在合作伙伴中心不可用。

问： 我是我们的 Azure 政府客户，可否按月支付预订购买费用？<br>
A. 目前不可以。

问： 何时可以在 Azure 门户中交换或退款，而不是创建支持票证？<br>
A. 目前不可以。 每月支付的 exchange 和退款预订请求均由 Azure 支持部门处理。

## <a name="next-steps"></a>后续步骤

- 若要了解有关预订的详细信息，请参阅[什么是 Azure 保留？](billing-save-compute-costs-reservations.md)
- 若要了解在购买保留之前应完成的任务，请参阅在[购买之前确定正确的 VM 大小](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)
