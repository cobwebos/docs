---
title: 预付 Azure SQL 数据库 vCore 费用以节省资金 | Microsoft Docs
description: 了解如何购买 Azure SQL 数据库预留容量以节省计算成本。
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.devlang: na
ms.topic: conceptual
ms.date: 08/08/2018
ms.author: carlrab
ms.openlocfilehash: 1d1cad4b614eb35be9235a721368db8048be050a
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39631938"
---
# <a name="prepay-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>通过 Azure SQL 数据库预留容量预付 SQL 数据库计算资源费用

与即用即付相比，预付 Azure SQL 数据库的计算资源费用可以节省 Azure SQL 数据库的成本。 通过 Azure SQL 数据库预留容量，可以提前承诺为期一年或三年的 SQL 数据库购买量，以获得可观的计算资源价格折扣。 若要购买 SQL 数据库预留容量，需要指定 Azure 区域、部署类型、服务和期限。 

不需要将预留容量分配到 SQL 数据库实例。 已在运行或者新部署的匹配 SQL 数据库实例将自动获得此权益。 购买预留容量即可预付为期一年或三年的 SQL 数据库实例计算资源费用。 购买预留容量后，与预留属性匹配的 SQL 数据库计算资源费用不再按照即用即付的费率计收。 预留容量不包括与 SQL 数据库实例关联的软件、网络或存储费用。 预留期限结束时，计费权益随即过期，SQL 数据库将按即用即付价格计费。 虚拟机预留实例不自动续订。 有关定价信息，请参阅 [SQL 数据库预留容量套餐](https://azure.microsoft.com/pricing/details/sql-database/managed/)。

可在 [Azure 门户](https://portal.azure.com)中购买 Azure SQL 数据库预留容量。 购买 SQL 数据库预留容量：
- 必须至少是一个企业或即用即付订阅的“所有者”角色。
- 对于企业订阅，必须在 [EA 门户](https://ea.azure.com)中启用 Azure 预留容量购买。
-  对于云解决方案提供商 (CSP) 计划，只有管理员代理或销售代理可以购买 SQL 数据库预留容量。

[常见问题解答](#frequently-asked-questions)中提到了有关如何向企业客户和即用即付客户计收预留容量费用的详细信息。

## <a name="determine-the-right-sql-size-before-purchase"></a>购买之前确定正确的 SQL 大小

预留容量的大小应该基于特定区域中使用相同性能层和硬件代次的现有或即将部署的 SQL 单一数据库和/或弹性池所用的计算资源总量。 

例如，假设你要运行一个常规用途的第 5 代 16 vCore 弹性池，以及两个业务关键型的第 5 代 4 vCore 单一数据库。 此外，假设你打算在下个月额外部署一个常规用途的第 5 代 16 vCore 弹性池，以及一个业务关键型的第 5 代 32 vCore 弹性池。 我们还假设你知道在至少 1 年的时间内，都需要这些资源。 在这种情况下，应该购买 32 (2x16) 个 vCore、1 年的“常规用途 - 第 5 代计算”SQL 单一数据库/弹性池的预留容量，以及 40 (2x4 + 32) 个 vCore、1 年的“业务关键型 - 第 5 代计算”SQL 单一数据库/弹性池的预留容量。

## <a name="buy-sql-database-reserved-capacity"></a>购买 SQL 数据库预留容量

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务” > “预订”。
3. 选择“添加”，然后在“选择产品类型”窗格中，选择“SQL 数据库”以购买 SQL 数据库的新预留容量。
4. 填写必填字段。 与所选属性匹配的现有或新的单一数据库或弹性池符合预留容量折扣的条件。 获得折扣的 SQL 数据库实例的实际数目取决于所选范围和数量。

   ![提交 SQL 数据库预留容量订单之前的屏幕截图](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

    | 字段      | Description|
    |:------------|:--------------|
    |名称        |此预订的名称。| 
    |订阅|用于支付 SQL 数据库预留容量预订费用的订阅。 订阅付款方式是收取 SQL 数据库预留容量的预付费用。 订阅类型必须是“企业协议”（套餐编号：MS-AZR-0017P）或“即用即付”（套餐编号：MS-AZR-0003P）。 对于企业订阅，从注册的货币承诺余额中扣除费用或作为超额收取费用。 对于即用即付订阅，将向订阅的信用卡或发票付款方式收取费用。|    
    |范围       |vCore 预留范围可以包括一个订阅或多个订阅（共享范围）。 如果选择： <ul><li>单个订阅 - vCore 预留折扣将应用到此订阅中的 SQL 数据库实例。 </li><li>共享 - vCore 预留折扣将应用到计费上下文中任何订阅中运行的 SQL 数据库实例。 对于企业客户，共享范围是许可登记表，包括许可登记表中的所有订阅（开发/测试订阅除外）。 对于即用即付客户，共享范围是由帐户管理员创建的所有即用即付订阅。</li></ul>|
    |区域      |预订的 SQL 数据库预留容量涵盖的 Azure 区域。|    
    |部署类型|要为其购买预留容量的 SQL 部署类型。|
    |服务层|SQL 数据库实例的服务层。
    |术语        |一年或三年。|
    |数量    |在 SQL 数据库预留容量预订中购买的实例数。 该数量是可以获得计费折扣的运行中 SQL 数据库实例数。 例如，如果在美国东部运行 10 个 SQL 数据库实例，则将数量指定为 10，以将所有正在运行的虚拟机的权益最大化。 |

5. 在**费用**部分查看 SQL 数据库预留容量的预订费用。
6. 选择“购买”。
7. 选择“查看此预订”以查看购买的状态。

## <a name="next-steps"></a>后续步骤 
vCore 预留折扣自动应用到与 SQL 数据库预留容量预订范围和属性匹配的 SQL 数据库实例数目。 可以通过 [Azure 门户](https://portal.azure.com)、PowerShell、CLI 或 API 更新 SQL 数据库预留容量的预订范围。 

若要了解如何管理 SQL 数据库预留容量的预订，请参阅[管理 SQL 数据库预留容量](../billing/billing-manage-reserved-vm-instance.md)。

若要详细了解 Azure 预留，请参阅以下文章：

- [什么是 Azure 预留？](../billing/billing-save-compute-costs-reservations.md)
- [管理 Azure 预留项](../billing/billing-manage-reserved-vm-instance.md)
- [了解即用即付订阅的预留使用情况](../billing/billing-understand-reserved-instance-usage.md)
- [了解企业合约的预留使用情况](../billing/billing-understand-reserved-instance-usage-ea.md)
- [合作伙伴中心云解决方案提供商 (CSP) 计划中的 Azure 预留](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果仍有疑问，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

