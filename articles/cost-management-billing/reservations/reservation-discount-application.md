---
title: 如何应用 Azure 预留折扣
description: 本文帮助你了解在一般情况下如何应用预留实例折扣。
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: banders
ms.openlocfilehash: cd09bb7bde759c543b066070c5d2450404949610
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82627103"
---
# <a name="how-a-reservation-discount-is-applied"></a>如何应用预留折扣

本文帮助你了解在一般情况下如何应用预留实例折扣。 预留折扣适用于与购买预留时选择的属性相匹配的资源使用情况。 属性包括匹配的 VM、SQL 数据库、Azure Cosmos DB 或其他资源的运行范围。 例如，如果需要“美国西部”区域四个标准 D2 虚拟机的预留折扣，请选择正在运行的 VM 所在的订阅。

预留折扣的性质是“不用就会失效”  。 如果你在任何小时内没有匹配资源，那么你将丢失该小时的预留数量。 不能结转未使用的预留小时数。

关闭资源时，预留折扣将自动应用于指定范围内的另一个匹配资源。 如果在指定的范围内找不到匹配的资源，则预留小时数将丢失  。

例如，你可能会在以后创建一个资源，并包含一个利用不足的匹配预留项。 预留折扣将自动应用到新的匹配资源。

如果虚拟机是在注册/帐户的不同订阅中运行，则请选择作为共享的范围。 共享范围允许跨订阅应用预留折扣。 可在购买预留后更改范围。 有关详细信息，请参阅[管理 Azure 预订](manage-reserved-vm-instance.md)。

预留折扣仅适用于与企业、Microsoft 客户协议、CSP 或采用即用即付费率的订阅相关联的资源。 订阅中运行的其他产品/服务类型的资源无法获得预订折扣。

## <a name="when-the-reservation-term-expires"></a>当预留期限过期时

预留期限结束时，计费折扣随即过期，资源将按即用即付价格计费。 默认情况下，预留未设置为自动续订。 可以通过在续订设置中选择相应的选项来选择启用预留的自动续订。 如果使用自动续订，则在现有预留到期后，将购买替换预留。 默认情况下，替换预留具有与过期预留相同的属性，你可以选择更改续订设置中的计费频率、期限或数量。 对用于计费的预留和订阅拥有所有者访问权限的任何用户都可以设置续订。  

## <a name="discount-applies-to-different-sizes"></a>折扣适用于不同的大小

购买预订时，折扣可以应用于具有同一大小组中属性的其他实例。 此功能称为“实例大小灵活性”。 折扣覆盖范围的灵活性取决于预订的类型以及购买预订时选择的特性。

服务计划：

- 虚拟机预留实例：购买预留项时，如果选择“针对实例大小灵活性进行优化”  ，则折扣覆盖范围将取决于所选的 VM 大小。 预订可以应用于同一大小系列组中的虚拟机 (VM) 大小。 有关详细信息，请参阅[预订 VM 实例的虚拟机大小灵活性](../../virtual-machines/windows/reserved-vm-instance-size-flexibility.md)。
- Azure 存储预留容量：你可以购买标准 Azure 存储帐户的预留容量（单位为每月 100 TiB 或 1 PiB）。 若要了解哪些区域支持 Azure 存储预留容量，请参阅[块 blob 定价](https://azure.microsoft.com/pricing/details/storage/blobs/)。 Azure 存储预留容量可用于所有访问层（热、冷、存档）和任何复制配置（LRS、GRS 或 ZRS）。
- SQL 数据库预留容量：折扣覆盖范围取决于所选的性能层。 有关详细信息，请参阅[了解如何应用 Azure 预订折扣](understand-reservation-charges.md)。
- Azure Cosmos DB 预留容量：折扣覆盖范围取决于预配的吞吐量。 有关详细信息，请参阅[了解如何应用 Azure Cosmos DB 预订折扣](understand-cosmosdb-reservation-charges.md)。

## <a name="how-discounts-apply-to-specific-azure-services"></a>对特定 Azure 服务应用折扣的方式

请阅读以下适用于自己的文章，了解如何对特定的 Azure 服务应用折扣：

- [应用服务](reservation-discount-app-service-isolated-stamp.md)
- [用于 Redis 的 Azure 缓存](understand-azure-cache-for-redis-reservation-charges.md)
- [Cosmos DB](understand-cosmosdb-reservation-charges.md)
- [Database for MariaDB](understand-reservation-charges-mariadb.md)
- [Database for MySQL](understand-reservation-charges-mysql.md)
- [Database for PostgreSQL](understand-reservation-charges-postgresql.md)
- [Databricks](reservation-discount-databricks.md)
- [数据资源管理器](understand-azure-data-explorer-reservation-charges.md)
- [专用主机](billing-understand-dedicated-hosts-reservation-charges.md)
- [磁盘存储](understand-disk-reservations.md)
- [Red Hat Linux Enterprise](understand-rhel-reservation-charges.md)
- [软件计划](understand-suse-reservation-charges.md)
- [存储](understand-storage-charges.md)
- [SQL 数据库](understand-reservation-charges.md)
- [Azure Synapse Analytics](reservation-discount-azure-sql-dw.md)
- [虚拟机](../manage/understand-vm-reservation-charges.md)


## <a name="next-steps"></a>后续步骤

- [管理 Azure 预留项](manage-reserved-vm-instance.md)
- [了解采用即用即付费率的订阅的预留项使用情况](understand-reserved-instance-usage.md)
- [了解企业合约的预留使用情况](understand-reserved-instance-usage-ea.md)
- [预留未包含的 Windows 软件成本](reserved-instance-windows-software-costs.md)