---
title: 什么是 Azure 预留？ | Microsoft Docs
description: 了解 Azure 预留和定价，以节省虚拟机、SQL 数据库和其他资源成本。
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
ms.date: 08/08/2018
ms.author: yashar
ms.openlocfilehash: 93c11852a11e0bb57a0b92090368298fc14b8c2a
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39626298"
---
# <a name="what-are-azure-reservations"></a>什么是 Azure 预留？

Azure 预留通过预付一年或三年的虚拟机或 SQL 数据库计算能力来帮助节省资金。 通过预付，能够以折扣价购买所用资源。 Azure 预留可大幅度减少虚拟机或 SQL 数据库计算成本，最多可以降低 72% 的即用即付价格，但需提前一年或三年预留。 预留提供计费折扣，且不影响虚拟机或 SQL 数据库的运行时状态。

可在 [Azure 门户](https://aka.ms/reservations)中购买预留。 相关详细信息，请参阅以下主题：

- [通过 Azure 虚拟机预留实例为虚拟机预付费](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [通过 Azure SQL 数据库保留容量预付 SQL 数据库计算资源费用](../sql-database/sql-database-reserved-capacity.md)

## <a name="why-should-i-buy-a-reservation"></a>为何应购买预留？

如果需要长时间运行虚拟机或 SQL 数据库，购买预留可以获得最实惠的价格。 例如，如果持续运行四个服务实例，在不购买预留的情况下，需按即用即付价格付费。 如果购买这些资源的预留，则将立即获得预留折扣。 这些资源不再按即用即付费率收费。

## <a name="what-charges-does-a-reservation-cover"></a>预留涵盖哪些费用？

- 虚拟机预留实例：预留仅涵盖虚拟机计算成本。 而不涵盖软件、网络或存储等其他费用。
- SQL 数据库预留 vCore：预留仅包含计算成本。 许可证单独计费。

对于 Windows 虚拟机和 SQL 数据库，可以利用 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)来涵盖许可费用。

## <a name="whos-eligible-to-purchase-a-reservation"></a>谁有资格购买预留？

使用以下订阅类型的 Azure 客户可购买预留：

- 企业协议订阅套餐类型 (MS-AZR-0017P)。
- 
  [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)订阅套餐类型 (MS-AZR-003P)。 订阅必须具有“所有者”角色才能购买预留。 若要以企业合约方式购买预留，企业管理员必须在 EA 门户中启用预留购买。 此设置默认已启用。
- 云解决方案提供商 (CSP) 合作伙伴可使用 Azure 门户或[合作伙伴中心](https://docs.microsoft.com/partner-center/azure-reservations)购买 Azure 预留。 

Azure 预留折扣仅适用于与企业、即用即付或 CSP 订阅类型关联的虚拟机或 SQL 数据库。

## <a name="how-is-a-reservation-billed"></a>预留如何计费？

预留通过与订阅绑定的付款方式付款。 如果订阅为“企业”订阅，则虚拟机预留实例费用会从货币承诺余额中扣除。 如果货币承诺余额不足以支付虚拟机预留实例的费用，则还会向你收取所欠费用。 如果订阅为“即用即付”订阅，则会立即从帐户中的信用卡扣费。 如果采用账单付费的方式，则费用显示在下次的账单上。

## <a name="how-is-the-reservation-discount-applied"></a>如何应用预留折扣？

预留折扣适用于与购买预留时选择的特性相匹配的资源使用情况。 这些特性包括匹配的 VM、SQL 数据库或其他资源的运行范围。 例如，如果需要“美国西部”区域四个标准 D2 虚拟机的预留折扣，请选择正在运行的 VM 所在的订阅。 如果虚拟机是在注册/帐户的不同订阅中运行，则请选择作为共享的范围。 共享范围允许跨订阅应用预留折扣。 可在购买预留后更改范围。 有关详细信息，请参阅[在 Azure 中管理预留](billing-manage-reserved-vm-instance.md)。

预留折扣仅适用于与企业或即用即付订阅类型关联的虚拟机或 SQL 数据库。 订阅中运行的其他产品/服务类型的虚拟机或 SQL 数据库无法获得预留折扣。 对于企业合约，企业开发/测试订阅没有资格获得预留权益。

若要更好地了解预留如何影响虚拟机或 SQL 数据库计费，请参阅[了解如何应用预留折扣](billing-understand-vm-reservation-charges.md)。

## <a name="what-happens-when-the-reservation-term-expires"></a>预留期限过期时，会发生什么情况？

预留期限结束时，计费折扣随即过期，虚拟机、SQL 数据库或其他资源价格按即用即付价格计费。 Azure 预留不会自动续订。 若要继续获得计费折扣，必须为符合预留条件的服务购买新的预留。

## <a name="next-steps"></a>后续步骤

购买[虚拟机预留实例](../virtual-machines/windows/prepay-reserved-vm-instances.md)或 [SQL 数据库保留容量](../sql-database/sql-database-reserved-capacity.md)，开始节省虚拟机费用。

若要了解有关预留的详细信息，请参阅以下文章：

- [管理 Azure 预留](billing-manage-reserved-vm-instance.md)
- [了解如何应用预留折扣](billing-understand-vm-reservation-charges.md)
- [了解即用即付订阅的预留使用情况](billing-understand-reserved-instance-usage.md)
- [了解企业合约的预留使用情况](billing-understand-reserved-instance-usage-ea.md)
- [预留未包含的 Windows 软件成本](billing-reserved-instance-windows-software-costs.md)
- [合作伙伴中心云解决方案提供商 (CSP) 计划中的 Azure 预留](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果仍有疑问，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。
