---
title: 预付费计算与保留容量 - Azure 缓存为 Redis
description: 为具有预留容量的 Redis 计算资源预付 Azure 缓存
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: aded023c9f4c045f612e33d32c1e3ac71afddf02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77530297"
---
# <a name="prepay-for-azure-cache-for-redis-compute-resources-with-reserved-capacity"></a>为具有预留容量的 Redis 计算资源预付 Azure 缓存

Redis 的 Azure 缓存现在通过预付费计算资源与即用即付价格相比，帮助您节省资金。 使用用于 Redis 保留容量的 Azure 缓存，您可以提前承诺在缓存上执行一到三年的周期，以获得计算成本的显著折扣。 要为 Redis 预留容量购买 Azure 缓存，需要指定 Azure 区域、服务层和术语。

不需要为 Redis 实例将预留分配给特定的 Azure 缓存。 已运行的 Redis 或新部署的 Azure 缓存将自动获得保留定价的好处，最多为保留缓存大小。 购买预留容量便会预付为期一年或三年的计算资源费用。 购买预留后，与保留属性匹配的 Redis Azure 缓存计算费用将不再按即用即付费率收费。 预留不包括与缓存关联的网络或存储费用。 在预订期限结束时，计费权益将过期，Redis 的 Azure 缓存以即用即付价格计费。 虚拟机预留实例不自动续订。 有关定价信息，请参阅[Redis 保留容量提供的 Azure 缓存。](https://azure.microsoft.com/pricing/details/cache)

您可以在[Azure 门户](https://portal.azure.com/)中为 Redis 保留容量购买 Azure 缓存。 要购买预留容量：

* 您必须在至少一个企业或个人订阅的所有者角色中，使用即用即付费率。
* 对于企业订阅，必须在 [EA 门户](https://ea.azure.com/)中启用“添加预留实例”****。 或者，如果禁用了该设置，则必须是订阅的 EA 管理员。
* 对于云解决方案提供商 （CSP） 计划，只有管理代理或销售代理可以购买 Azure 缓存以保留容量。

有关企业客户和即用即付客户如何收取预订费用的详细信息，请参阅[了解企业注册的 Azure 预留使用情况](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)，并[了解即用即付订阅的 Azure 预留使用情况](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)。


## <a name="determine-the-right-cache-size-before-purchase"></a>购买前确定正确的缓存大小

预留的大小应基于特定区域内的现有或即将部署的缓存使用的计算总量，并使用相同的服务层。

例如，假设您正在运行一个通用，Gen5 = 32 vCore 缓存，以及两个经过优化的内存，Gen5 = 16 vCore 缓存。 此外，假设您计划在下个月部署额外的通用用途，Gen5 = 32 vCore 数据库服务器，以及一个内存优化的 Gen5 = 16 vCore 数据库服务器。 假设您知道至少需要这些资源 1 年。 在这种情况下，您应该购买 64 （2x32） vCore，单数据库通用 1 年预订 - Gen5 和 48 （2x16 + 16） vCore 1 年预留，用于优化单个数据库内存 - Gen5


## <a name="buy-azure-cache-for-redis-reserved-capacity"></a>为 Redis 预留容量购买 Azure 缓存

1. 登录到 Azure[门户](https://portal.azure.com/)。
2. 选择**所有服务** > **预订**。
3. 选择 **"添加**"，然后在"购买预留"窗格中，选择 **"Azure 缓存"以用于 Redis**以购买缓存的新预留。
4. 填写所需的字段。 与您选择的属性匹配的现有或新数据库有资格获得预留容量折扣。 获得折扣的 Redis 实例的 Azure 缓存的实际数量取决于所选的范围和数量。


![保留定价概述](media/cache-reserved-pricing/cache-reserved-price.png)


下表描述了必填字段。

| 字段 | 描述 |
| :------------ | :------- |
| 订阅   | 用于为 Redis 预留容量预留的 Azure 缓存付费的订阅。 订阅上的付款方式将收取 Redis 预留容量预留的 Azure 缓存的前期费用。 订阅类型必须是企业协议（产品/服务编号：MS-AZR-0017P 或 MS-AZR-0148P）或具有即用即付定价的单个协议（产品/服务编号：MS-AZR-0003P 或 MS-AZR-0023P）。 对于企业订阅，从注册的货币承诺余额中扣除费用或作为超额收取费用。 对于具有即用即付定价的个人订阅，费用将计入订阅上的信用卡或发票付款方式。
| 范围 | 预订的范围可以包含一个订阅或多个订阅（共享范围）。 如果选择： </br></br> **共享**，预留折扣将应用于在计费上下文中任何订阅中运行的 Redis 实例的 Azure 缓存。 对于企业客户，共享范围是注册范围，包括注册中的所有订阅。 对于即用即付客户，共享范围是由帐户管理员创建的所有即用即付订阅。</br></br> **单个订阅**，保留折扣将应用于此订阅中 Redis 实例的 Azure 缓存。 </br></br> **单个资源组**，预留折扣应用于所选订阅中的 Redis 实例的 Azure 缓存以及该订阅中的选定资源组。
| 区域 | Azure 缓存为 Redis 保留容量预留所覆盖的 Azure 区域。
| 定价层 | Redis 服务器的 Azure 缓存的服务层。
| 术语 | 一年或三年
| 数量 | 在 Azure 缓存中为 Redis 预留容量预留而购买的计算资源量。 数量是所选 Azure 区域和服务层中保留的多个缓存，您将获得计费折扣。 例如，如果您正在运行或计划为美国东部区域的总缓存容量为 26 GB 的 Redis 服务器运行 Azure 缓存，则将数量指定为 26，以最大化所有缓存的好处。

## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

可以在一定的限制下对预留执行取消、交换或退款操作。 有关详细信息，请参阅 [Azure 预留的自助交换和退款](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund)。

## <a name="cache-size-flexibility"></a>缓存大小灵活性

缓存大小灵活性可帮助您在服务层和地区内向上或向下扩展，而不会丢失预留容量权益。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

保留折扣将自动应用于与预留范围和属性匹配的 Redis 实例的 Azure 缓存。 可以通过 Azure 门户、PowerShell、Azure CLI 或 API 更新预留的范围。

*  要了解如何将预留容量折扣应用于 Redis 的 Azure 缓存，请参阅[了解 Azure 预留折扣](../cost-management-billing/reservations/understand-azure-cache-for-redis-reservation-charges.md)

* 若要了解有关 Azure 预订的详细信息，请参阅以下文章：

    * [什么是 Azure 预留？](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
    * [管理 Azure 预留](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
    * [了解 Azure 预留折扣](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
    * [了解即用即付订阅的预留使用情况](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mysql)
    * [了解企业合约的预留使用情况](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
    * [合作伙伴中心云解决方案提供商 (CSP) 计划中的 Azure 预订](https://docs.microsoft.com/partner-center/azure-reservations)

