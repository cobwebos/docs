---
title: '保留计算定价-Azure Database for PostgreSQL-超大规模 (Citus) '
description: 预付 Azure Database for PostgreSQL (Citus) 计算具有保留容量的资源。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: a5ce99927ce4cd2b04b5dd5cb865299b4be84ecb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86519790"
---
# <a name="prepay-for-azure-database-for-postgresql---hyperscale-citus-compute-resources-with-reserved-capacity"></a>预付的 Azure Database for PostgreSQL (Citus) 计算具有保留容量的资源

Azure Database for PostgreSQL –与即用即付价格相比，超大规模 (Citus) 现可帮助节省计算资源的费用。 使用超大规模 (Citus) 预留容量，你可以在一年或三年的时间内对超大规模 (Citus) 服务器组进行前期承诺，以获得有关计算成本的重要折扣。 若要购买超大规模 (Citus) 预留容量，需要指定 Azure 区域、预留条款和计费频率。

> [!IMPORTANT]
> 本文介绍 Azure Database for PostgreSQL (Citus) 的保留容量。 有关 Azure Database for PostgreSQL –单一服务器的预留容量的信息，请参阅 [Azure Database for PostgreSQL 的预付-单服务器计算资源和预留容量](/azure/postgresql/concept-reserved-pricing)。

不需要将保留分配给特定的超大规模 (Citus) 服务器组。 已运行的超大规模 (Citus) 服务器组或新部署的服务器组将自动获得保留价格的好处。 购买预订后，将事先预付一年或三年的计算成本。 购买预订后，就不再按现用现付费率对超大规模 (Citus) 与预留属性匹配的计算费用。 

预订不涉及与超大规模 (Citus) 服务器组关联的软件、网络或存储费用。 在保留期结束时，计费权益过期，超大规模 (Citus) 服务器组按即用即付价格计费。 保留不 autorenew。 有关定价信息，请参阅 [Azure Database for PostgreSQL-超大规模 (Citus) 保留容量产品](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)。

