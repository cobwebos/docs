---
title: 准备购买 Azure 预留项
description: 了解在购买 Azure 预留项之前所要注意的要点。
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: banders
ms.openlocfilehash: 1f5ca2d43356eab98cffe8414c00d97e5744739a
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2020
ms.locfileid: "80235653"
---
# <a name="prepare-to-buy-a-reservation"></a>准备购买预留项

Azure 预留项可帮助你节省资金，因为它可以承诺许多 Azure 资源的一年期或三年期计划。 在正式承诺购买预留项之前，请务必查看以下部分来做好购买准备。

## <a name="who-can-buy-a-reservation"></a>谁可以购买预留项

若要购买计划，必须在企业订阅（MS-AZR-0017P 或 MS-AZR-0148P）或即用即付订阅（MS-AZR-0003P 或 MS-AZR-0023P）或 Microsoft 客户协议订阅中具有订阅所有者角色。 云解决方案提供商可使用 Azure 门户或 [合作伙伴中心](/partner-center/azure-reservations)购买 Azure 预留项。

企业协议 (EA) 客户可以通过在 EA 门户中禁用“添加预留实例”选项，将购买权限制为 EA 管理员。  EA 管理员必须是至少一个 EA 订阅的订阅所有者才能购买预留项。 对于希望通过一个核心团队为不同的成本中心购买预留项的企业而言，该选项非常有用。 购买后，核心团队可将成本中心所有者添加到预留项。 然后，所有者可将预留范围限定为其订阅。 购买预留项时，核心团队无需拥有订阅所有者访问权限。

预留折扣仅适用于与通过企业、云解决方案提供商 (CSP)、采用即用即付费率的 Microsoft 客户协议和单个计划购买的订阅关联的资源。

## <a name="scope-reservations"></a>限定预留范围

可将预留范围限定为订阅或资源组。 设置预留范围可以选择要在哪些方面应用预留节省。 将预留范围限定为某个资源组时，预留折扣仅应用到该资源组 — 而不会应用到整个订阅。

### <a name="reservation-scoping-options"></a>预留范围选项

可以根据需要使用三个选项来限定预留范围：

- **单个资源组范围** — 仅将预留折扣应用到所选资源组中匹配的资源。
- **单个订阅范围** — 将预留折扣应用到所选订阅中匹配的资源。
- **共享范围** — 将预留折扣应用到计费上下文中符合条件的订阅中的匹配资源。 对于企业协议客户，计费上下文为注册。 对于 Microsoft 客户协议客户，计费范围为计费对象信息。 对于采用即用即付费率的单个订阅，计费范围是由帐户管理员创建的所有符合条件的订阅。

对用途应用预留折扣时，Azure 将按以下顺序处理预留：

1. 范围限定为资源组的预留项
2. 单一范围的预留项
3. 共享范围的预留项

根据预留项的限定范围，单个资源组可从多个预留项获得预留折扣。

始终可在购买预留项后更新范围。 为此，请转到该预留项，单击“配置”，然后重新设置预留范围。  重新设置预留范围不属于商业交易。 预留条款不会有变化。 有关更新范围的详细信息，请参阅[在购买预留项后更新范围](manage-reserved-vm-instance.md#change-the-reservation-scope)。

![演示如何更改预留范围的示例](./media/prepare-buy-reservation/rescope-reservation-resource-group.png)

## <a name="purchase-reservations"></a>购买预留项

可以通过 Azure 门户、API、PowerShell、CLI 购买预留项。 准备好购买预留项时，请阅读以下适用于自己的文章：

- [应用服务](prepay-app-service-isolated-stamp.md)
- [用于 Redis 的 Azure 缓存](../../azure-cache-for-redis/cache-reserved-pricing.md)
- [Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)
- [Databricks](prepay-databricks-reserved-capacity.md)
- [数据资源管理器](../../data-explorer/pricing-reserved-capacity.md)
- [磁盘存储](../../virtual-machines/linux/disks-reserved-capacity.md)
- [专用主机](../../virtual-machines/prepay-dedicated-hosts-reserved-instances.md)
- [软件计划](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [存储](../../storage/blobs/storage-blob-reserved-capacity.md)
- [SQL 数据库](../../sql-database/sql-database-reserved-capacity.md)
- [SQL 数据仓库](prepay-sql-data-warehouse-charges.md)
- [虚拟机](../../virtual-machines/windows/prepay-reserved-vm-instances.md)

## <a name="next-steps"></a>后续步骤

- [管理 Azure 资源的预留](manage-reserved-vm-instance.md)
