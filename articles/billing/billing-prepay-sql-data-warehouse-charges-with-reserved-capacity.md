---
title: 利用 Azure 保留容量节省 SQL 数据仓库费用
description: 了解如何通过保留容量节省 SQL 数据仓库费用，以节省资金。
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.openlocfilehash: 381a709c74f5fcf6bb1f89f07ad84d5e3af0c5e0
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806279"
---
# <a name="save-costs-for-sql-data-warehouse-charges-with-reserved-capacity"></a>采用预留容量节省 SQL 数据仓库的成本

通过在一年或三年的持续时间内提交 cDWU 使用情况，可以通过 Azure SQL 数据仓库节省资金。 若要购买 SQL 数据仓库保留容量，需要选择 Azure 区域和术语。 然后，将 SQL 数据仓库 SKU 添加到购物车，并选择要购买的 cDWU 单位数量。

在你购买预留容量后，与预留属性匹配的 SQL 数据仓库使用量不再按即用即付费率进行收费。

预留容量不包括与 SQL 数据仓库使用量相关的存储或网络费用。

如果预留容量到期，SQL 数据仓库实例会继续运行，但按即用即付的费率进行计费。 预留容量不会自动续订。

有关定价信息，请参阅[SQL 数据仓库保留容量产品](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/)。

可以在[Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)中购买 Azure SQL 数据仓库保留容量。 提前支付预订费用，[按月](billing-monthly-payments-reservations.md)支付。 购买预留容量：

- 你必须具有至少一个企业或即用即付订阅的所有者角色。
- 对于企业订阅，必须在[EA 门户](https://ea.azure.com/)中启用 "**添加保留实例**" 选项。 如果禁用此设置，则必须是 EA 管理员。
- 对于云解决方案提供商（CSP）程序，只有管理代理或销售代理可以购买 SQL 数据仓库保留容量。

有关企业客户和即用即付客户如何针对预订购买付费的详细信息，请参阅[了解企业注册的 azure 保留使用情况](billing-understand-reserved-instance-usage-ea.md)和[了解 azure 保留使用情况即用即付订阅](billing-understand-reserved-instance-usage.md)。

## <a name="choose-the-right-size-before-purchase"></a>在购买前选择合适的大小

SQL 数据仓库预留大小应基于你使用的总计算数据仓库单位（cDWU）。 以 100 cDWU 的增量进行购买。

例如，假设 SQL 数据仓库的总消耗为 DW3000c。 你需要为所有 it 购买保留容量。 因此，应购买30个单位的 cDWU 保留容量。

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>购买 SQL 数据仓库保留容量

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 选择“所有服务” > “预订”。
3. 选择一个订阅。 使用 "订阅" 列表选择用于支付预留容量的订阅。 订阅的付款方式支付了预留容量的费用。 订阅类型必须为企业协议（套餐编号：MS-AZR-0017P 或 MS-AZR-0148P），或即用即付（产品/服务编号：MS-AZR-0003P 或 MS-AZR-0023P）。
   - 对于企业订阅，从注册的货币承诺余额中扣除费用或作为超额收取费用。
   - 对于即用即付订阅，将向订阅的信用卡或发票付款方式收取费用。
4. 选择范围。 使用 "作用域" 列表选择订阅作用域。
   - **单个资源组范围**-仅将预订折扣应用于所选资源组中的匹配资源。
   - **单个订阅范围**-将预订折扣应用于所选订阅中的匹配资源。
   - **共享作用域**-将预订折扣应用于计费上下文中符合条件的订阅中的匹配资源。 对于企业协议客户，计费上下文是注册。 对于使用即用即付费率的各个订阅，计费范围是由帐户管理员创建的所有符合条件的订阅。
   - 对于企业客户，计费上下文为 EA 注册。
   - 对于即用即付客户，共享范围是由帐户管理员创建的所有即用即付订阅。
5. 选择一个区域，以选择保留容量涵盖的 Azure 区域。
6. 选择数量。 输入要购买的100数据仓库单位（cDWU）的数量。    
   例如，如果数量为30，则每小时会为你提供 3000 cDWU 的预留容量。
7. 在 "**成本**" 部分中查看 SQL 数据仓库保留容量保留成本。
8. 选择“购买”。
9. 选择 "**查看此预订**" 以查看购买状态。

## <a name="cancel-exchange-or-refund-reservations"></a>取消、交换或退款预订

您可以取消、交换或退款保留，但有一些限制。 有关详细信息，请参阅[Azure 预订的自助服务交换和退款](billing-azure-reservations-self-service-exchange-and-refund.md)。

预订折扣将自动应用于与 SQL 数据仓库保留容量范围和区域匹配的 SQL 数据仓库实例的数目。 你可以使用[Azure 门户](https://portal.azure.com/)、POWERSHELL、CLI 或通过 API 更新 SQL 数据仓库保留容量的范围。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/)。

## <a name="next-steps"></a>后续步骤

- 若要详细了解如何将预订折扣应用于 Azure SQL 数据仓库，请参阅[如何将预订折扣应用于 AZURE Sql 数据仓库](billing-prepay-sql-data-warehouse-charges-with-reserved-capacity.md)。

- 若要了解有关 Azure 预订的详细信息，请参阅以下文章：
  - [什么是 Azure 预订？](billing-save-compute-costs-reservations.md)
  - [管理 Azure 预留项](billing-manage-reserved-vm-instance.md)
  - [了解 Azure 预留折扣](billing-understand-reservation-charges.md)
  - [了解即用即付订阅的预留使用情况](billing-understand-reserved-instance-usage.md)
  - [了解企业合约的预留使用情况](billing-understand-reserved-instance-usage-ea.md)
