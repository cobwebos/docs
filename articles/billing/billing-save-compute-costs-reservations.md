---
title: 什么是 Azure 预留项？ | Microsoft Docs
description: 了解 Azure 预订和定价以节省虚拟机、SQL 数据库、Azure Cosmos DB 和其他资源的成本。
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: banders
ms.openlocfilehash: 4133d78ccff1fa4eb712d8928cefa604ca2f52ca
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2019
ms.locfileid: "54902051"
---
# <a name="what-are-azure-reservations"></a>什么是 Azure 预留项？

通过预付一年或三年的虚拟机、SQL 数据库计算容量、Azure Cosmos DB 吞吐量或其他 Azure 资源的费用，Azure 预订可为你节省资金。 通过预付，能够以折扣价购买所用资源。 预订可将虚拟机、SQL 数据库计算、Azure Cosmos DB 或其他资源的成本大幅减少至即用即付价格的 72%。 预订提供计费折扣，并且不会影响资源的运行时状态。

可在 [Azure 门户](https://aka.ms/reservations)中购买预留。 相关详细信息，请参阅以下主题：

- [通过 Azure 虚拟机预留实例为虚拟机预付费](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [通过 Azure SQL 数据库保留容量预付 SQL 数据库计算资源费用](../sql-database/sql-database-reserved-capacity.md)
- [通过 Azure Cosmos DB 保留容量预付 Azure Cosmos DB 资源费用](../cosmos-db/cosmos-db-reserved-capacity.md)

## <a name="why-should-i-buy-a-reservation"></a>为何应购买预留？

如果你有长时间运行的虚拟机、Azure Cosmos DB 或 SQL 数据库，那么购买预订不失为一项获得最实惠价格的选择。 例如，如果持续运行四个服务实例，在不购买预留的情况下，需按即用即付价格付费。 如果购买这些资源的预留，则将立即获得预留折扣。 这些资源不再按即用即付费率收费。

## <a name="what-charges-does-a-reservation-cover"></a>预留涵盖哪些费用？

- 虚拟机预留实例：预留仅涵盖虚拟机计算成本。 而不涵盖软件、网络或存储等其他费用。
- SQL 数据库预留 vCore：预留仅包含计算成本。 许可证单独计费。
- Azure Cosmos DB 预留容量：预留涵盖为资源预配的吞吐量的费用，而不涵盖存储和网络费用。 

对于 Windows 虚拟机和 SQL 数据库，可以利用 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)来涵盖许可费用。

## <a name="whos-eligible-to-purchase-a-reservation"></a>谁有资格购买预留？

使用以下订阅类型的 Azure 客户可购买预订：

- 企业协议订阅套餐类型 (MS-AZR-0017P)。
- [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)订阅套餐类型 (MS-AZR-003P)。 订阅必须具有“所有者”角色才能购买预留。
- 云解决方案提供商 (CSP) 合作伙伴可使用 Azure 门户或[合作伙伴中心](https://docs.microsoft.com/partner-center/azure-reservations)购买 Azure 预订。

预订折扣仅适用于与企业、即用即付或 CSP 订阅类型相关联的资源。

## <a name="how-is-a-reservation-billed"></a>预留如何计费？

预留通过与订阅绑定的付款方式付款。 如果订阅为“企业”订阅，则虚拟机预留实例费用会从货币承诺余额中扣除。 如果货币承诺余额不足以支付虚拟机预留实例的费用，则还会向你收取所欠费用。 如果订阅为“即用即付”订阅，则会立即从帐户中的信用卡扣费。 如果采用账单付费的方式，则费用显示在下次的账单上。

## <a name="how-is-the-reservation-discount-applied"></a>如何应用预留折扣？

预留折扣适用于与购买预留时选择的特性相匹配的资源使用情况。 这些特性包括匹配的 VM、SQL 数据库、Azure Cosmos DB 或其他资源的运行范围。 例如，如果需要“美国西部”区域四个标准 D2 虚拟机的预留折扣，请选择正在运行的 VM 所在的订阅。 如果虚拟机是在注册/帐户的不同订阅中运行，则请选择作为共享的范围。 共享范围允许跨订阅应用预留折扣。 可在购买预留后更改范围。 有关详细信息，请参阅[管理 Azure 预订](billing-manage-reserved-vm-instance.md)。

预订折扣仅适用于与企业、即用即付或 CSP 订阅类型相关联的资源。 订阅中运行的其他产品/服务类型的资源无法获得预订折扣。 对于企业合约，企业开发/测试订阅没有资格获得预留权益。

若要更好地了解预订如何对计费产生影响，请参阅以下主题：

-  [了解 Azure 虚拟机预订实例折扣](billing-understand-vm-reservation-charges.md)
- [了解 Azure 预订折扣](billing-understand-vm-reservation-charges.md)
- [了解 Azure Cosmos DB 预订折扣](billing-understand-cosmosdb-reservation-charges.md)
- [了解 Azure 预订折扣和 SUSE 的使用情况](billing-understand-suse-reservation-charges.md)

## <a name="what-happens-when-the-reservation-term-expires"></a>预留期限过期时，会发生什么情况？

预订期限结束时，计费折扣随即过期，并且虚拟机、SQL 数据库、Azure Cosmos DB 或其他资源将按即用即付的价格计费。 Azure 预订不会自动续订。 若要继续获得计费折扣，必须为符合条件的服务和软件购买新的预订。

## <a name="discount-applies-to-different-sizes-with-instance-size-flexibility"></a>折扣可以按照实例大小灵活性应用于不同大小

购买预订时，折扣可以应用于具有同一大小组中属性的其他实例。 折扣覆盖范围的灵活性取决于预订的类型以及购买预订时选择的特性。

- 虚拟机预留实例：购买预留时，如果选择“优化对象: 实例大小灵活性”，则折扣覆盖范围将取决于所选的 VM 大小。 预订可以应用于同一大小系列组中的虚拟机 (VM) 大小。 有关详细信息，请参阅[预订 VM 实例的虚拟机大小灵活性](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md)。
- SUSE Linux Enterprise 软件计划：折扣覆盖范围取决于运行 SUSE 软件的 VM 的 vCPU。 有关详细信息，请参阅[了解如何应用 SUSE Linux Enterprise 软件计划折扣](billing-understand-suse-reservation-charges.md)。
- SQL 数据库预留容量：折扣覆盖范围取决于所选的性能层。 有关详细信息，请参阅[了解如何应用 Azure 预订折扣](billing-understand-reservation-charges.md)。
- Azure Cosmos DB 预留容量：折扣覆盖范围取决于预配的吞吐量。 有关详细信息，请参阅[了解如何应用 Azure Cosmos DB 预订折扣](billing-understand-cosmosdb-reservation-charges.md)。

## <a name="next-steps"></a>后续步骤

购买[预订VM 实例](../virtual-machines/windows/prepay-reserved-vm-instances.md)、[SQL 数据库保留容量](../sql-database/sql-database-reserved-capacity.md)或 [Azure Cosmos DB 保留容量](../cosmos-db/cosmos-db-reserved-capacity.md)，开始节省虚拟机费用。

若要了解有关 Azure 预订的详细信息，请参阅以下文章：

- [管理 Azure 预留项](billing-manage-reserved-vm-instance.md)
- [了解即用即付订阅的预留使用情况](billing-understand-reserved-instance-usage.md)
- [了解企业合约的预留使用情况](billing-understand-reserved-instance-usage-ea.md)
- [预留未包含的 Windows 软件成本](billing-reserved-instance-windows-software-costs.md)
- [合作伙伴中心云解决方案提供商 (CSP) 计划中的 Azure 预订](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。
