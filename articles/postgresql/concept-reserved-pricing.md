---
title: 保留计算定价-Azure Database for PostgreSQL-单服务器
description: 为 Azure Database for PostgreSQL 计算资源预付预留容量
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 05/02/2020
ms.openlocfilehash: 7f671e2a77a0a00fd1cc4338e29c14f7b8fca4f2
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734716"
---
# <a name="prepay-for-azure-database-for-postgresql-compute-resources-with-reserved-capacity"></a>为 Azure Database for PostgreSQL 计算资源预付预留容量

与即用即付价格相比，Azure Database for PostgreSQL 现在可为计算资源事先预付节省资金。 利用 Azure Database for PostgreSQL 预留容量，你可以在一年或三年期内对 PostgreSQL 服务器进行前期承诺，以获得有关计算成本的重要折扣。 若要购买 Azure Database for PostgreSQL 保留容量，需要指定 Azure 区域、部署类型、性能层和术语。 </br>

不需要将保留分配给特定 Azure Database for PostgreSQL 服务器。 已在运行的 Azure Database for PostgreSQL 或新部署的，将自动获得预留价格的好处。 购买预留容量便会预付为期一年或三年的计算资源费用。 购买保留后，与预订属性匹配的 Azure database for PostgreSQL 计算费用不再按即用即付费率收费。 预订不涉及与 PostgreSQL 数据库服务器关联的软件、网络或存储费用。 在保留期结束时，计费权益过期，按即用即付价格对 Azure Database for PostgreSQL 进行计费。 虚拟机预留实例不自动续订。 有关定价信息，请参阅[Azure Database for PostgreSQL 保留容量产品](https://azure.microsoft.com/pricing/details/postgresql/)。 </br>

> [!IMPORTANT]
> 保留的容量定价仅适用于 Azure Database for PostgreSQL[单服务器](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---single-server)部署，不适用于[超大规模 Citus](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---hyperscale-citus)部署。

可以在[Azure 门户](https://portal.azure.com/)中购买 Azure Database for PostgreSQL 保留容量。 通过[提前付款或按月付款](../cost-management-billing/reservations/monthly-payments-reservations.md)的方式为预留付款。 购买保留容量：

* 对于至少一个企业或个人订阅，你必须以即用即付费率作为所有者角色。
* 对于企业订阅，必须在 [EA 门户](https://ea.azure.com/)中启用“添加预留实例”****。 或者，如果禁用了该设置，则必须是订阅的 EA 管理员。
* 对于云解决方案提供商（CSP）程序，只有管理代理或销售代理可以购买 Azure Database for PostgreSQL 保留容量。 </br>

有关企业客户和即用即付客户如何针对预订购买付费的详细信息，请参阅[了解企业注册的 azure 保留使用情况](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)和了解即[用即付订阅的 azure 保留使用情况](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)。


## <a name="determine-the-right-server-size-before-purchase"></a>在购买之前确定正确的服务器大小

保留的大小应基于特定区域中现有或即将部署的服务器使用的计算总量，并使用相同的性能层和硬件生成。</br>

例如，假设你正在运行一个常规用途： Gen5 – 32 vCore PostgreSQL 数据库和两个内存优化 Gen5 – 16 vCore PostgreSQL 数据库。 接下来，假设你计划在下个月中部署一个额外的常规用途： Gen5 – 32 vCore 数据库服务器和一个内存优化的 Gen5 – 16 vCore 数据库服务器。 假设你知道，你将需要至少1年的这些资源。 在这种情况下，你应该购买64（2x32） Vcore，为单一数据库的1年保留-Gen5，为单一数据库内存优化了48（2x16 + 16） vCore 1 年保留-Gen5


## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>购买 Azure Database for PostgreSQL 保留的容量

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 选择 "**所有服务** > **保留**"。
3. 选择 "**添加**"，然后在 "购买预订" 窗格中选择 " **Azure Database for PostgreSQL** "，为 PostgreSQL 数据库购买新预订。
4. 填写必填字段。 与所选属性匹配的现有或新数据库可以获取预留容量折扣。 获取折扣的 Azure Database for PostgreSQL 服务器的实际数目取决于所选的范围和数量。


![保留定价概述](media/concepts-reserved-pricing/postgresql-reserved-price.png)


下表描述了必填字段。

| 字段 | 说明 |
| :------------ | :------- |
| 订阅   | 用于支付 Azure Database for PostgreSQL 预留容量预留的订阅。 订阅上的付款方式将收取 Azure Database for PostgreSQL 预留容量预留的前期成本。 订阅类型必须是企业协议（产品/服务编号： BC-OP-NT-AZR-Ms-azr-0017p 或 BC-OP-NT-AZR-Ms-azr-0148p）或使用即用即付定价的单个协议（产品/服务编号： MS-BC-OP-NT-AZR-Ms-azr-0003p 或-bc-op-nt-azr）。 对于企业订阅，从注册的货币承诺余额中扣除费用或作为超额收取费用。 对于使用即用即付定价的单个订阅，将对订阅上的信用卡或发票付款方式收取费用。
| 作用域 | vCore 预留范围可以包括一个订阅或多个订阅（共享范围）。 如果选择： </br></br> **共享**，vCore 预订折扣应用于计费上下文内任何订阅中运行的 Azure Database for PostgreSQL 服务器。 对于企业客户，共享范围是注册范围，包括注册中的所有订阅。 对于即用即付客户，共享范围是由帐户管理员创建的所有即用即付订阅。</br></br> **单个订阅**，vCore 预订折扣将应用到此订阅中的 Azure Database for PostgreSQL 服务器。 </br></br> **单个资源组**，预订折扣应用于所选订阅中的 Azure Database for PostgreSQL 服务器，以及该订阅内的所选资源组。
| 区域 | Azure Database for PostgreSQL 保留容量保留所涵盖的 Azure 区域。
| 部署类型 | 要为其购买预订的 Azure Database for PostgreSQL 资源类型。
| 性能层 | Azure Database for PostgreSQL 服务器的服务层。
| 术语 | 一年
| 数量 | 在 Azure Database for PostgreSQL 保留容量保留内购买的计算资源量。 该数量是所选 Azure 区域和正在保留的性能层中的 Vcore 数，将获得计费折扣。 例如，如果你正在运行或计划运行具有 Gen5 16 Vcore 的总计算能力的 Azure Database for PostgreSQL 服务器，则会将数量指定为16，以最大程度地提高所有服务器的权益。

## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

可以在一定的限制下对预留执行取消、交换或退款操作。 有关详细信息，请参阅[Azure 预订的自助服务交换和退款](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund)。

## <a name="vcore-size-flexibility"></a>vCore 大小灵活性

vCore 大小灵活性有助于在同一性能层和区域内纵向扩展或收缩，且不会丢失预留容量权益。 

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

VCore 预订折扣将自动应用于与 Azure Database for PostgreSQL 保留容量预留范围和属性匹配的 Azure Database for PostgreSQL 服务器数。 可以通过 Azure 门户、PowerShell、CLI 或通过 API，更新 Azure database for PostgreSQL reserved 容量预留的作用域。 </br></br>
若要了解如何管理 Azure Database for PostgreSQL 预留容量，请参阅管理 Azure Database for PostgreSQL 保留容量。

若要了解有关 Azure 预订的详细信息，请参阅以下文章：

* [什么是 Azure 保留](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)？
* [管理 Azure 预留项](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [了解 Azure 预留折扣](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [了解即用即付订阅的预留使用情况](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-postgresql)
* [了解企业合约的预留使用情况](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [合作伙伴中心云解决方案提供商 (CSP) 计划中的 Azure 预订](https://docs.microsoft.com/partner-center/azure-reservations)
