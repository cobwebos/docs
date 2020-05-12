---
title: 使用 Azure 预留容量节省 Azure Synapse Analytics 费用
description: 了解如何通过省钱的预留容量节省 Azure Synapse Analytics 的费用成本。
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: banders
ms.openlocfilehash: 3706493f0132cd4cc57af589e0d2935c31d6657e
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629144"
---
# <a name="save-costs-for-azure-synapse-analytics-charges-with-reserved-capacity"></a>使用预留容量节省 Azure Synapse Analytics 费用成本

通过承诺使用一到三年的 cDWU 预留量，你可以节省 Azure Synapse Analytics 的费用。 若要购买 Azure Synapse Analytics 预留容量，需要选择 Azure 区域和期限。 然后，将 Azure Synapse Analytics SKU 添加到购物车，并选择要购买的 cDWU 单位数量。

在购买预留容量后，与预留属性匹配的 Azure Synapse Analytics 用量不再按即用即付费率收费。

预留不涵盖与 Azure Synapse Analytics 用量相关的存储或网络费用。

当预留容量到期时，Azure Synapse Analytics 实例会继续运行，但会按即用即付费率计费。 预留容量不会自动续订。

有关定价信息，请参阅 [Azure Synapse Analytics 预留容量套餐](https://azure.microsoft.com/pricing/details/synapse-analytics/)。

可以在 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)中购买 Azure Synapse Analytics 预留容量。 通过[提前付款或按月付款](monthly-payments-reservations.md)的方式为预留付款。 购买预留容量：

- 你必须拥有至少一个企业或即用即付订阅的“所有者”角色。
- 对于企业订阅，必须在 [EA 门户](https://ea.azure.com/)中启用“添加预留实例”选项  。 如果禁用了该设置，则必须是 EA 管理员。
- 对于云解决方案提供商 (CSP) 计划，只有管理员代理或销售代理可以购买 Azure Synapse Analytics 预留容量。

有关预留购买如何针对企业客户和即用即付客户进行计费的详细信息，请参阅[了解适用于企业合约的 Azure 预留使用情况](understand-reserved-instance-usage-ea.md)和[了解即用即付订阅的 Azure 预留使用情况](understand-reserved-instance-usage.md)。

## <a name="choose-the-right-size-before-purchase"></a>在购买之前选择合适的大小

Azure Synapse Analytics 预留大小应该基于你消耗的计算数据仓库单位 (cDWU) 总计。 购买按 100 cDWU 增量进行。

例如，假设你对 Azure Synapse Analytics 的总消耗量为 DW3000c， 你希望针对整个消耗量购买预留容量。 因此，应购买 30 个单位的 cDWU 预留容量。

## <a name="buy-azure-synapse-analytics-reserved-capacity"></a>购买 Azure Synapse Analytics 预留容量

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 选择“所有服务”   > “预订”  。
3. 选择一个订阅。 使用“订阅”列表，选择用于支付预留容量费用的订阅。 将向订阅的付款方式收取预留容量的费用。 订阅类型必须为企业协议（套餐编号：MS-AZR-0017P 或 MS-AZR-0148P），或即用即付（产品/服务编号：MS-AZR-0003P 或 MS-AZR-0023P）。
   - 对于企业订阅，从注册的货币承诺余额中扣除费用或作为超额收取费用。
   - 对于即用即付订阅，将向订阅的信用卡或发票付款方式收取费用。
4. 选择一个范围。 使用“范围”列表来选择订阅范围。
   - **单个资源组范围** - 仅将预留折扣应用到所选资源组中匹配的资源。
   - **单个订阅范围** - 将预留折扣应用到所选订阅中匹配的资源。
   - **共享范围** - 将预留折扣应用到计费上下文中符合条件的订阅中的匹配资源。 对于企业协议客户，计费上下文为注册。 对于采用即用即付费率的单个订阅，计费范围是由帐户管理员创建的所有符合条件的订阅。
   - 对于企业客户，计费上下文为 EA 注册。
   - 对于即用即付客户，共享范围是由帐户管理员创建的所有即用即付订阅。
5. 选择“区域”，以便选择一个预留容量涵盖的 Azure 区域。
6. 选择数量。 输入数量 100，作为要购买的数据仓库单位数 (cDWU)。    
   例如，数量为 30 时，会为你提供每小时 3,000 cDWU 的预留容量。
7. 在“成本”  部分查看 Azure Synapse Analytics 预留容量的预留成本。
8. 选择“购买”。 
9. 选择“查看此预留”  以查看购买状态。

## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

可以在一定的限制下对预留执行取消、交换或退款操作。 有关详细信息，请参阅 [Azure 预留的自助交换和退款](exchange-and-refund-azure-reservations.md)。

预留折扣自动应用于与 Azure Synapse Analytics 预留容量范围和区域匹配的 Azure Synapse Analytics 实例数目。 可以通过 [Azure 门户](https://portal.azure.com/)、PowerShell、CLI 或 API 更新 Azure Synapse Analytics 预留容量的范围。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/)。

## <a name="next-steps"></a>后续步骤

- 若要详细了解预留折扣如何应用于 Azure Synapse Analytics，请参阅[预留折扣如何应用于 Azure Synapse Analytics](prepay-sql-data-warehouse-charges.md)。

- 若要了解有关 Azure 预订的详细信息，请参阅以下文章：
  - [什么是 Azure 预订？](save-compute-costs-reservations.md)
  - [管理 Azure 预留项](manage-reserved-vm-instance.md)
  - [了解 Azure 预留折扣](understand-reservation-charges.md)
  - [了解即用即付订阅的预留使用情况](understand-reserved-instance-usage.md)
  - [了解企业合约的预留使用情况](understand-reserved-instance-usage-ea.md)
