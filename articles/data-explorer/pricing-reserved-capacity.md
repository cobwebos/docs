---
title: 预付 Azure 数据资源管理器标记以节省资金
description: 了解如何购买 Azure 数据资源管理器保留容量以节省 Azure 数据资源管理器成本。
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: c728f3b9a4c10d52d7361ca76e9b40198a6a0dba
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769092"
---
# <a name="prepay-for-azure-data-explorer-markup-units-with-azure-data-explorer-reserved-capacity"></a>为 Azure 数据资源管理器标记单位预付 Azure 数据资源管理器保留容量

与即用即付价格相比，使用 Azure 数据资源管理器通过事先预付为标记单位节省资金。 使用 Azure 数据资源管理器预留容量，你可以在一年或三年的一段时间内做出 Azure 数据资源管理器使用承诺，以获得有关 Azure 数据资源管理器标记成本的重要折扣。 若要购买 Azure 数据资源管理器预留容量，只需指定条款，它将应用于所有区域中所有 Azure 数据资源管理器部署。

购买预订后，会提前支付一年或三年的标记成本。 一旦购买了预订，就不再按即用即付费率对与预订属性匹配的 Azure 数据资源管理器加值费用收费。 已在运行或新部署的 Azure 数据资源管理器群集将自动获得权益。 此保留不涉及与群集关联的计算、网络或存储费用。 需要单独购买这些资源的预留容量。 在保留期结束时，计费权益过期，Azure 数据资源管理器标记单位按即用即付价格计费。 预订不自动续订。 有关定价信息，请参阅[Azure 数据资源管理器定价页](https://azure.microsoft.com/pricing/details/data-explorer/)。

可以在[Azure 门户](https://portal.azure.com)中购买 Azure 数据资源管理器保留容量。 购买 Azure 数据资源管理器保留的容量：

* 你必须是至少一个企业或即用即付订阅的所有者。
* 对于企业订阅，必须在 [EA 门户](https://ea.azure.com)中启用“添加预留实例”。 或者，如果禁用此设置，则必须是订阅上的 EA 管理员。
* 对于云解决方案提供商（CSP）程序，只有管理代理或销售代理可以购买 Azure 数据资源管理器保留容量。

若要详细了解企业客户和即用即付客户对预订购买的费用，请参阅：
* [了解企业注册的 Azure 保留使用情况](../billing/billing-understand-reserved-instance-usage-ea.md) 
* [了解即用即付订阅的 Azure 保留使用情况](../billing/billing-understand-reserved-instance-usage.md)。

## <a name="determine-the-right-markup-usage-before-purchase"></a>在购买之前确定正确的标记用法

保留的大小应基于现有或即将部署的 Azure 数据资源管理器群集使用的 Azure 数据资源管理器标记单位的总数。 标记单位数等于生产中 Azure 数据资源管理器引擎群集核心的数量（不包括开发/测试 SKU）。 

## <a name="buy-azure-data-explorer-reserved-capacity"></a>购买 Azure 数据资源管理器保留的容量

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 选择 "**所有服务**" > **预订**" > **立即购买**"。 选择“添加”
1. 在 "**选择产品类型**" 窗格中，选择 " **azure 数据资源管理器**" 以购买 azure 数据资源管理器标记单位的新预订。 
1. 选择**购买**
1. 填写必填字段。 

    ![购买页](media/pricing-reserved-capacity/purchase-page.png)

1. 查看**成本**部分中 Azure 数据资源管理器标记保留的容量保留容量。
1. 选择“购买”。
1. 选择“查看此预订”以查看购买的状态。

## <a name="cancellations-and-exchanges"></a>取消和交换

如果需要取消 Azure 数据资源管理器保留的容量预留，可能会提前终止12%。 退款基于购买价格的最低价格或预订的当前价格。 退款限制为每年 50,000 美元。 收到的退款为按比例计算出的余额减去 12% 的提前终止费。 若要请求取消，请在 Azure 门户中访问该预留，并选择“退款”创建支持请求。

如果需要将 Azure 数据资源管理器保留的容量保留更改为其他项，可以将其与具有相同或更大值的其他保留进行交换。 新订购项目的期限开始日期不是从交换的订购项目延续。 1 年或 3 年期限从创建新预留开始算起。 若要请求交换，请在 Azure 门户中访问该预留，并选择“交换”创建支持请求。

有关如何交换或退款预订的详细信息，请参阅[保留交换和退款](../billing/billing-azure-reservations-self-service-exchange-and-refund.md)。

## <a name="manage-your-reserved-capacity-reservation"></a>管理保留容量预留

Azure 数据资源管理器标记单位预订折扣将自动应用于与 Azure 数据资源管理器保留容量预留范围和属性匹配的标记单位数。 


> [!NOTE]
> * 可以通过[Azure 门户](https://portal.azure.com)、POWERSHELL、CLI 或通过 API 来更新 Azure 数据资源管理器保留容量保留的范围。
> * 若要了解如何管理 Azure 数据资源管理器保留容量保留，请参阅[管理 azure 数据资源管理器保留容量](../billing/billing-understand-kusto-azuredataexplorer-reservation-charges.md)。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 预留的详细信息，请参阅以下文章：

* [什么是 Azure 预留？](../billing/billing-save-compute-costs-reservations.md)
* [管理 Azure 预留项](../billing/billing-manage-reserved-vm-instance.md)
* [了解 Azure 预留折扣](../billing/billing-understand-reservation-charges.md)
* [了解即用即付订阅的预留使用情况](../billing/billing-understand-reserved-instance-usage.md)
* [了解企业合约的预留使用情况](../billing/billing-understand-reserved-instance-usage-ea.md)
* [合作伙伴中心云解决方案提供商 (CSP) 计划中的 Azure 预订](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。
