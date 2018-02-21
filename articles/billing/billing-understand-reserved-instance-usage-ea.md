---
title: "了解企业的保留实例使用情况 | Microsoft Docs"
description: "了解如何读取使用情况以了解企业许可登记表中保留实例的应用情况。"
services: billing
documentationcenter: 
author: manish-shukla01
manager: manshuk
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: manshuk
ms.openlocfilehash: 515eae3c9a84a171bebc5213f5824e1b50336e34
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="understand--reserved-instance-usage-for-your-enterprise-enrollment"></a>了解企业许可登记表中保留实例使用情况
使用[“预订”页](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade )中的 ReservationId 和 [EA 门户](https://ea.azure.com)中的使用情况文件了解保留实例的使用率。 还可以在 [EA 门户](https://ea.azure.com)的使用情况摘要部分中看到预订使用情况。

>[!NOTE]
>如果已在即用即付计费上下文中购买了预订，请参阅[了解即用即付订阅的保留实例使用情况](billing-understand-reserved-instance-usage.md)

在以下部分中，假定在美国东部区域中运行 Standard_D1_v2 Windows VM，则预订信息如下表所示：

| 字段 | 值 |
|---| --- |
|预订 ID |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|数量 |1|
|SKU | Standard_D1|
|区域 | eastus |

## <a name="reservation-application"></a>预订应用

涵盖 VM 的硬件部分，因为部署的 VM 与预订属性匹配。 若要查看保留实例未涵盖哪些 Windows 软件，请转到 [Azure 预订 VM 实例 Windows 软件成本](billing-reserved-instance-windows-software-costs.md)。


### <a name="reservation-usage-in-csv"></a>csv 中的预订使用情况
可以从 EA 门户下载 EA 使用情况 csv。 在下载的 csv 文件中，筛选其他信息，并键入预订 ID。 以下屏幕截图显示了与预订相关的字段：

![保留实例的 EA csv](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. “其他信息”字段中的 ReservationId 表示用于将权益应用到 VM 的预订。
2. ConsumptionMeter 是 VM 的计量 ID。
3. 这是成本为 0 美元的 ReservationMeter，因为运行 VM 的成本已通过预订支付。 
4. Standard_D1 是 1 vCPU VM，此 VM 在不使用 Azure 混合权益的情况下部署。 因此，此计量涉及 Windows 软件的额外费用。 请参阅 [Azure 保留 VM 实例 Windows 软件成本](billing-reserved-instance-windows-software-costs.md)， 找到与 D 系列 1 核心 VM 对应的计量。 如果使用 Azure 混合权益，则不会收取此额外费用。

### <a name="reservation-usage-in-usage-summary-page-in-ea-portal"></a>EA 门户的使用情况摘要页上的预订使用情况

保留实例使用情况也会出现在 EA 门户的使用情况摘要部分中：![EA 使用情况摘要](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. 不会收取 VM 的硬件组件费用，因为它由保留实例涵盖。 
2. 未使用 Azure 混合权益时，将收取 Windows 软件费用。 

## <a name="next-steps"></a>后续步骤
若要了解有关预订虚拟机实例的详细信息，请参阅以下文章。

- [通过预订 VM 实例预付虚拟机](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [管理预订虚拟机实例](billing-manage-reserved-vm-instance.md)
- [通过预订虚拟机实例节省虚拟机资金](billing-save-compute-costs-reservations.md)
- [了解如何应用预订虚拟机实例折扣](billing-understand-vm-reservation-charges.md)
- [了解即用即付订阅的预订实例使用情况](billing-understand-reserved-instance-usage.md)
- [预订实例未包含的 Windows 软件成本](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。

如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。