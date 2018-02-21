---
title: "了解即用即付订阅的 Azure 保留实例使用情况 | Microsoft Docs"
description: "了解如何读取使用情况以了解即用即付订阅的保留实例应用情况"
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
ms.openlocfilehash: 29f153803d5eb74e2d287d97cf9436e81b2a3e20
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="understand-reserved-instance-usage-for-your-pay-as-you-go-subscription"></a>了解即用即付订阅的保留实例使用情况

使用[“预订”页](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade )中的 ReservationId 和 [Azure 帐户门户](https://account.azure.com)中的使用情况文件了解保留实例的使用率。


>[!NOTE]
>本文不适用于 EA 客户。 如果是 EA 客户，请参阅[了解企业许可登记表中保留实例的使用情况](billing-understand-reserved-instance-usage-ea.md)。 本文还假定将预订应用于单个订阅。 如果将预订应用于多个订阅，则预订权益可能跨多个使用情况 csv 文件。 

在以下部分中，假定在美国东部区域中运行 Standard_DS1_v2 Windows VM，则预订信息如下表所示：

| 字段 | 值 |
|---| :---: |
|预订 ID |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|数量 |1|
|SKU | Standard_DS1_v2|
|区域 | eastus |

## <a name="reservation-application"></a>预订应用

涵盖 VM 的硬件部分，因为部署的 VM 与预订属性匹配。 若要查看保留实例未涵盖哪些 Windows 软件，请转到 [Azure 保留 VM 实例 Windows 软件成本](billing-reserved-instance-windows-software-costs.md)。

### <a name="statement-section-of-csv"></a>csv 的声明部分
csv 的此部分显示预订的总体使用情况。 将筛选器应用于包含“保留-”的“计量”子类别字段，数据如以下屏幕截图所示：![直接声明预订](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

“保留-基础 VM”行包含预订涵盖的小时总数。 此行是 0.00 美元，因为保留实例涵盖它。 “保留-Windows Server (1 核心)”行涵盖 Windows 软件的成本。

### <a name="daily-usage-section-of-csv"></a>csv 的“每日使用情况”部分
筛选其他信息，并键入预订 ID。 以下屏幕截图显示了与预订相关的字段。 

![每日使用费用](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. “其他信息”字段中的 ReservationId 是用于将权益应用到 VM 的预订。
2. ConsumptionMeter 是 VM 的计量 ID。
3. “保留-基础 VM”计量子类别行表示声明部分中的 0 美元成本行。 预订已支付了运行此 VM 的成本。
4. 这是预订的计量 ID。 此计量的成本是 0 美元。 任何有资格作为保留实例的 VM 都在 csv 中具有此计量 ID 以便核算成本。 
5. Standard_DS1_v2 是 1 vCPU VM，此 VM 在不使用 Azure 混合权益的情况下部署。 因此，此计量涉及 Windows 软件的额外费用。 请参阅 [Azure 保留 VM 实例 Windows 软件成本](billing-reserved-instance-windows-software-costs.md)， 找到与 D 系列 1 核心 VM 对应的计量。 如果使用 Azure 混合权益，则不会收取此额外费用。 

## <a name="next-steps"></a>后续步骤
若要了解有关预订虚拟机实例的详细信息，请参阅以下文章。

- [通过预订 VM 实例预付虚拟机](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [管理预订虚拟机实例](billing-manage-reserved-vm-instance.md)
- [通过预订虚拟机实例节省虚拟机资金](billing-save-compute-costs-reservations.md)
- [了解如何应用预订虚拟机实例折扣](billing-understand-vm-reservation-charges.md)
- [了解企业许可登记表的预订实例使用情况](billing-understand-reserved-instance-usage-ea.md)
- [预订实例未包含的 Windows 软件成本](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。

如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。