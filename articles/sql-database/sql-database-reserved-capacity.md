---
title: 节省成本
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
ms.date: 08/29/2019
ms.openlocfilehash: 0713e1ed98cc87b95cad1f84672148cd062e0b58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979993"
---
# <a name="save-costs-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>使用 Azure SQL 数据库预留容量节省 SQL 数据库计算资源的成本

与即用即付价格相比，通过订阅计算资源预留，使用 Azure SQL 数据库节省资金。 使用 Azure SQL 数据库预留容量时，您可以承诺在一到三年内使用 SQL 数据库，以便显著降低计算成本。 若要购买 SQL 数据库预留容量，需要指定 Azure 区域、部署类型、性能层和期限。


不需要将预留容量分配给特定的 SQL 数据库实例（单一数据库、弹性池或托管实例）。 已在运行或者新部署的匹配 SQL 数据库实例将自动获得此权益。 通过购买预订，您将承诺使用一年或三年的计算成本。 购买预留容量后，与预留属性匹配的 SQL 数据库计算资源费用不再按照即用即付的费率计收。 预留容量不包括与 SQL 数据库实例关联的软件、网络或存储费用。 预留期限结束时，计费权益随即过期，SQL 数据库将按即用即付价格计费。 虚拟机预留实例不自动续订。 有关定价信息，请参阅 [SQL 数据库预留容量套餐](https://azure.microsoft.com/pricing/details/sql-database/managed/)。

可在 [Azure 门户](https://portal.azure.com)中购买 Azure SQL 数据库预留容量。 通过[提前付款或按月付款](../cost-management-billing/reservations/monthly-payments-reservations.md)的方式为预留付款。 购买 SQL 数据库预留容量：

- 您必须在至少一个企业或个人订阅的所有者角色中，使用即用即付费率。
- 对于企业订阅，必须在 [EA 门户](https://ea.azure.com)中启用“添加预留实例”****。 或者，如果禁用了该设置，则必须是订阅的 EA 管理员。
- 对于云解决方案提供商 (CSP) 计划，只有管理员代理或销售代理可以购买 SQL 数据库预留容量。

有关企业客户和即用即付客户如何收取预订费用的详细信息，请参阅[了解企业注册的 Azure 预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)[，并了解即用即付订阅的 Azure 预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage.md)。

## <a name="determine-the-right-sql-size-before-purchase"></a>购买之前确定正确的 SQL 大小

预留容量的大小应基于特定区域中使用相同性能层和硬件代次的现有或即将部署的单一数据库、弹性池或托管实例所用的计算资源总量。

例如，假设你要运行一个常规用途的第 5 代 16 vCore 弹性池，以及两个业务关键型的第 5 代 4 vCore 单一数据库。 此外，假设你打算在下个月额外部署一个常规用途的第 5 代 16 vCore 弹性池，以及一个业务关键型的第 5 代 32 vCore 弹性池。 我们还假设你知道在至少 1 年的时间内，都需要这些资源。 在这种情况下，应该购买 32 (2x16) 个 vCore、1 年的“常规用途 - 第 5 代”单一数据库/弹性池的预留容量，以及 40 (2x4 + 32) 个 vCore、1 年的“业务关键 - 第 5 代”单一数据库/弹性池的预留容量。

## <a name="buy-sql-database-reserved-capacity"></a>购买 SQL 数据库预留容量

1. 登录到 Azure[门户](https://portal.azure.com)。
2. 选择**所有服务** > **预订**。
3. 选择 **"添加**"，然后在"购买预留"窗格中选择**SQL 数据库**以购买 SQL 数据库的新预留。
4. 填写所需的字段。 与所选属性匹配的现有或新的单一数据库、弹性池或托管实例符合享受预留容量折扣的条件。 获得折扣的 SQL 数据库实例的实际数目取决于所选范围和数量。
    ![提交 SQL 数据库预留容量订单之前的屏幕截图](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

下表描述了必填字段。

| 字段      | 描述|
|------------|--------------|
|订阅|用于支付 SQL 数据库预留容量预订费用的订阅。 订阅付款方式是收取 SQL 数据库预留容量的预付费用。 订阅类型必须是企业协议（产品/服务编号：MS-AZR-0017P 或 MS-AZR-0148P）或具有即用即付定价的单个协议（产品/服务编号：MS-AZR-0003P 或 MS-AZR-0023P）。 对于企业订阅，从注册的货币承诺余额中扣除费用或作为超额收取费用。 对于具有即用即付定价的个人订阅，费用将计入订阅上的信用卡或发票付款方式。|
|范围       |vCore 预留范围可以包括一个订阅或多个订阅（共享范围）。 如果选择： <br/><br/>**共享**，vCore 预留折扣应用于在计费上下文中任何订阅中运行的 SQL 数据库实例。 对于企业客户，共享范围是注册范围，包括注册中的所有订阅。 对于即用即付客户，共享范围是由帐户管理员创建的所有即用即付订阅。<br/><br/>**单个订阅**，vCore 预留折扣应用于此订阅中的 SQL 数据库实例。 <br/><br/>**单个资源组**，预留折扣应用于所选订阅中的 SQL 数据库实例以及该订阅中的选定资源组。|
|区域      |预订的 SQL 数据库预留容量涵盖的 Azure 区域。|
|部署类型|要为其购买预留容量的 SQL 资源类型。|
|性能层|SQL 数据库实例的服务层级。
|术语        |一年或三年。|
|数量    |在 SQL 数据库预留容量预留中购买的计算资源量。 数量是所选 Azure 区域和"性能"层中保留的 vCore 的多个 vCore，它们将获得计费折扣。 例如，如果您正在运行或计划运行 SQL 数据库实例，总计算容量为 En5 16 vCores 在美国东部区域，那么您将数量指定为 16，以最大化所有实例的好处。 |

1. 在**费用**部分查看 SQL 数据库预留容量的预订费用。
1. 选择“购买”。****
1. 选择“查看此预订”**** 以查看购买的状态。

## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

可以在一定的限制下对预留执行取消、交换或退款操作。 有关详细信息，请参阅 [Azure 预留的自助交换和退款](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

## <a name="vcore-size-flexibility"></a>vCore 大小灵活性

vCore 大小灵活性有助于在同一性能层和区域内纵向扩展或收缩，且不会丢失预留容量权益。 SQL 数据库预留容量还提供了灵活性，使用户可以在正常操作过程中临时在池和单一数据库之间移动正在使用的数据库（在同一区域和性能层内），而不会失去预留容量权益。 通过在预订中保留一个未应用的缓冲区，你可以在不超出预算的情况下有效地管理性能峰值。

## <a name="limitation"></a>限制

您不能保留基于 DTU 的 SQL 数据库（基本数据库、标准数据库或高级数据库）。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

vCore 预留折扣自动应用到与 SQL 数据库预留容量预订范围和属性匹配的 SQL 数据库实例数目。 可以通过 [Azure 门户](https://portal.azure.com)、PowerShell、CLI 或 API 更新 SQL 数据库预留容量的预订范围。

要了解如何管理 SQL 数据库预留容量保留，请参阅[管理 SQL 数据库保留容量](../cost-management-billing/reservations/manage-reserved-vm-instance.md)。

若要了解有关 Azure 预订的详细信息，请参阅以下文章：

- [什么是 Azure 预留？](../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [管理 Azure 预留](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [了解 Azure 预留折扣](../cost-management-billing/reservations/understand-reservation-charges.md)
- [了解即用即付订阅的预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [了解企业合约的预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [合作伙伴中心云解决方案提供商 (CSP) 计划中的 Azure 预订](https://docs.microsoft.com/partner-center/azure-reservations)
