---
title: Azure 预留建议是如何创建的
description: 了解如何为虚拟机创建 Azure 预留建议。
author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/28/2020
ms.author: banders
ms.openlocfilehash: a43e0c4d86bd47c953b50ab9fb1fd8df00e7df3d
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76851345"
---
# <a name="how-reservation-recommendations-are-created"></a>如何创建预留建议

Azure 预留实例 (RI) 购买建议是由 Azure 消耗[预留建议 API](/rest/api/consumption/reservationrecommendations) 生成的。 [Azure 顾问](../..//advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)也使用来自该 API 的建议。 顾问将建议显示在 Azure 门户中。

如果你有在 Azure 中运行的 VM，则可以对 RI 使用折扣定价，对 VM 使用预付费。 Microsoft 消耗建议 API 评估你在 7、30 和 60天内的使用情况，并提供最佳的 RI 配置建议。 它计算你之前没有 RI 时将支付的成本，并将该成本与具有 RI 时将支付的成本进行比较，以优化成本节省。

Azure 顾问基于 30 天期间显示建议。

为简单起见，下面的示例展示了针对七天建议进行的计算。 在计算 30 天或 60 天建议时，将应用相同的方法。

## <a name="calculation-method-example"></a>计算方法示例

假设你在特定 SKU 和区域的每小时 Windows VM 使用量在七天（168 小时）内发生变化。 七天内的最小使用量为 65 个单位，最大使用量为 127 个单位。 在此示例中，第 79 小时使用了 80 个 VM，你购买了 75 个 RI。

如果你购买 75 个预留实例，则你将为第 79 小时支付以下成本：

- 75 个预留实例。 购买 RI 时，成本是预付的。
- 预留实例涵盖了正在运行的 VM 的硬件成本，因此你将以纯软件价格支付 75 个小时的费用。
- 第 79 小时的使用量是 80，因此你将支付 5 小时的 Windows 加硬件组合计量价格。 组合价格基于你的企业协议 (EA) 或即用即付费率。

如果你购买 75 个 RI，则可以通过添加前面的每小时成本来计算总成本。 还可以使用你的费率计算你的当前成本。 在此示例中，这两个金额之间的差别是你节省 7 天的成本。

此 API 为每个特定使用点执行计算。 然后，它将返回可以最大限度地节省成本的建议数量。 在下面的示例中，图表显示了在第 68 小时的成本节省峰值。 此后，成本节省降低，因此 API 建议的值为 68。

![显示了成本节省峰值的图示](./media/reserved-instance-purchase-recommendations/peak-savings.png)

## <a name="other-expected-api-behavior"></a>其他预期的 API 行为

- 当使用了适用于 Windows 的 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)时，此 API 会显示该权益可能会节省的成本。 如果未使用该权益，则 API 建议将基于核心 Windows 成本。 如果可供你使用，请考虑使用 Azure 混合权益来提高成本节省。
- 当使用七天的回溯期时，如果 VM 关闭超过一天，你可能无法获得建议。

## <a name="next-steps"></a>后续步骤
- 了解 [Azure 预留折扣如何应用于虚拟机](../manage/understand-vm-reservation-charges.md)。
