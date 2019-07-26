---
title: 预付 Azure SQL 数据库 vCore 费用以节省资金 | Microsoft Docs
description: 了解如何购买 Azure SQL 数据库预留容量以节省计算成本。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 07/19/2019
ms.openlocfilehash: 1cc8828f5a936b130480c2c7516d00b8bee6760f
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68357279"
---
# <a name="prepay-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>通过 Azure SQL 数据库预留容量预付 SQL 数据库计算资源费用

与即用即付相比，预付计算资源费用可以节省 Azure SQL 数据库的成本。 通过 Azure SQL 数据库预留容量，可以提前承诺为期一年或三年的 SQL 数据库购买量，以获得可观的计算资源价格折扣。 若要购买 SQL 数据库预留容量，需要指定 Azure 区域、部署类型、性能层和期限。


不需要将预留容量分配给特定的 SQL 数据库实例（单一数据库、弹性池或托管实例）。 已在运行或者新部署的匹配 SQL 数据库实例将自动获得此权益。 购买预留容量便会预付为期一年或三年的计算资源费用。 购买预留容量后，与预留属性匹配的 SQL 数据库计算资源费用不再按照即用即付的费率计收。 预留容量不包括与 SQL 数据库实例关联的软件、网络或存储费用。 预留期限结束时，计费权益随即过期，SQL 数据库将按即用即付价格计费。 虚拟机预留实例不自动续订。 有关定价信息，请参阅 [SQL 数据库预留容量套餐](https://azure.microsoft.com/pricing/details/sql-database/managed/)。

可在 [Azure 门户](https://portal.azure.com)中购买 Azure SQL 数据库预留容量。 购买 SQL 数据库预留容量：

- 对于至少一个企业或个人订阅, 你必须以即用即付费率作为所有者角色。
- 对于企业订阅，必须在 [EA 门户](https://ea.azure.com)中启用“添加预留实例”。 或者，如果禁用了该设置，则必须是订阅的 EA 管理员。
- 对于云解决方案提供商 (CSP) 计划，只有管理员代理或销售代理可以购买 SQL 数据库预留容量。

有关预留购买如何针对企业客户和即用即付客户进行计费的详细信息，请参阅[了解适用于企业合约的 Azure 预留使用情况](../billing/billing-understand-reserved-instance-usage-ea.md)和[了解即用即付订阅的 Azure 预留使用情况](../billing/billing-understand-reserved-instance-usage.md)。

## <a name="determine-the-right-sql-size-before-purchase"></a>购买之前确定正确的 SQL 大小

预留容量的大小应基于特定区域中使用相同性能层和硬件代次的现有或即将部署的单一数据库、弹性池或托管实例所用的计算资源总量。

例如，假设你要运行一个常规用途的第 5 代 16 vCore 弹性池，以及两个业务关键型的第 5 代 4 vCore 单一数据库。 此外，假设你打算在下个月额外部署一个常规用途的第 5 代 16 vCore 弹性池，以及一个业务关键型的第 5 代 32 vCore 弹性池。 我们还假设你知道在至少 1 年的时间内，都需要这些资源。 在这种情况下，应该购买 32 (2x16) 个 vCore、1 年的“常规用途 - 第 5 代”单一数据库/弹性池的预留容量，以及 40 (2x4 + 32) 个 vCore、1 年的“业务关键 - 第 5 代”单一数据库/弹性池的预留容量。

## <a name="buy-sql-database-reserved-capacity"></a>购买 SQL 数据库预留容量

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务” > “预订”。
3. 选择 "**添加**", 然后在 "购买预订" 窗格中选择 " **sql 数据库**", 为 sql 数据库购买新的保留。
4. 填写必填字段。 与所选属性匹配的现有或新的单一数据库、弹性池或托管实例符合享受预留容量折扣的条件。 获得折扣的 SQL 数据库实例的实际数目取决于所选范围和数量。
    ![提交 SQL 数据库保留容量购买之前的屏幕截图](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

下表描述了必填字段。

| 字段      | 描述|
|------------|--------------|
|订阅|用于支付 SQL 数据库预留容量预订费用的订阅。 订阅付款方式是收取 SQL 数据库预留容量的预付费用。 订阅类型必须为企业协议（套餐编号：BC-OP-NT-AZR-Ms-azr-0017p 或 BC-OP-NT-AZR-Ms-azr-0148p) 或使用即用即付定价的单个协议 (产品/服务编号:MS-AZR-0003P 或 MS-AZR-0023P）。 对于企业订阅，从注册的货币承诺余额中扣除费用或作为超额收取费用。 对于使用即用即付定价的单个订阅, 将对订阅上的信用卡或发票付款方式收取费用。|
|范围       |vCore 预留范围可以包括一个订阅或多个订阅（共享范围）。 如果选择： <br/><br/>**共享**, vCore 预订折扣应用于计费上下文内任何订阅中运行的 SQL 数据库实例。 对于企业客户，共享范围是注册范围，包括注册中的所有订阅。 对于即用即付客户，共享范围是由帐户管理员创建的所有即用即付订阅。<br/><br/>**单个订阅**, vCore 预订折扣将应用到此订阅中的 SQL 数据库实例。 <br/><br/>**单个资源组**, 预订折扣应用于所选订阅中的 SQL 数据库实例, 以及该订阅内的所选资源组。|
|地区      |预订的 SQL 数据库预留容量涵盖的 Azure 区域。|
|RootFile|要为其购买预留容量的 SQL 资源类型。|
|性能层|SQL 数据库实例的服务层级。
|术语        |一年或三年。|
|数量    |在 SQL 数据库保留容量保留内购买的计算资源量。 该数量是所选 Azure 区域和正在保留的性能层中的 Vcore 数, 将获得计费折扣。 例如, 如果你正在运行或计划运行的 SQL 数据库实例的总计算能力为 "美国东部" 区域的 Gen5 16 Vcore, 则需将 "数量" 指定为 16, 以最大程度地提高所有实例的权益。 |

1. 在**费用**部分查看 SQL 数据库预留容量的预订费用。
1. 选择“购买”。
1. 选择“查看此预订”以查看购买的状态。

## <a name="cancellations-and-exchanges"></a>取消和更换

如果需要取消你的 SQL 数据库预留容量预订，可能会产生 12% 的提前终止费。 退款根据购买价格和当前预留价格中的最低者计算。 退款限制为每年 50,000 美元。 收到的退款为按比例计算出的余额减去 12% 的提前终止费。 若要取消, 请跳到 Azure 门户中的预订, 然后选择 "**退款**"。

如果需要将 SQL 数据库预留容量预订更改为其他区域、部署类型、性能层或期限，可以将其更换为价值相等或更高的另一个预订。 新订购项目的期限开始日期不是从交换的订购项目延续。 1 年或 3 年期限从创建新订购项目开始算起。 若要交换, 请在 Azure 门户中找到保留项, 然后选择 " **exchange**"。

有关如何交换或退款预订的详细信息, 请参阅[保留交换和退款](../billing/billing-azure-reservations-self-service-exchange-and-refund.md)。

## <a name="vcore-size-flexibility"></a>vCore 大小灵活性

vCore 大小灵活性有助于在同一性能层和区域内纵向扩展或收缩，且不会丢失预留容量权益。 SQL 数据库预留容量还提供了灵活性，使用户可以在正常操作过程中临时在池和单一数据库之间移动正在使用的数据库（在同一区域和性能层内），而不会失去预留容量权益。 通过在预订中保留一个未应用的缓冲区，你可以在不超出预算的情况下有效地管理性能峰值。

## <a name="need-help-contact-us"></a>需要帮助? 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

vCore 预留折扣自动应用到与 SQL 数据库预留容量预订范围和属性匹配的 SQL 数据库实例数目。 可以通过 [Azure 门户](https://portal.azure.com)、PowerShell、CLI 或 API 更新 SQL 数据库预留容量的预订范围。

若要了解如何管理 SQL 数据库预留容量的预订，请参阅[管理 SQL 数据库预留容量](../billing/billing-manage-reserved-vm-instance.md)。

若要了解有关 Azure 预订的详细信息，请参阅以下文章：

- [什么是 Azure 预订？](../billing/billing-save-compute-costs-reservations.md)
- [管理 Azure 预留项](../billing/billing-manage-reserved-vm-instance.md)
- [了解 Azure 预留折扣](../billing/billing-understand-reservation-charges.md)
- [了解即用即付订阅的预留使用情况](../billing/billing-understand-reserved-instance-usage.md)
- [了解企业合约的预留使用情况](../billing/billing-understand-reserved-instance-usage-ea.md)
- [合作伙伴中心云解决方案提供商 (CSP) 计划中的 Azure 预订](https://docs.microsoft.com/partner-center/azure-reservations)
