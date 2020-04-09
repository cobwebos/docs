---
title: 购买 Azure 预留
description: 了解帮助购买 Azure 预留项的要点。
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: banders
ms.openlocfilehash: 1f44b0a0e2278bfcfbbf7cde1a3605fe334eaa3f
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886511"
---
# <a name="buy-a-reservation"></a>购买预留项

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
- **共享范围** — 将预留折扣应用到计费上下文中符合条件的订阅中的匹配资源。 对于企业协议客户，计费上下文为注册。 对于 Microsoft 客户协议客户，计费范围为计费配置文件。 对于采用即用即付费率的单个订阅，计费范围是由帐户管理员创建的所有符合条件的订阅。

对用途应用预留折扣时，Azure 将按以下顺序处理预留：

1. 范围限定为资源组的预留项
2. 单一范围的预留项
3. 共享范围的预留项

始终可在购买预留项后更新范围。 为此，请转到该预留项，单击“配置”，然后重新设置预留范围。  重新设置预留范围不属于商业交易。 预留条款不会有变化。 有关更新范围的详细信息，请参阅[在购买预留项后更新范围](manage-reserved-vm-instance.md#change-the-reservation-scope)。

![演示如何更改预留范围的示例](./media/prepare-buy-reservation/rescope-reservation-resource-group.png)

## <a name="discounted-subscription-and-offer-types"></a>打折的订阅和套餐类型

预留折扣适用于以下符合条件的订阅和套餐类型。

- 企业协议（套餐编号：MS-AZR-0017P 或 MS-AZR-0148P）
- Microsoft 客户协议订阅。
- 采用即用即付费率的单个计划（套餐编号：MS-AZR-0003P 或 MS-AZR-0023P）
- CSP 订阅

订阅中运行的其他产品/服务类型的资源无法获得预订折扣。

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

## <a name="buy-reservations-with-monthly-payments"></a>按月付款的方式购买预留项

可以通过按月付款的方式购买预留项。 与需要全额付款的提前付款购买不同，按月付款选项将预留的总费用平均分配到期限的每个月。 预付和每月预留的总费用相同，选择每月支付不会产生额外的费用。

如果使用 Microsoft 客户协议 (MCA) 购买预留项，则每月付款金额可能会有所不同，具体取决于本地货币的当月市场汇率。

每月付款不适用于：Databricks、SUSE Linux 预留项、Red Hat 计划和 Azure Red Hat OpenShift 计算。

### <a name="view-payments-made"></a>查看已进行的付款

可以查看通过 API、使用数据和成本分析进行的付款。 对于按月付款的预留，频率值在使用数据和预留费用 API 中显示为“定期”。  对于提前付款的预留，该值显示为“一次性”。 

成本分析在默认视图中显示按月购买。 对“费用类型”应用“购买”筛选器，对“频率”应用“定期”筛选器，即可查看所有购买项。     若只查看预留，请应用“预留”筛选器。 

![在成本分析中显示预留购买成本的示例](./media/prepare-buy-reservation/cost-analysis.png)

### <a name="exchange-and-refunds"></a>交换和退款

与其他预留一样，可以通过每月计费对购买的预留执行退款或交换操作。 

交换一个按月付款的预留时，新购买的生存期费用总计应该大于已取消的针对退回的预留的剩余付款。 对于交换，没有任何其他限制或费用。 可以交换一个提前付款的预留，购买一个新的按月计费的预留。 但是，新预留的生存期价值应该大于退回的预留的按比例计算的价值。

如果取消某个按月付款的预留项，已取消的将来付款会计入到 50,000 美元退款限额。

有关交换和退款的详细信息，请参阅 [Azure 预留的自助交换和退款](exchange-and-refund-azure-reservations.md)。

## <a name="reservation-notifications"></a>预留通知

根据 Azure 订阅的付款方式，我们将向组织中的以下用户发送预留电子邮件通知。 我们会针对各种事件发送通知，包括： 

- 购买
- 预留即将过期
- Expiry
- 续订
- 取消
- 范围更改

对于使用 EA 订阅的客户：

- 通知只会发送到 EA 通知联系人。
- 使用 RBAC (IAM) 权限添加到预留项的用户不会收到任何电子邮件通知。

对于使用单个订阅的客户：

- 购买者会收到购买通知。
- 在购买时，订阅计费帐户所有者将收到购买通知。
- 帐户所有者将收到所有其他通知。

## <a name="next-steps"></a>后续步骤

- [管理 Azure 资源的预留](manage-reserved-vm-instance.md)
