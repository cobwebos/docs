---
title: 保留计算定价 - Azure 数据库，用于 PostgreSQL - 单个服务器
description: 具有预留容量的后SQL计算资源的 Azure 数据库预付款
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 66d7228e78f03196da0b26249e7f1f86e79d79fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159007"
---
# <a name="prepay-for-azure-database-for-postgresql-compute-resources-with-reserved-capacity"></a>具有预留容量的后SQL计算资源的 Azure 数据库预付款

现在，与即用即付价格相比，PostgreSQL 的 Azure 数据库通过预付费计算资源来帮助您节省资金。 使用适用于 PostgreSQL 预留容量的 Azure 数据库，您可以在 PostgreSQL 服务器上提前承诺一到三年，以获得计算成本的大幅折扣。 要为 PostgreSQL 预留容量购买 Azure 数据库，需要指定 Azure 区域、部署类型、性能层和术语。 </br>

您无需为 PostgreSQL 服务器将预留分配给特定的 Azure 数据库。 已运行的用于 PostgreSQL 或新部署的 Azure 数据库将自动获得保留定价的好处。 购买预留容量便会预付为期一年或三年的计算资源费用。 购买预留后，与预留属性匹配的 PostgreSQL 计算费用的 Azure 数据库将不再按即用即付费率收费。 预留不包括与 PostgreSQL 数据库服务器关联的软件、网络或存储费用。 在预订期限结束时，计费权益将过期，PostgreSQL 的 Azure 数据库以即用即付价格计费。 虚拟机预留实例不自动续订。 有关定价信息，请参阅[Azure 数据库，用于 PostgreSQL 预留容量产品](https://azure.microsoft.com/pricing/details/postgresql/)。 </br>

> [!IMPORTANT]
> 预留容量定价仅适用于用于 PostgreSQL[单个服务器](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---single-server)部署的 Azure 数据库，不适用于[超大规模 Citus](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---hyperscale-citus)部署。

您可以在[Azure 门户](https://portal.azure.com/)中为 PostgreSQL 保留容量购买 Azure 数据库。 要购买预留容量：

* 您必须在至少一个企业或个人订阅的所有者角色中，使用即用即付费率。
* 对于企业订阅，必须在 [EA 门户](https://ea.azure.com/)中启用“添加预留实例”****。 或者，如果禁用了该设置，则必须是订阅的 EA 管理员。
* 对于云解决方案提供商 （CSP） 计划，只有管理代理或销售代理可以购买 Azure 数据库以用于 PostgreSQL 预留容量。 </br>

有关企业客户和即用即付客户如何收取预订费用的详细信息，请参阅[了解企业注册的 Azure 预留使用情况](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)[，并了解即用即付订阅的 Azure 预留使用情况](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)。


## <a name="determine-the-right-server-size-before-purchase"></a>购买前确定正确的服务器大小

预留的大小应基于特定区域内现有或即将部署的服务器使用的计算总量，并使用相同的性能层和硬件生成。</br>

例如，假设您正在运行一个通用数据库，Gen5 = 32 vCore PostgreSQL 数据库，以及两个经过优化的内存，Gen5 = 16 vCore PostgreSQL 数据库。 此外，假设您计划在下个月部署额外的通用用途，Gen5 = 32 vCore 数据库服务器，以及一个内存优化的 Gen5 = 16 vCore 数据库服务器。 假设您知道至少需要这些资源 1 年。 在这种情况下，您应该购买 64 （2x32） vCore，单数据库通用 1 年预订 - Gen5 和 48 （2x16 + 16） vCore 1 年预留，用于优化单个数据库内存 - Gen5


## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>购买 Azure 数据库以进行后容量保留容量

1. 登录到 Azure[门户](https://portal.azure.com/)。
2. 选择**所有服务** > **预订**。
3. 选择 **"添加**"，然后在"购买预留"窗格中，选择**PostgreSQL 的 Azure 数据库**，以便为 PostgreSQL 数据库购买新的预留。
4. 填写所需的字段。 与您选择的属性匹配的现有或新数据库有资格获得预留容量折扣。 获得折扣的 PostgreSQL 服务器的 Azure 数据库的实际数量取决于所选的范围和数量。


![保留定价概述](media/concepts-reserved-pricing/postgresql-reserved-price.png)


下表描述了必填字段。

| 字段 | 描述 |
| :------------ | :------- |
| 订阅   | 用于为 PostgreSQL 预留容量预留的 Azure 数据库付费的订阅。 订阅上的付款方式将收取 PostgreSQL 预留容量预留 Azure 数据库的前期费用。 订阅类型必须是企业协议（产品/服务编号：MS-AZR-0017P 或 MS-AZR-0148P）或具有即用即付定价的单个协议（产品/服务编号：MS-AZR-0003P 或 MS-AZR-0023P）。 对于企业订阅，从注册的货币承诺余额中扣除费用或作为超额收取费用。 对于具有即用即付定价的个人订阅，费用将计入订阅上的信用卡或发票付款方式。
| 范围 | vCore 预留范围可以包括一个订阅或多个订阅（共享范围）。 如果选择： </br></br> **共享**，vCore 预留折扣将应用于 Azure 数据库，用于在计费上下文中任何订阅中运行的 PostgreSQL 服务器。 对于企业客户，共享范围是注册范围，包括注册中的所有订阅。 对于即用即付客户，共享范围是由帐户管理员创建的所有即用即付订阅。</br></br> **单一订阅**，vCore 预留折扣将应用于此订阅中 PostgreSQL 服务器的 Azure 数据库。 </br></br> **单个资源组**，预留折扣应用于所选订阅中的 PostgreSQL 服务器的 Azure 数据库以及该订阅中的选定资源组。
| 区域 | Azure 数据库涵盖的 Azure 区域，用于 PostgreSQL 预留容量保留。
| 部署类型 | 要为其购买预留的 PostgreSQL 资源类型的 Azure 数据库。
| 性能层 | PostgreSQL 服务器 Azure 数据库的服务层。
| 术语 | 一年
| 数量 | 在 Azure 数据库中为 PostgreSQL 预留容量预留而购买的计算资源量。 数量是所选 Azure 区域和"性能"层中保留的 vCore 的多个 vCore，它们将获得计费折扣。 例如，如果您正在运行或计划为 PostgreSQL 服务器运行 Azure 数据库，其总计算容量为美国东部区域的 Gen5 16 vCores，则将数量指定为 16，以最大化所有服务器的好处。

## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

可以在一定的限制下对预留执行取消、交换或退款操作。 有关详细信息，请参阅 [Azure 预留的自助交换和退款](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund)。

## <a name="vcore-size-flexibility"></a>vCore 大小灵活性

vCore 大小灵活性有助于在同一性能层和区域内纵向扩展或收缩，且不会丢失预留容量权益。 

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

vCore 预留折扣将自动应用于与 PostgreSQL 预留容量预留范围和属性的 Azure 数据库匹配的 PostgreSQL 服务器的 Azure 数据库数。 您可以通过 Azure 门户、PowerShell、CLI 或 API 更新 PostgreSQL 预留容量预留的 Azure 数据库的范围。 </br></br>
要了解如何管理用于 PostgreSQL 保留容量的 Azure 数据库，请参阅管理用于 PostgreSQL 保留容量的 Azure 数据库。

若要了解有关 Azure 预订的详细信息，请参阅以下文章：

* [什么是 Azure 预留](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)？
* [管理 Azure 预留](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [了解 Azure 预留折扣](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [了解即用即付订阅的预留使用情况](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-postgresql)
* [了解企业合约的预留使用情况](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [合作伙伴中心云解决方案提供商 (CSP) 计划中的 Azure 预订](https://docs.microsoft.com/partner-center/azure-reservations)