你可以在 [Azure 门户](https://portal.azure.com/)中购买超大规模 (Citus) 保留容量。 通过[提前付款或按月付款](https://docs.microsoft.com/azure/cost-management-billing/reservations/monthly-payments-reservations)的方式为预留付款。 购买保留容量：

* 对于至少一个企业协议 (EA) 或按现用现付费率的个人订阅，你必须拥有 "所有者" 角色。
* 对于企业协议订阅，必须在[EA 门户](https://ea.azure.com/)中启用 "**添加保留实例**"。 或者，如果禁用此设置，则你必须是订阅的企业协议管理员。
* 对于云解决方案提供商 (CSP) 计划中，只有管理员代理或销售代理才能购买超大规模 (Citus) 预留容量。

要了解企业协议客户和即用即付客户如何针对预订购买付费，请参阅：
- [了解企业协议注册的 Azure 保留使用情况](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
- [了解即用即付订阅的 Azure 保留使用情况](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)

## <a name="determine-the-right-server-group-size-before-purchase"></a>在购买之前确定正确的服务器组大小

保留的大小基于在特定区域中的超大规模 (Citus) 服务器组中的现有或即将部署协调器和辅助角色节点所使用的计算总量。

例如，假设你正在运行一个超大规模 (Citus) 服务器组，其中包含16个 vCore 协调器和 3 8 vCore 辅助角色节点。 接下来，假设你计划在下个月中部署一个额外的超大规模 (Citus) 服务器组，其中包含 32 vCore 协调器和 2 4 vCore 工作节点。 同时假设你至少需要一年的这些资源。

在这种情况下，请购买为期一年的保留期：

* 协调器节点总共 16 Vcore + 32 Vcore = 48 Vcore
* 总计3个节点 x 8 Vcore + 2 个节点 x 4 Vcore = 24 + 8 = 32 Vcore 适用于辅助角色节点

## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>购买 Azure Database for PostgreSQL 保留的容量

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 选择“所有服务” > “预订”。
1. 选择“添加”  。 在 " **购买预订** " 窗格中，选择 " **Azure Database for PostgreSQL** "，为 PostgreSQL 数据库购买新预订。
1. 选择要购买的 **超大规模 (Citus) 计算** 类型，然后单击 " **选择**"。
1. 在 " **产品** " 选项卡上查看所选计算类型的数量。
1. 继续转到 " **购买 + 审阅** " 选项卡完成购买。

下表描述了必填字段。

| 字段        | 说明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 订阅 | 用于支付 Azure Database for PostgreSQL 预留容量预留的订阅。 订阅上的付款方式将收取 Azure Database for PostgreSQL 预留容量预留的前期成本。 订阅类型必须是企业协议 (产品/服务： BC-OP-NT-AZR-Ms-azr-0017p 或 BC-OP-NT-AZR-Ms-azr-0148p) ，或者使用即用即付定价 (产品/服务定价的单个协议： MS-BC-OP-NT-AZR-Ms-azr-0003p 或 bc-op-nt-azr-0023P) 。 对于企业协议订阅，将从注册的货币承诺余额中扣除费用，或者将费用收费为超额。 对于使用即用即付定价的单个订阅，将对订阅上的信用卡或发票付款方式收取费用。                                                                                  |
| 范围        | VCore 预订的范围可以包含一个订阅或多个订阅 (共享范围) 。 如果选择 " **共享**"，vCore 预订折扣将应用于计费上下文内任何订阅中运行的超大规模 (Citus) 服务器组。 对于企业协议客户，共享范围是注册，并且包括注册中的所有订阅。 对于即用即付客户，共享范围是由帐户管理员创建的所有即用即付订阅。 如果选择 " **单个订阅**"，则 vCore 预订折扣将应用到此订阅中的超大规模 (Citus) 服务器组。 如果选择 " **单个资源组**"，则预订折扣将应用于所选订阅中的超大规模 (Citus) 服务器组以及该订阅内的所选资源组。 |
| 区域       | Azure Database for PostgreSQL –超大规模 (Citus 所涵盖的 Azure 区域) 保留容量预留。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| 术语         | 一年或三年。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| 数量     | 在超大规模 (Citus 中采购的计算资源量) 保留容量保留。 具体而言，将保留所选 Azure 区域中的协调器或辅助角色节点的数量，这些 Vcore 将获得计费折扣。 例如，如果你正在运行 (或计划运行) 超大规模 (Citus) 服务器组，其中的总计算能力为64协调器节点 Vcore，32工作节点 Vcore 在美国东部区域，请分别将数量指定为协调器和工作节点的64和32，以最大程度地提高所有服务器的权益。                                                                                                                                                                                                                                                     |



## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

可以在一定的限制下对预留执行取消、交换或退款操作。 有关详细信息，请参阅 [Azure 预订的自助服务交换和退款](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund)。

## <a name="vcore-size-flexibility"></a>vCore 大小灵活性

vCore 大小的灵活性可帮助你在一个区域内增加或减少协调器和辅助角色节点，而不会丢失保留容量权益。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

VCore 预订折扣将自动应用于与 Azure Database for PostgreSQL 保留容量预留范围和属性匹配的超大规模 (Citus) 服务器组。 可以通过 Azure 门户、PowerShell、Azure CLI 或 API 更新 Azure Database for PostgreSQL 的作用域 (Citus) 保留容量预留。

若要了解有关 Azure 预留的详细信息，请参阅以下文章：

* [什么是 Azure 预留？](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
* [管理 Azure 预留](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [了解 Azure 保留折扣](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [了解即用即付订阅的保留使用情况](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-postgresql)
* [了解企业协议注册的保留使用情况](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [合作伙伴中心云解决方案提供商计划中的 Azure 保留](https://docs.microsoft.com/partner-center/azure-reservations)
