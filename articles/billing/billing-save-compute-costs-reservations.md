---
title: "通过为 Azure 虚拟机预付款来节省资金 - Azure | Microsoft Docs"
description: "了解 Azure 保留虚拟机实例，以便节省虚拟机成本。"
services: billing
documentationcenter: 
author: vikramdesai01
manager: vikramdesai01
editor: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2017
ms.author: vikdesai
ms.openlocfilehash: 96e9cf2fed0b22fd7aa7b9ffeab0e94738ce510d
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2017
---
# <a name="save-money-on-virtual-machines-with-reserved-virtual-machine-instances"></a>通过保留虚拟机实例节省虚拟机资金 
使用保留虚拟机实例时，需预付一年或三年的计算能力款项，以便获得所用虚拟机的折扣。 它可以显著减少虚拟机成本，最多可以降低 72% 的即用即付价格，前提是提前承诺使用一年或三年的服务。 保留虚拟机实例是一种计费折扣，不影响虚拟机的运行时状态。

可以在 [Azure 门户](https://aka.ms/reservations)中购买保留虚拟机实例。 有关详细信息，请参阅 [Prepay for virtual machines and save money with Reserved Virtual Machine Instances](https://go.microsoft.com/fwlink/?linkid=861721)（通过保留虚拟机实例预付虚拟机款项以节省资金）。

## <a name="why-should-i-buy-a-reserved-virtual-machine-instance"></a>为何应购买保留虚拟机实例？
如果虚拟机运行时间长，则购买保留虚拟机实例可以获得最实惠的价格。 例如，如果在“美国西部”区域持续运行四个标准 D2 实例，则在不购买保留虚拟机实例的情况下，需按即用即付价格付费。 如果为这四个 VM 购买保留虚拟机实例，则会立即获得这些 VM 的价格优惠。 这些 VM 的费率不再是即用即付费率。 

## <a name="what-charges-does-a-reserved-virtual-machine-instance-cover"></a>保留虚拟机实例涵盖哪些收费？
保留虚拟机实例仅涵盖 Windows 或 Linux 虚拟机的虚拟机基础结构费用， 而不涵盖软件、网络或存储这样的其他费用。 对于 Windows 虚拟机，可以利用 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)来涵盖 Windows 许可费用。

## <a name="whos-eligible-to-purchase-a-reserved-virtual-machine-instance"></a>谁有资格购买保留虚拟机实例？
以下订阅类型的 Azure 客户可以购买保留虚拟机实例：
-   企业协议订阅产品/服务类型 (MS-AZR-0017P)。
-   [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)订阅产品/服务类型 (MS-AZR-003P)。
必须是订阅的“所有者”角色才能购买保留实例。 若要在企业注册时购买保留虚拟机实例，企业管理员必须在 EA 门户中允许购买保留虚拟机实例。默认情况下，该设置是启用的。

## <a name="how-is-a-reserved-virtual-machine-instances-purchase-billed"></a>购买保留虚拟机实例时如何付款？
购买保留虚拟机实例时，可以通过与订阅绑定的付款方式付款。 如果订阅为“企业”订阅，则保留虚拟机实例费用会从货币承诺余额中扣除。 如果货币承诺余额不足以支付保留虚拟机实例的费用，则还会向你收取所欠费用。
如果订阅为“即用即付”订阅，则会立即从帐户中的信用卡扣费。 如果采用账单付费的方式，则费用显示在下次的账单上。

## <a name="how-is-the-purchased-reserved-virtual-machine-instance-discount-applied"></a>购买的保留虚拟机实例如何应用折扣？
保留虚拟机实例折扣适用于符合某些特性的虚拟机，这些特性是在购买保留虚拟机实例时选择的。 这些特性包括相匹配的 VM 的运行范围。 例如，如果需要“美国西部”区域四个“标准 D2”虚拟机的保留 VM 实例折扣，请选择正在运行的 VM 所在的订阅。 如果虚拟机是在注册/帐户的不同订阅中运行，则请选择作为共享的范围。 共享范围允许跨订阅应用保留折扣。
可以在购买保留 VM 实例后更改范围。 若要更改范围，请参阅有关如何管理保留虚拟机实例的文档。

保留折扣仅适用于订阅中产品/服务类型为“企业”或“即用即付”的虚拟机。 在订阅中运行的其他产品/服务类型的虚拟机没有保留折扣。 对于企业注册，企业开发/测试订阅没有资格获得保留实例权益。

[了解如何应用保留计费权益](https://go.microsoft.com/fwlink/?linkid=863405)中介绍了保留虚拟机实例对虚拟机计费的影响。

## <a name="what-happens-when-the-reservation-term-expires"></a>保留期限过期时，会发生什么情况？
保留期限结束时，计费折扣过期，虚拟机基础结构按即用即付价格计费。 保留虚拟机实例不自动续订。 若要继续获取计费折扣，必须购买新的保留虚拟机实例。 

## <a name="sizes-and-regional-availability"></a>大小和区域可用性
保留虚拟机实例适用于大多数 VM 大小，只有一些例外：
- 预览版 VM 大小 – 任何预览版大小都不适用于保留虚拟机实例的购买。
- 云 – 保留虚拟机实例不适用于“Azure 美国政府”、“德国”或“中国”区域的购买。 
- 配额不足 – 其范围仅限单个订阅的保留 VM 实例必须在订阅中具有可用的 vCPU 配额才能获得新的 RI。 例如，如果目标订阅的配额限制为 10 个 vCPU（适用于 D-Series 系列），则不能为 11 个 Standard_D1 实例购买保留 VM 实例。 保留虚拟机实例的配额检查包括已在订阅中部署的 VM。 例如，如果订阅的配额为 10 个 vCPU（适用于 D-Series 系列）。 如果该订阅部署了两个 standard_D1 实例，则可在该订阅中为 10 个 standard_D1 实例购买保留 VM 实例。 
- 容量限制 – 在极少数情况下，Azure 限制购买部分 VM 大小的新保留虚拟机实例，因为某个区域的容量不足。

## <a name="next-steps"></a>后续步骤
购买[保留虚拟机实例](https://go.microsoft.com/fwlink/?linkid=861721)，开始节省虚拟机费用。 

如果需要帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。
