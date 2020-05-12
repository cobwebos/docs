---
title: 什么是 Azure 预留项？
description: 了解 Azure 预留项和定价以节省虚拟机、SQL 数据库、Azure Cosmos DB 和其他资源的成本。
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: banders
ms.openlocfilehash: ae94a5c8d7e9ff93098e85535aea3699206b9e03
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82626951"
---
# <a name="what-are-azure-reservations"></a>什么是 Azure 预留项？

Azure 预留项通过承诺多种产品的一年期或三年期计划，帮助你节省资金。 通过承诺预付，能够以折扣价购买所用资源。 预留可显著将资源的成本，最多减少至即用即付价格的 72%。 预订提供计费折扣，并且不会影响资源的运行时状态。 购买预留项后，折扣会自动应用于匹配的资源。

可以提前或每月为预留项付费。 预付和每月预留的总费用相同，选择每月支付不会产生额外的费用。 每月付款适用于 Azure 预留项，而不适用于第三方产品。

可以在 Azure 门户 ([https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)) 中购买预留项。

## <a name="why-buy-a-reservation"></a>为何购买预留项？

如果你持续产生的资源用量可以享受预留权益，则购买预留项便有机会降低成本。 例如，如果持续运行某个服务实例，在不购买预留项的情况下，需按即用即付费率付费。 如果购买预留项，则将立即获得预留折扣。 这些资源不再按即用即付费率收费。

## <a name="how-reservation-discount-is-applied"></a>如何应用预留折扣

购买后，预留折扣会自动应用于与购买预留项时所选的属性匹配的资源用量。 属性包括 SKU、区域（如果适用）和范围。 预留范围选择预留节省的应用场合。

有关折扣应用方式的详细信息，请参阅[预留实例折扣的应用](reservation-discount-application.md)。

有关预留范围工作原理的详细信息，请参阅[范围预留](prepare-buy-reservation.md#scope-reservations)。

## <a name="determine-what-to-purchase"></a>确定要购买的内容 

除 Azure Databricks 以外的所有预留项都是按小时应用的。 请根据一贯的基本用量考虑如何购买预留项。 可以通过分析用量数据或使用预留建议，来确定要购买哪种预留项。 以下位置提供了建议：

- Azure 顾问（仅限 VM）
- Azure 门户中的预留项购买体验
- 成本管理 Power BI 应用
- API 

有关详细信息，请参阅 [确定要购买哪种预留项](determine-reservation-purchase.md) 

## <a name="buying-a-reservation"></a>购买预留项 

可以通过 Azure 门户、API、PowerShell 和 CLI 购买预留项。 

转到 Azure 门户购买预留项 (https://ms.portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Docs) 

有关详细信息，请参阅 [购买预留项](prepare-buy-reservation.md) 

## <a name="how-is-a-reservation-billed"></a>预留如何计费？ 

预留通过与订阅绑定的付款方式付款。 预留成本将从货币承诺余额中扣除（如果可用）。 如果货币承诺余额不足以支付虚拟机预留实例的费用，则还会向你收取所欠费用。 如果你的订阅来自采用即用即付费率的单个计划，将立即从你帐户中的信用卡提前扣取购买费。 每月付款额将显示在发票上，并且每月将从你的信用卡扣费。 如果采用按发票计费的方式，则费用将显示在下一份发票上。 

## <a name="permissions-to-view-and-manage-reservations"></a>预留项的查看和管理权限 

购买预留项的用户，以及用于计收预留项费用的订阅的帐户管理员，将获得预留订单和预留项的“所有者”角色。

可以通过将用户添加到预留订单或预留的角色来委托预留管理。 可以在 Azure 门户中或使用 API 和 PowerShell 分配角色。 

有关详细信息，请参阅 [添加或更改可以管理预留项的用户](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation)。 

## <a name="get-reservation-details-and-utilization-after-purchase"></a>购买后获取预留项详细信息和利用率

如果你有权查看某个预留项，可以在 Azure 门户中查看该预留项及其用量。 也可以使用 API 获取这些数据。 

有关如何在 Azure 门户中查看预留项的详细信息，请参阅 [在 Azure 门户中查看预留项](view-reservations.md) 

## <a name="manage-reservations-after-purchase"></a>购买后管理预留项 

购买某个 Azure 预留项后，可将该预留项的应用范围更新为不同的订阅、更改可以管理该预留项的人员、将预留项拆分为较小的部分，或更改实例大小灵活性。 

有关详细信息，请参阅 [管理 Azure 资源的预留项](manage-reserved-vm-instance.md) 

## <a name="flexibility-with-azure-reservations"></a>Azure 预留项的灵活性

Azure 预留可以灵活地满足你不断变化的需求。 可以用一个预留来交换同一类型的另一个预留。 如果你不再需要某个预留项，也可以申请退款，在 12 个月的滚动期限内最多可以退回 50,000 美元。 退款最大上限应用于与 Microsoft 签订的协议范围内的所有预留。

有关详细信息，请参阅 [Azure 预留项的自助交换和退款](exchange-and-refund-azure-reservations.md)


## <a name="charges-covered-by-reservation"></a>预留项涵盖的费用

- **虚拟机预留实例** - 预留项仅涵盖虚拟机和云服务的计算成本。 它不涵盖其他软件、Windows、网络或存储费用。
- **Azure 存储预留容量** - 该预留项涵盖用于 Blob 存储或 Azure Data Lake Gen2 存储的标准存储帐户的存储容量。 不涵盖带宽或事务费率。
- **Azure Cosmos DB 预留容量** - 预留项涵盖为资源预配的吞吐量的费用。 但它不涵盖存储和网络费用。
- **SQL 数据库预留 vCore** - 预留项仅包含计算成本。 SQL 许可证单独计费。
- **Azure Synapse Analytics** - 预留项涵盖 cDWU 使用量。 它不涵盖与 Azure Synapse Analytics 使用量相关的存储或网络费用。
- **Azure Databricks** - 预留项仅涵盖 DBU 使用费。 计算、存储和网络等其他费用单独计收。
- **应用服务印花费** - 预留项涵盖印花使用费。 它不适用于辅助角色，因此与印花相关的任何其他资源将单独收费。
- **Azure Database for MySQL** - 预留项仅包含计算费用。 预留容量不包括与 MySQL 数据库服务器关联的软件、网络或存储费用。
- **Azure Database for PostgreSQL** - 预留项仅包含计算费用。 预留容量不包括与 PostgreSQL 数据库服务器关联的软件、网络或存储费用。
- **Azure Database for MariaDB** - 预留项仅包含计算费用。 预留容量不包括与 MariaDB 数据库服务器关联的软件、网络或存储费用。
- **Azure 数据资源管理器** - 预留项涵盖标记费用。 预留项不适用于与群集关联的计算、网络或存储费用。
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
