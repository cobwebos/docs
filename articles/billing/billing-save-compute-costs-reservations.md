---
title: 什么是 Azure 预留实例？ - Azure 计费 | Microsoft Docs
description: 了解 Azure 虚拟机预留实例和虚拟机定价，以节省虚拟机的成本并获得最有利的价格。
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: yashar
ms.openlocfilehash: 93be4bb037af400599b88bb71f34143ee65a5deb
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301696"
---
# <a name="what-are-azure-reserved-vm-instances"></a>什么是 Azure 虚拟机预留实例？
[Azure 虚拟机预留实例](https://azure.microsoft.com/pricing/reserved-vm-instances)允许预付一年或三年的计算容量并提供所用虚拟机的折扣，帮助客户节省资金。 Azure 预留实例可以显著减少虚拟机成本，最多可以降低 72% 的即用即付价格，前提是提前承诺使用一年或三年的服务。 预留实例提供计费折扣，且不影响虚拟机的运行时状态。

可以在 [Azure 门户](https://aka.ms/reservations)中购买预留实例 (RI)。 有关详细信息，请参阅[通过预留实例预付虚拟机费用以节省资金](https://go.microsoft.com/fwlink/?linkid=861721)。

## <a name="why-should-i-buy-a-reserved-instance"></a>为何购买预留实例？
如果需要长时间运行虚拟机，购买预留实例可以获得最实惠的价格。 例如，如果在“美国西部”区域持续运行四个标准 D2 VM 的实例，则在不购买预留实例的情况下，需按即用即付价格付费。 如果为这四个 VM 购买预留实例，则会立即获得这些 VM 的价格优惠。 这些 VM 的费率不再是即用即付费率。 

## <a name="what-charges-does-a-reserved-instance-cover"></a>预留实例涵盖哪些费用？
预留实例仅涵盖 Windows 或 Linux 虚拟机的虚拟机基础结构费用， 而不涵盖附加的软件、网络或存储费用。 对于 Windows 虚拟机，可以利用 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)来涵盖 Windows 许可费用。

## <a name="whos-eligible-to-purchase-a-reserved-instance"></a>谁有资格购买预留实例？
以下订阅类型的 Azure 客户可以购买预留实例：
-   企业协议订阅产品/服务类型 (MS-AZR-0017P)。
-   [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)订阅产品/服务类型 (MS-AZR-003P)。 必须是订阅的“所有者”角色才能购买保留实例。 若要在企业登记时购买预留实例，企业管理员必须在 EA 门户中启用预留实例购买选项。 此设置默认已启用。
-   云解决方案提供商 (CSP) 合作伙伴可以使用 Azure 门户或[合作伙伴中心](https://docs.microsoft.com/partner-center/azure-reservations)购买预留实例。

## <a name="how-is-a-reserved-instance-purchase-billed"></a>预留实例如何计费？
购买预留实例时，会根据与订阅绑定的付款方式计费。 如果使用的是企业订阅，则预留实例费用会从货币承诺余额中扣除。 如果货币承诺余额不足以支付预留实例的费用，则还会向你收取所欠费用。
如果订阅为“即用即付”订阅，则会立即从帐户中的信用卡扣费。 如果采用账单付费的方式，则费用显示在下次的账单上。

## <a name="how-is-the-purchased-reserved-instance-discount-applied"></a>购买的预留实例如何应用折扣？
预留实例折扣适用于符合某些特性的虚拟机，这些特性是在购买预留实例时选择的。 这些特性包括相匹配的 VM 的运行范围。 例如，如果需要“美国西部”区域四个标准 D2 虚拟机的预留实例折扣，请选择正在运行的 VM 所在的订阅。 如果虚拟机是在注册/帐户的不同订阅中运行，则请选择作为共享的范围。 共享范围允许跨订阅应用预留实例折扣。 可以在购买预留实例后更改范围。 若要更改范围，请参阅有关如何管理预留实例的文档。

预留实例折扣仅适用于与企业或即用即付订阅类型关联的虚拟机。 在订阅中运行的其他产品/服务类型的虚拟机没有预留实例折扣。 对于企业注册，企业开发/测试订阅没有资格获得保留实例权益。

若要更好地了解预留实例如何影响虚拟机计费，请参阅[了解如何应用预留实例计费权益](https://go.microsoft.com/fwlink/?linkid=863405)。

## <a name="what-happens-when-the-reserved-instance-term-expires"></a>预留实例期限过期时，会发生什么情况？
预留实例期限结束时，计费折扣过期，虚拟机基础结构按即用即付价格计费。 预留实例不会自动续订。 若要继续获得计费折扣，必须购买新的预留实例。 

## <a name="sizes-and-regional-availability"></a>大小和区域可用性
预留实例适用于大多数 VM 大小，只有一些例外：
- 预览版 VM – 以预览版提供的任何 VM 系列或大小都不适用于预留实例的购买条款。
- 云 – 预留实例不适用于 Azure 美国政府、德国或中国区域的购买条款。 
- 配额不足 – 其范围仅限单个订阅的预留实例必须在订阅中具有可用的 vCPU 配额才能获得新的 RI。 例如，如果目标订阅的配额限制为 10 个 vCPU（适用于 D 系列），则不能为 11 个 Standard_D1 实例购买预留实例。 预留实例的配额检查包括已在订阅中部署的 VM。 例如，如果该订阅的配额为针对 D 系列购买 10 个 vCPU，并且已部署两个 Standard_D1 实例，则可在该订阅中为 10 个 Standard_D1 实例购买预留实例。 
- 容量限制 – 在极少数情况下，Azure 限制购买部分 VM 大小的新预留实例，因为某个区域的容量不足。

## <a name="next-steps"></a>后续步骤
购买 [Azure 预留实例](https://go.microsoft.com/fwlink/?linkid=861721)，开始节省虚拟机的成本。 

若要了解有关预留实例的详细信息，请参阅以下文章：

- [通过预留实例为虚拟机预付资金](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [管理预留实例](billing-manage-reserved-vm-instance.md)
- [了解如何应用预留实例折扣](billing-understand-vm-reservation-charges.md)
- [了解即用即付订阅的预订实例使用情况](billing-understand-reserved-instance-usage.md)
- [了解企业许可登记表的预订实例使用情况](billing-understand-reserved-instance-usage-ea.md)
- [预订实例未包含的 Windows 软件成本](billing-reserved-instance-windows-software-costs.md)
- [合作伙伴中心云解决方案提供商 (CSP) 计划中的保留实例](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果仍有疑问，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。
