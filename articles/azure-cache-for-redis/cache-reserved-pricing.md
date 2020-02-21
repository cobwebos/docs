---
title: 为计算预付预留容量-Azure Cache for Redis
description: 预付 Azure Cache for Redis 计算资源的预留容量
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: aded023c9f4c045f612e33d32c1e3ac71afddf02
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77530297"
---
# <a name="prepay-for-azure-cache-for-redis-compute-resources-with-reserved-capacity"></a>预付 Azure Cache for Redis 计算资源的预留容量

与即用即付价格相比，适用于 Redis 的 Azure 缓存现在可帮助节省计算资源的费用。 使用 Azure Cache for Redis 保留容量，你可以在一年或三年期内针对缓存提前做出承诺，以获得有关计算成本的重要折扣。 若要为 Redis 保留容量购买 Azure 缓存，需要指定 Azure 区域、服务层和术语。

无需为 Redis 实例的特定 Azure 缓存分配保留。 已为 Redis 运行的 Azure 缓存或新部署的 Azure 缓存会自动获得预留价格的权益，直至保留缓存大小。 购买预留容量便会预付为期一年或三年的计算资源费用。 一旦你购买了预订，就不再按即用即付费率对用于 Redis 计算费用的 Azure 缓存进行收费。 预订不涉及与缓存相关的网络或存储费用。 在保留期结束时，计费权益过期，Azure Cache for Redis 将按即用即付价格计费。 虚拟机预留实例不自动续订。 有关定价信息，请参阅[Azure Cache For Redis reserved 容量产品](https://azure.microsoft.com/pricing/details/cache)。

可以在[Azure 门户](https://portal.azure.com/)中为 Redis 保留容量购买 Azure 缓存。 购买保留容量：

* 对于至少一个企业或个人订阅，你必须以即用即付费率作为所有者角色。
* 对于企业订阅，必须在 [EA 门户](https://ea.azure.com/)中启用“添加预留实例”。 或者，如果禁用了该设置，则必须是订阅的 EA 管理员。
* 对于云解决方案提供商（CSP）程序，只有管理代理或销售代理可以为 Redis 保留容量购买 Azure Cache。

有关企业客户和即用即付客户如何针对预订购买付费的详细信息，请参阅[了解企业注册的 azure 保留使用情况](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)和[了解即用即付订阅的 azure 保留使用情况](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)。


## <a name="determine-the-right-cache-size-before-purchase"></a>在购买之前确定正确的缓存大小

保留的大小应基于特定区域中现有或即将部署的缓存使用的计算总量，并使用相同的服务层。

例如，假设你正在运行一个通用的 Gen5 – 32 vCore 缓存，以及两个经过优化的内存 Gen5 – 16 vCore 缓存。 接下来，假设你计划在下个月中部署一个额外的常规用途： Gen5 – 32 vCore 数据库服务器和一个内存优化的 Gen5 – 16 vCore 数据库服务器。 假设你知道，你将需要至少1年的这些资源。 在这种情况下，你应该购买64（2x32） Vcore，为单一数据库的1年保留-Gen5，为单一数据库内存优化了48（2x16 + 16） vCore 1 年保留-Gen5


## <a name="buy-azure-cache-for-redis-reserved-capacity"></a>为 Redis 保留容量购买 Azure 缓存

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 选择“所有服务” > “预订”。
3. 选择 "**添加**"，然后在 "购买预订" 窗格中选择 "**用于 Redis 的 Azure 缓存**"，为缓存购买新的保留。
4. 填写必填字段。 与所选属性匹配的现有或新数据库可以获取预留容量折扣。 获取折扣的 Redis 实例的 Azure 缓存的实际数目取决于所选的范围和数量。


![保留定价概述](media/cache-reserved-pricing/cache-reserved-price.png)


下表描述了必填字段。

| 字段 | 说明 |
| :------------ | :------- |
| 订阅   | 用于为 Redis 保留容量预留支付 Azure 缓存的订阅。 订阅上的付款方式将收取用于 Redis 预留容量预留的 Azure 缓存的前期成本。 订阅类型必须为企业协议（套餐编号：BC-OP-NT-AZR-Ms-azr-0017p 或 BC-OP-NT-AZR-Ms-azr-0148p）或使用即用即付定价的单个协议（产品/服务编号：MS-AZR-0003P 或 MS-AZR-0023P）。 对于企业订阅，从注册的货币承诺余额中扣除费用或作为超额收取费用。 对于使用即用即付定价的单个订阅，将对订阅上的信用卡或发票付款方式收取费用。
| 范围 | 预订的范围可以包含一个订阅或多个订阅（共享范围）。 如果选择： </br></br> **共享**，预订折扣将应用到计费上下文中任何订阅中运行的 Redis 实例的 Azure Cache。 对于企业客户，共享范围是注册范围，包括注册中的所有订阅。 对于即用即付客户，共享范围是由帐户管理员创建的所有即用即付订阅。</br></br> **单个订阅**，预订折扣将应用到此订阅中的 Redis 实例的 Azure Cache。 </br></br> **单个资源组**，预订折扣适用于所选订阅中的 Redis 实例的 Azure 缓存，以及该订阅内的所选资源组。
| 地区 | Azure Cache 为 Redis 保留容量保留提供的 Azure 区域。
| 定价层 | 适用于 Redis 服务器的 Azure 缓存的服务层。
| 术语 | 一年或三年
| 数量 | 在 Azure 缓存中为 Redis 保留容量预留购买的计算资源量。 该数量是所选 Azure 区域中的大量缓存和正在保留的服务层，并将获得计费折扣。 例如，如果你正在运行或计划为 Redis 服务器运行 Azure Cache，其中的总缓存容量为 26 GB，在美国东部区域中，你可以将 "数量" 指定为26，以最大限度地提高所有缓存的权益。

## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

可以在一定的限制下对预留执行取消、交换或退款操作。 有关详细信息，请参阅 [Azure 预留的自助交换和退款](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund)。

## <a name="cache-size-flexibility"></a>缓存大小灵活性

缓存大小的灵活性可帮助你在服务层和区域内纵向扩展或缩减，而不会丢失保留容量权益。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

预订折扣将自动应用于与预订范围和属性匹配的 Redis 实例的 Azure Cache。 可以通过 Azure 门户、PowerShell、Azure CLI 或 API 更新预留的范围。

*  若要了解如何将保留容量折扣应用于 Redis 的 Azure 缓存，请参阅[了解 azure 保留折扣](../cost-management-billing/reservations/understand-azure-cache-for-redis-reservation-charges.md)

* 若要了解有关 Azure 预订的详细信息，请参阅以下文章：

    * [什么是 Azure 预订？](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
    * [管理 Azure 预留项](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
    * [了解 Azure 预留折扣](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
    * [了解即用即付订阅的预留使用情况](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mysql)
    * [了解企业合约的预留使用情况](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
    * [合作伙伴中心云解决方案提供商 (CSP) 计划中的 Azure 预订](https://docs.microsoft.com/partner-center/azure-reservations)

