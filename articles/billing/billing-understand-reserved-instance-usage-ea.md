---
title: 了解企业的 Azure 预留实例使用情况 - Azure 计费 | Microsoft Docs
description: 了解如何读取使用情况，以了解如何应用企业许可登记表的 Azure 虚拟机预留实例。
services: billing
documentationcenter: ''
author: manish-shukla01
manager: manshuk
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: manshuk
ms.openlocfilehash: a92fce33b194c5cb7b763930e7fd11135f9fbd4f
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301308"
---
# <a name="understand-azure-reserved-instance-usage-for-your-enterprise-enrollment"></a>了解企业许可登记表的 Azure 预留实例使用情况
使用“预留”页[](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)中的 ReservationId 和 [EA 门户](https://ea.azure.com)中的使用情况文件了解预留实例的使用率。 还可以在 [EA 门户](https://ea.azure.com)的使用情况摘要部分中看到预留实例使用情况。

>[!NOTE]
>如果已在即用即付计费上下文中购买了预留实例，请参阅[了解即用即付订阅的预留实例使用情况](billing-understand-reserved-instance-usage.md)

在以下部分中，假定在美国东部区域中运行 Standard_D1_v2 Windows VM，并且预留实例信息如下表所示：

| 字段 | 值 |
|---| --- |
|预订 ID |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|数量 |1|
|SKU | Standard_D1|
|区域 | eastus |

## <a name="reserved-instance-application"></a>预留实例应用程序

将涵盖 VM 的硬件部分，因为部署的 VM 与预留实例属性匹配。 若要查看保留实例未涵盖哪些 Windows 软件，请转到 [Azure 预订 VM 实例 Windows 软件成本](billing-reserved-instance-windows-software-costs.md)。


### <a name="reserved-instance-usage-in-csv"></a>csv 中的预留实例使用情况
可以从 EA 门户下载 EA 使用情况 csv。 在下载的 csv 文件中，筛选其他信息，并键入 **ReservationID**。 以下屏幕截图显示了与预留实例相关的字段：

![适用于 Azure 预留实例的企业协议 (EA) csv](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. “其他信息”字段中的 **ReservationId** 表示用于将权益应用到 VM 的预留实例。
2. ConsumptionMeter 是 VM 的计量 ID。
3. 这是成本为 0 美元的预留计量，因为运行 VM 的成本已通过预留实例支付。 
4. Standard_D1 是 1 vCPU VM，此 VM 在不使用 Azure 混合权益的情况下部署。 因此，此计量涉及 Windows 软件的额外费用。 请参阅 [Azure 保留 VM 实例 Windows 软件成本](billing-reserved-instance-windows-software-costs.md)， 找到与 D 系列 1 核心 VM 对应的计量。 如果使用 Azure 混合权益，则不会收取此额外费用。

### <a name="reserved-instance-usage-in-usage-summary-page-in-ea-portal"></a>EA 门户中使用情况摘要页上的预留实例使用情况

预留实例使用情况也会出现在 EA 门户的使用情况摘要部分中：![企业协议 (EA) 使用情况摘要](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. 不会收取 VM 的硬件组件费用，因为它由保留实例涵盖。 
2. 未使用 Azure 混合权益时，将收取 Windows 软件费用。 

## <a name="next-steps"></a>后续步骤
若要了解有关 Azure 预留实例的详细信息，请参阅以下文章：

- [通过 Azure 预留实例节省虚拟机资金](billing-save-compute-costs-reservations.md)
- [通过预留实例为虚拟机预付资金](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [管理预留实例](billing-manage-reserved-vm-instance.md)
- [了解如何应用预留实例折扣](billing-understand-vm-reservation-charges.md)
- [了解即用即付订阅的预订实例使用情况](billing-understand-reserved-instance-usage.md)
- [预订实例未包含的 Windows 软件成本](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果仍有疑问，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。