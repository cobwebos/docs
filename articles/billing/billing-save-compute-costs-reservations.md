---
title: 什么是 Azure 预留项？ | Microsoft Docs
description: 了解 Azure 预订和定价以节省虚拟机、SQL 数据库、Azure Cosmos DB 和其他资源的成本。
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: banders
ms.openlocfilehash: 1349a05e1dd235c7b375335ae2c9fed16170a61f
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58649384"
---
# <a name="what-are-azure-reservations"></a>什么是 Azure 预留项？

通过预付一年或三年的虚拟机、SQL 数据库计算容量、Azure Cosmos DB 吞吐量或其他 Azure 资源的费用，Azure 预订可为你节省资金。 通过预付，能够以折扣价购买所用资源。 预订可将虚拟机、SQL 数据库计算、Azure Cosmos DB 或其他资源的成本大幅减少至即用即付价格的 72%。 预订提供计费折扣，并且不会影响资源的运行时状态。

可在 [Azure 门户](https://aka.ms/reservations)中购买预留。 有关详细信息，请参阅以下文章：

服务计划：
- [使用 Azure 保留的 VM 实例的虚拟机](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [使用 Azure Cosmos DB 的 azure Cosmos DB 资源保留容量](../cosmos-db/cosmos-db-reserved-capacity.md)
- [使用 Azure SQL 数据库的 SQL 数据库计算资源保留容量](../sql-database/sql-database-reserved-capacity.md)

软件计划：
- [Azure 保留项中的 Red Hat 软件计划](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [从 Azure 预订 SUSE 软件计划](../virtual-machines/linux/prepay-suse-software-charges.md)

## <a name="why-buy-a-reservation"></a>为什么购买预订吗？

如果有虚拟机、 Azure Cosmos DB 或长时间运行的 SQL 数据库，购买预订提供最经济实惠的选项。 例如，在持续运行四个没有预留容量的服务实例时，我们即用即付费率收费。 如果你购买保留这些资源，你立即获得预订折扣。 这些资源不再按即用即付费率收费。

## <a name="charges-covered-by-reservation"></a>预订涵盖的费用

服务计划：

- 虚拟机预留实例：预留仅涵盖虚拟机计算成本。 而不涵盖软件、网络或存储等其他费用。
- Azure Cosmos DB 预留容量：预订涵盖资源的预配的吞吐量。 但它不涵盖存储和网络费用。
- SQL 数据库预留 vCore：预留仅包含计算成本。 许可证单独计费。
- Azure Cosmos DB 预留容量：预留涵盖为资源预配的吞吐量的费用，而不涵盖存储和网络费用。

对于 Windows 虚拟机和 SQL 数据库，可以利用 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)来涵盖许可费用。

## <a name="whos-eligible-to-purchase-a-reservation"></a>谁有资格购买预留？

若要购买计划，必须具有 （MS-条-0017 P 或 MS-条-0148 P） 的企业或即用即付订阅 （MS-条-003 P 或 MS-条-0023 P） 中的订阅所有者角色。 云解决方案提供商可以使用 Azure 门户或 [合作伙伴中心](/partner-center/azure-reservations) 购买 Azure 保留项。

EA 客户可以通过禁用限制到 EA 管理员购买**添加保留实例**EA 门户中的选项。 EA 管理员必须至少一个 EA 订阅是订阅所有者才能购买预订。 选项为适用于想要购买的不同的成本中心保留一个集中式的团队企业非常有用。 在购买集中式的团队可以将成本中心所有者添加到保留项。 所有者可以然后范围限定到自己的订阅保留。 中心团队不需要购买预订时具有订阅所有者的访问。

预订折扣仅适用于与企业、即用即付或 CSP 订阅类型相关联的资源。

## <a name="how-is-a-reservation-billed"></a>预留如何计费？

预留通过与订阅绑定的付款方式付款。 如果订阅为“企业”订阅，则虚拟机预留实例费用会从货币承诺余额中扣除。 如果货币承诺余额不足以支付虚拟机预留实例的费用，则还会向你收取所欠费用。 如果订阅为“即用即付”订阅，则会立即从帐户中的信用卡扣费。 如果采用账单付费的方式，则费用显示在下次的账单上。

## <a name="how-reservation-discount-is-applied"></a>如何应用预订折扣

预留折扣适用于与购买预留时选择的特性相匹配的资源使用情况。 这些特性包括匹配的 VM、SQL 数据库、Azure Cosmos DB 或其他资源的运行范围。 例如，如果需要“美国西部”区域四个标准 D2 虚拟机的预留折扣，请选择正在运行的 VM 所在的订阅。 如果虚拟机是在注册/帐户的不同订阅中运行，则请选择作为共享的范围。 共享范围允许跨订阅应用预留折扣。 可在购买预留后更改范围。 有关详细信息，请参阅[管理 Azure 预订](billing-manage-reserved-vm-instance.md)。

预订折扣仅适用于与企业、即用即付或 CSP 订阅类型相关联的资源。 订阅中运行的其他产品/服务类型的资源无法获得预订折扣。

若要更好地了解如何保留项影响计费，请参阅以下文章：

服务计划：

- [了解 Azure 虚拟机预订实例折扣](billing-understand-vm-reservation-charges.md)
- [了解 Azure 预订折扣](billing-understand-vm-reservation-charges.md)
- [了解 Azure Cosmos DB 预订折扣](billing-understand-cosmosdb-reservation-charges.md)

软件计划：

- [了解 Azure 预订折扣和使用情况的 Red Hat](billing-understand-rhel-reservation-charges.md)
- [了解 Azure 预订折扣和 SUSE 的使用情况](billing-understand-suse-reservation-charges.md)

## <a name="when-the-reservation-term-expires"></a>预订期到期时

预订期限结束时，计费折扣随即过期，并且虚拟机、SQL 数据库、Azure Cosmos DB 或其他资源将按即用即付的价格计费。 Azure 预订不会自动续订。 若要继续获得计费折扣，必须为符合条件的服务和软件购买新的预订。

## <a name="discount-applies-to-different-sizes"></a>折扣适用于不同的大小

购买预订时，折扣可以应用于具有同一大小组中属性的其他实例。 此功能被称为实例大小灵活性。 折扣覆盖范围的灵活性取决于预订的类型以及购买预订时选择的特性。

服务计划：

- 虚拟机预留实例：在购买预订并选中**针对优化**:**实例大小灵活性**，折扣覆盖率取决于你选择的 VM 大小。 预订可以应用于同一大小系列组中的虚拟机 (VM) 大小。 有关详细信息，请参阅[预订 VM 实例的虚拟机大小灵活性](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md)。
- SQL 数据库预留容量：折扣覆盖范围取决于所选的性能层。 有关详细信息，请参阅[了解如何应用 Azure 预订折扣](billing-understand-reservation-charges.md)。
- Azure Cosmos DB 预留容量：折扣覆盖范围取决于预配的吞吐量。 有关详细信息，请参阅[了解如何应用 Azure Cosmos DB 预订折扣](billing-understand-cosmosdb-reservation-charges.md)。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如果有疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- 购买[预订VM 实例](../virtual-machines/windows/prepay-reserved-vm-instances.md)、[SQL 数据库保留容量](../sql-database/sql-database-reserved-capacity.md)或 [Azure Cosmos DB 保留容量](../cosmos-db/cosmos-db-reserved-capacity.md)，开始节省虚拟机费用。
- 若要了解有关 Azure 预订的详细信息，请参阅以下文章：
    - [管理 Azure 预留项](billing-manage-reserved-vm-instance.md)
    - [了解即用即付订阅的预留使用情况](billing-understand-reserved-instance-usage.md)
    - [了解企业合约的预留使用情况](billing-understand-reserved-instance-usage-ea.md)
    - [预留未包含的 Windows 软件成本](billing-reserved-instance-windows-software-costs.md)
    - [合作伙伴中心云解决方案提供商 (CSP) 计划中的 Azure 预订](https://docs.microsoft.com/partner-center/azure-reservations)
