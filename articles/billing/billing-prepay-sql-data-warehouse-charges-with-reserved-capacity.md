---
title: SQL 数据仓库费用的预付款购买具有 Azure 预留容量 |Microsoft Docs
description: 了解如何可以为 SQL 数据仓库费用预付款具有预留容量以节省资金。
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 3d56ee99f8056139a234477b6eed93d9fcbe2bbb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60371183"
---
# <a name="prepay-for-sql-data-warehouse-charges-with-reserved-capacity"></a>为 SQL 数据仓库费用预付款具有预留容量

通过事先 cDWU 使用量预付一年或三年的持续时间，可以使用 Azure SQL 数据仓库降低成本。 若要购买保留的 SQL 数据仓库容量，需要选择的 Azure 区域和术语。 然后，将 SQL 数据仓库 SKU 添加到您的购物车，并选择你想要购买的 cDWU 单位的数量。

在购买预订时，SQL 数据仓库与预订属性匹配的用量不能再收费付薪现都将费率。

保留不会介绍存储或 SQL 数据仓库用途相关联的网络费用。

保留的容量过期时，SQL 数据仓库实例继续运行，但会按即付现转费率收费。 保留不自动续订。

有关定价信息，请参阅[SQL 数据仓库保留容量的产品/服务](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/)。

可以购买保留 Azure SQL 数据仓库容量[Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)。 购买预留容量：

- 必须具有至少一个企业或即用即付订阅的所有者角色。
- 对于企业订阅**添加保留实例**必须在启用选项[EA 门户](https://ea.azure.com/)。 如果禁用了设置，你必须是 EA 管理员。
- 对于云解决方案提供商 (CSP) 程序中，只有管理代理或销售代理可以购买保留的 SQL 数据仓库容量。

企业客户和即用即付客户的预订购买收费的详细信息，请参阅[了解企业许可登记表 Azure 预订使用情况](billing-understand-reserved-instance-usage-ea.md)和[了解 Azure即用即付订阅的预订使用情况](billing-understand-reserved-instance-usage.md)。

## <a name="choose-the-right-size-before-purchase"></a>选择适当的大小购买前

SQL 数据仓库预留大小应基于总数计算所使用的数据仓库单位 (cDWU)。 以 100 cDWU 为增量进行购买。

例如，假设 SQL 数据仓库在总消耗 DW3000c。 你想要购买它的所有预留的容量。 因此，您应购买 cDWU 保留容量的 30 个单位。

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>购买 SQL 数据仓库保留容量

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 选择“所有服务” > “预订”。
3. 选择一个订阅。 使用的订阅列表来选择用于支付的预留容量的订阅。 订阅的付款方式进行收费的预留容量的预付费用。 订阅类型必须为企业协议（套餐编号：MS-AZR-0017P 或 MS-AZR-0148P），或即用即付（产品/服务编号：MS-AZR-0003P 或 MS-AZR-0023P）。
  - 对于企业订阅，从注册的货币承诺余额中扣除费用或作为超额收取费用。
  - 对于即用即付订阅，将向订阅的信用卡或发票付款方式收取费用。
4. 选择作用域。 使用作用域列表选择订阅范围。
  - 与**单个**选项，将预订折扣应用于所选订阅中部署的 SQL 数据仓库。
  - 与**共享**选项，将预订折扣应用到任何订阅计费上下文中运行的实例。
    - 适用于企业客户的计费上下文是 EA 注册。
    - 对于即用即付客户，共享范围是由帐户管理员创建的所有即用即付订阅。
5. 选择要选择 Azure 区域所涵盖的预留容量的区域。
6. 选择数量。 输入的 100 个数据仓库单位 (cDWU) 想要购买的数量。    
  例如，30 的数量将为您提供保留容量的 3,000 cDWU 每隔一小时。
7. 查看 SQL 数据仓库保留在容量保留成本**成本**部分。
8. 选择“购买”。
9. 选择**查看此预订**若要查看您购买的状态。

## <a name="cancellations-and-exchanges"></a>取消和更换

如果你需要取消 SQL 数据仓库保留容量，可能会有 12%的提前终止服务费。 退款根据购买价格和当前预留价格中的最低者计算。 退款仅限于每 50,000.00 年美元。 你收到的退款是剩余的按比例的余额减去 12%的提前终止服务费。 若要请求取消，请在 Azure 门户中访问该预留，并选择“退款”创建支持请求。

如果需要将 SQL 数据仓库保留容量更改为另一个区域或字词，您可以为相等或更高版本的值的另一个预订来进行交换。 新订购项目的期限开始日期不是从交换的订购项目延续。 一个或三年期开始时创建新的保留。 若要请求交换，在 Azure 门户中，打开预订，并选择**Exchange**创建支持请求。

有关如何对 exchange 或退款保留的详细信息，请参阅[保留交换和退款](billing-azure-reservations-self-service-exchange-and-refund.md)。

预订折扣会自动应用到匹配的 SQL 数据仓库保留容量作用域和区域的 SQL 数据仓库实例数。 您可以更新与 SQL 数据仓库保留容量的作用域[Azure 门户](https://portal.azure.com/)，PowerShell、 CLI 或通过 API。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/)。

## <a name="next-steps"></a>后续步骤

- 若要了解有关如何将预订折扣应用于 Azure SQL 数据仓库的详细信息，请参阅[如何将预订折扣应用于 Azure SQL 数据仓库](billing-prepay-sql-data-warehouse-charges-with-reserved-capacity.md)。

- 若要了解有关 Azure 预订的详细信息，请参阅以下文章：
  - [什么是 Azure 预订？](billing-save-compute-costs-reservations.md)
  - [管理 Azure 预留项](billing-manage-reserved-vm-instance.md)
  - [了解 Azure 预留折扣](billing-understand-reservation-charges.md)
  - [了解即用即付订阅的预留使用情况](billing-understand-reserved-instance-usage.md)
  - [了解企业合约的预留使用情况](billing-understand-reserved-instance-usage-ea.md)
