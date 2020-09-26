---
title: 节省预留容量的计算成本
titleSuffix: Azure SQL Database & SQL Managed Instance
description: 了解如何购买 Azure SQL 数据库和 SQL 托管实例保留容量以节省计算成本。
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 08/29/2019
ms.openlocfilehash: 7a7373f5fcd36298d2feeff6a2a5b67c9e10e40b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91321588"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-sql-database--sql-managed-instance"></a>利用保留容量节省资源成本-Azure SQL 数据库 & SQL 托管实例
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)] 

与即用即付价格相比，将 Azure SQL 数据库和 SQL 托管实例与即用即付价格相比，为计算资源节省资金。 利用预留容量，你可以对 SQL 数据库和/或 SQL 托管实例使用一年或三年的时间，以获得有关计算成本的重要折扣。 若要购买保留容量，需要指定 Azure 区域、部署类型、性能层和术语。

不需要将保留分配给特定的数据库或托管实例。 匹配已在运行的现有部署或新部署的部署会自动获得权益。 购买预订后，可在一年或三年的时间内承诺计算成本。 一旦购买了预订，就不再按即用即付费率对与预订属性匹配的计算费用收费。 预订不涉及与服务相关联的软件、网络或存储费用。 在保留期结束时，计费权益过期，数据库或托管实例按即用即付价格计费。 保留不自动续订。 有关定价信息，请参阅 [保留容量产品](https://azure.microsoft.com/pricing/details/sql-database/managed/)。

可以在 [Azure 门户](https://portal.azure.com)中购买保留容量。 通过[提前付款或按月付款](../../cost-management-billing/reservations/prepare-buy-reservation.md)的方式为预留付款。 购买预留容量：

- 对于至少一个企业或个人订阅，你必须以即用即付费率作为所有者角色。
- 对于企业订阅，必须在 [EA 门户](https://ea.azure.com)中启用“添加预留实例”****。 或者，如果禁用了该设置，则必须是订阅的 EA 管理员。 保留容量。

有关企业客户和即用即付客户如何针对预订购买付费的详细信息，请参阅 [了解企业注册的 azure 保留使用情况](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) 和 [了解即用即付订阅的 azure 保留使用情况](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)。

## <a name="determine-correct-size-before-purchase"></a>在购买之前确定正确的大小

保留的大小应基于特定区域中现有或即将部署的数据库或托管实例使用的计算总量，并使用相同的性能层和硬件生成。

例如，假设你正在运行一个通用的 Gen5 – 16 vCore 弹性池和两个业务关键 Gen5 – 4 vCore 单一数据库。 接下来，假设你计划在下个月中部署一个额外的常规用途 Gen5 – 16 vCore 弹性池和一个业务关键 Gen5 – 32 vCore 弹性池。 我们还假设你知道在至少 1 年的时间内，都需要这些资源。 在这种情况下，你应该购买 32 (2x16) Vcore 1 年保留，适用于单一数据库/弹性池常规用途-Gen5 和 40 (2x4 + 32) vCore 1 年保留用于单数据库/弹性池业务关键-Gen5。

## <a name="buy-reserved-capacity"></a>购买预留容量

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务” > “预订”。
3. 选择 " **添加** "，然后在 " **购买预订** " 窗格中选择 " **SQL 数据库** "，为 sql 数据库购买新的保留。
4. 填写必填字段。 SQL 数据库中的现有数据库和与选择的属性相匹配的 SQL 托管实例都有资格获取预留容量折扣。 获取折扣的数据库或托管实例的实际数目取决于所选的范围和数量。

    ![提交保留容量购买之前的屏幕截图](./media/reserved-capacity-overview/sql-reserved-vcores-purchase.png)

    下表描述了必填字段。
    
    | 字段      | 说明|
    |------------|--------------|
    |订阅|用于支付产能预留的订阅。 将向订阅的付款方式收取预订的预付费用。 订阅类型必须是企业协议 (产品/服务的 Ms-azr-0017p 或 BC-OP-NT-AZR-Ms-azr-0148p) ，或具有即用即付定价的单个协议 (产品编号为 MS-BC-OP-NT-AZR 或 bc-op-nt-azr-ms-azr-0003p) 。 对于企业订阅，从注册的货币承诺余额中扣除费用或作为超额收取费用。 对于使用即用即付定价的单个订阅，将对订阅上的信用卡或发票付款方式收取费用。|
    |范围       |VCore 预订的范围可以包含一个订阅或多个订阅 (共享范围) 。 如果选择 <br/><br/>**共享**，vCore 预订折扣应用于计费上下文内任何订阅中运行的数据库或托管实例。 对于企业客户，共享范围是注册范围，包括注册中的所有订阅。 对于即用即付客户，共享范围是由帐户管理员创建的所有即用即付订阅。<br/><br/>**单个订阅**，vCore 预订折扣将应用到此订阅中的数据库或托管实例。 <br/><br/>**单个资源组**，预订折扣应用于所选订阅中的数据库实例或托管实例，以及该订阅内的所选资源组。|
    |区域      |容量保留所涵盖的 Azure 区域。|
    |部署类型|要为其购买预留容量的 SQL 资源类型。|
    |性能层|数据库或托管实例的服务层。 |
    |术语        |一年或三年。|
    |数量    |在容量保留内购买的计算资源量。 该数量是所选 Azure 区域和正在保留的性能层中的 Vcore 数，将获得计费折扣。 例如，如果运行或计划运行的多个数据库的总计算能力为 "美国东部" 区域的 Gen5 16 Vcore，则可将数量指定为16，以最大程度地提高所有数据库的权益。 |

1. 在 " **成本** " 部分中查看容量预留的成本。
1. 选择“购买”。
1. 选择“查看此预订”**** 以查看购买的状态。

## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

可以在一定的限制下对预留执行取消、交换或退款操作。 有关详细信息，请参阅 [Azure 预留的自助交换和退款](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

## <a name="vcore-size-flexibility"></a>vCore 大小灵活性

vCore 大小灵活性有助于在同一性能层和区域内纵向扩展或收缩，且不会丢失预留容量权益。 保留容量还能让你灵活地将热数据库移入和移出弹性池 (在同一区域和性能层中，) 作为正常操作的一部分，而不会丢失保留容量权益。 通过保留保留中未使用的缓冲区，可以有效地管理性能峰值，而不会超出预算。

## <a name="limitation"></a>限制

不能在 SQL 数据库中保留基于 DTU 的 (基本、标准或高级) 的数据库。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

VCore 预订折扣将自动应用于与容量预留范围和属性匹配的数据库或托管实例的数目。 可以通过 [Azure 门户](https://portal.azure.com)、PowerShell、AZURE CLI 或 API 更新容量预留的作用域。

若要了解如何管理容量预留，请参阅 [管理预留容量](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)。

若要了解有关 Azure 预订的详细信息，请参阅以下文章：

- [什么是 Azure 预订？](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [管理 Azure 预留项](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [了解 Azure 预留折扣](../../cost-management-billing/reservations/understand-reservation-charges.md)
- [了解即用即付订阅的预留使用情况](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [了解企业合约的预留使用情况](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [合作伙伴中心云解决方案提供商 (CSP) 计划中的 Azure 预订](https://docs.microsoft.com/partner-center/azure-reservations)
