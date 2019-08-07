---
title: 为 SQL 数据仓库计费提供 Azure 保留容量
description: 了解如何为 SQL 数据仓库收费提供预留容量以节省资金。
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: banders
ms.openlocfilehash: 6ee7a661434bb756c6cf196937229db19d06b373
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779982"
---
# <a name="prepay-for-sql-data-warehouse-charges-with-reserved-capacity"></a>为 SQL 数据仓库支付预留容量的费用

你可以使用 Azure SQL 数据仓库通过事先预付在一年或三年期间使用 Azure SQL 数据仓库节省资金。 若要购买 SQL 数据仓库保留容量, 需要选择 Azure 区域和术语。 然后, 将 SQL 数据仓库 SKU 添加到购物车, 并选择要购买的 cDWU 单位的数量。

购买预订时, 与预订属性匹配的 SQL 数据仓库使用情况不再按即用即付费率收费。

预订不涉及与 SQL 数据仓库使用关联的存储或网络费用。

当保留容量过期时, SQL 数据仓库实例将继续运行, 但会按即用即付费率进行计费。 预订不会自动续订。

有关定价信息, 请参阅[SQL 数据仓库保留容量产品](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/)。

可以在[Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)中购买 Azure SQL 数据仓库保留容量。 购买预留容量：

- 你必须具有至少一个企业或即用即付订阅的所有者角色。
- 对于企业订阅, 必须在[EA 门户](https://ea.azure.com/)中启用 "**添加保留实例**" 选项。 如果禁用此设置, 则必须是 EA 管理员。
- 对于云解决方案提供商 (CSP) 程序, 只有管理代理或销售代理可以购买 SQL 数据仓库保留容量。

有关企业客户和即用即付客户如何针对预订购买付费的详细信息, 请参阅[了解企业注册的 azure 保留使用情况](billing-understand-reserved-instance-usage-ea.md)和[了解 azure 保留使用情况即用即付订阅](billing-understand-reserved-instance-usage.md)。

## <a name="choose-the-right-size-before-purchase"></a>在购买前选择合适的大小

SQL 数据仓库预留大小应基于你使用的总计算数据仓库单位 (cDWU)。 以 100 cDWU 的增量进行购买。

例如, 假设 SQL 数据仓库的总消耗为 DW3000c。 你需要为所有 it 购买保留容量。 因此, 应购买30个单位的 cDWU 保留容量。

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>购买 SQL 数据仓库保留容量

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 选择“所有服务” > “预订”。
3. 选择订阅。 使用 "订阅" 列表选择用于支付预留容量的订阅。 订阅的付款方式支付了预留容量的前期成本。 订阅类型必须为企业协议（套餐编号：MS-AZR-0017P 或 MS-AZR-0148P），或即用即付（产品/服务编号：MS-AZR-0003P 或 MS-AZR-0023P）。
   - 对于企业订阅，从注册的货币承诺余额中扣除费用或作为超额收取费用。
   - 对于即用即付订阅，将向订阅的信用卡或发票付款方式收取费用。
4. 选择范围。 使用 "作用域" 列表选择订阅作用域。
   - **单个资源组范围**-仅将预订折扣应用于所选资源组中的匹配资源。
   - **单个订阅范围**-将预订折扣应用于所选订阅中的匹配资源。
   - **共享作用域**-将预订折扣应用于计费上下文中符合条件的订阅中的匹配资源。 对于企业协议客户, 计费上下文是注册。 对于使用即用即付费率的各个订阅, 计费范围是由帐户管理员创建的所有符合条件的订阅。
   - 对于企业客户, 计费上下文为 EA 注册。
   - 对于即用即付客户，共享范围是由帐户管理员创建的所有即用即付订阅。
5. 选择一个区域, 以选择保留容量涵盖的 Azure 区域。
6. 选择数量。 输入要购买的100数据仓库单位 (cDWU) 的数量。    
   例如, 如果数量为 30, 则每小时会为你提供 3000 cDWU 的预留容量。
7. 在 "**成本**" 部分中查看 SQL 数据仓库保留容量保留成本。
8. 选择“购买”。
9. 选择 "**查看此预订**" 以查看购买状态。

## <a name="cancel-exchange-or-refund-reservations"></a>取消、交换或退款预订

您可以取消、交换或退款保留, 但有一些限制。 有关详细信息, 请参阅[Azure 预订的自助服务交换和退款](billing-azure-reservations-self-service-exchange-and-refund.md)。

预订折扣将自动应用于与 SQL 数据仓库保留容量范围和区域匹配的 SQL 数据仓库实例的数目。 你可以使用[Azure 门户](https://portal.azure.com/)、POWERSHELL、CLI 或通过 API 更新 SQL 数据仓库保留容量的范围。

## <a name="need-help-contact-us"></a>需要帮助? 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/)。

## <a name="next-steps"></a>后续步骤

- 若要详细了解如何将预订折扣应用于 Azure SQL 数据仓库, 请参阅[如何将预订折扣应用于 AZURE Sql 数据仓库](billing-prepay-sql-data-warehouse-charges-with-reserved-capacity.md)。

- 若要了解有关 Azure 预订的详细信息，请参阅以下文章：
  - [什么是 Azure 预订？](billing-save-compute-costs-reservations.md)
  - [管理 Azure 预留项](billing-manage-reserved-vm-instance.md)
  - [了解 Azure 预留折扣](billing-understand-reservation-charges.md)
  - [了解即用即付订阅的预留使用情况](billing-understand-reserved-instance-usage.md)
  - [了解企业合约的预留使用情况](billing-understand-reserved-instance-usage-ea.md)
