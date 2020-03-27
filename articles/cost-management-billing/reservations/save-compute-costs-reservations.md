---
title: 什么是 Azure 预留项？
description: 了解 Azure 预留项和定价以节省虚拟机、SQL 数据库、Azure Cosmos DB 和其他资源的成本。
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: banders
ms.openlocfilehash: 6277a7e7dc5891a3bc67c298a31344284c92e31d
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2020
ms.locfileid: "80235632"
---
# <a name="what-are-azure-reservations"></a>什么是 Azure 预留项？

Azure 预留项可帮助你节省资金，因为它可以承诺多种产品的一年期或三年期计划。 通过承诺预付，能够以折扣价购买所用资源。 预留可显著将资源的成本，最多减少至即用即付价格的 72%。 预订提供计费折扣，并且不会影响资源的运行时状态。 购买预留项后，折扣会自动应用于匹配的资源。

可以提前或每月为预留项付费。 预付和每月预留的总费用相同，选择每月支付不会产生额外的费用。 每月付款适用于 Azure 预留项，而不适用于第三方产品。

可在 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)中购买预留。

## <a name="why-buy-a-reservation"></a>为何购买预留项？

如果你持续产生的资源用量可以享受预留权益，则购买预留项便有机会降低成本。 例如，如果持续运行某个服务实例，在不购买预留项的情况下，需按即用即付费率付费。 如果购买预留项，则将立即获得预留折扣。 这些资源不再按即用即付费率收费。

## <a name="how-reservation-discount-is-applied"></a>如何应用预留折扣

购买后，预留折扣会自动应用于与购买预留项时所选的属性匹配的资源用量。 属性包括 SKU、区域（如果适用）和范围。 预留范围选择预留节省的应用场合。

有关折扣应用方式的详细信息，请参阅[预留实例折扣的应用](reservation-discount-application.md)。

有关预留范围工作原理的详细信息，请参阅[范围预留](prepare-buy-reservation.md#scope-reservations)。


## <a name="flexibility-with-azure-reservations"></a>Azure 预留项的灵活性

Azure 预留可以灵活地满足你不断变化的需求。 可以用一个预留来交换同一类型的另一个预留。 如果你不再需要某个预留项，也可以申请退款，在 12 个月的滚动期限内最多可以退回 50,000 美元。 退款最大上限应用于与 Microsoft 签订的协议范围内的所有预留。

有关详细信息，请参阅 [Azure 预留项的自助交换和退款](exchange-and-refund-azure-reservations.md)


## <a name="charges-covered-by-reservation"></a>预留项涵盖的费用

- **虚拟机预留实例** - 预留项仅涵盖虚拟机计算成本。 它不涵盖其他软件、Windows、网络或存储费用。
- **Azure 存储预留容量** - 该预留项涵盖用于 Blob 存储或 Azure Data Lake Gen2 存储的标准存储帐户的存储容量。 不涵盖带宽或事务费率。
- **Azure Cosmos DB 预留容量** - 预留项涵盖为资源预配的吞吐量的费用。 但它不涵盖存储和网络费用。
- **SQL 数据库预留 vCore** - 预留项仅包含计算成本。 SQL 许可证单独计费。
- **SQL 数据仓库** - 预留项涵盖 cDWU 用量。 它不涵盖与 SQL 数据仓库用量相关的存储或网络费用。
- **Azure Databricks** - 预留项仅涵盖 DBU 使用费。 计算、存储和网络等其他费用单独计收。
- **应用服务印花费** - 预留项涵盖印花使用费。 它不适用于辅助角色，因此与印花相关的任何其他资源将单独收费。
- **Azure Database for MySQL** - 预留项仅包含计算费用。 预留容量不包括与 MySQL 数据库服务器关联的软件、网络或存储费用。
- **Azure Database for PostgreSQL** - 预留项仅包含计算费用。 预留容量不包括与 PostgreSQL 数据库服务器关联的软件、网络或存储费用。
- **Azure Database for MariaDB** - 预留项仅包含计算费用。 预留容量不包括与 MariaDB 数据库服务器关联的软件、网络或存储费用。
- **Azure 数据资源管理器** - 预留项涵盖标记费用。 预留项不涵盖与群集关联的计算、网络或存储费用。
- **Azure Cache for Redis** - 预留仅包括计算费用。 预留不包括与 Redis 缓存实例相关联的网络或存储费用。
- **Azure 专用主机** - 专用主机仅包括计算成本。
- **Azure 磁盘存储预留项** - 预留项仅包括大小为 P30 或更高级别的高级 SSD。 它不涵盖小于 P30 的任何其他磁盘类型或大小。

软件计划：

- **SUSE Linux** - 预留项涵盖软件计划费用。 折扣仅适用于 SUSE 计量，而不适用于虚拟机的使用。
- **Red Hat 计划** - 预留项涵盖软件计划费用。 折扣仅适用于 RedHat 计量，而不适用于虚拟机的使用。
- **Azure VMware Solution by CloudSimple** - 预留项涵盖 VMWare CloudSimple 节点。 仍会收取额外的软件费用。
- **Azure Red Hat OpenShift** - 预留项适用于 OpenShift 费用，而不适用于 Azure 基础结构费用。

对于 Windows 虚拟机和 SQL 数据库，预留折扣不适用于软件费用。 可以通过 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)来涵盖许可费用。


## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Azure 预订的详细信息，请参阅以下文章：
    - [管理 Azure 预留项](manage-reserved-vm-instance.md)
    - [了解采用即用即付费率的订阅的预留项使用情况](understand-reserved-instance-usage.md)
    - [了解企业合约的预留使用情况](understand-reserved-instance-usage-ea.md)
    - [预留未包含的 Windows 软件成本](reserved-instance-windows-software-costs.md)
    - [合作伙伴中心云解决方案提供商 (CSP) 计划中的 Azure 预订](/partner-center/azure-reservations)

- 详细了解服务计划的预留项：
    - [包含 Azure 虚拟机预留实例的虚拟机](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [具有 Azure Cosmos DB 预留容量的 Azure Cosmos DB 资源](../../cosmos-db/cosmos-db-reserved-capacity.md)
    - [具有 Azure SQL 数据库预留容量的 SQL 数据库计算资源](../../sql-database/sql-database-reserved-capacity.md)详细了解软件计划的预留项：
    - [Azure 预留项中的 Red Hat 软件计划](../../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [Azure 预留项中的 SUSE 软件计划](../../virtual-machines/linux/prepay-suse-software-charges.md)
