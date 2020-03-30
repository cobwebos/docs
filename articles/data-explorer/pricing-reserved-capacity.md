---
title: 预付 Azure 数据资源管理器标记以节省资金
description: 了解如何购买 Azure 数据资源管理器保留容量以节省 Azure 数据资源管理器成本。
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: bbb2108967353b851a0fa0ee610ec30380e3fc50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969271"
---
# <a name="prepay-for-azure-data-explorer-markup-units-with-azure-data-explorer-reserved-capacity"></a>具有 Azure 数据资源管理器预留容量的 Azure 数据资源管理器标记单元的预付款

通过预付费标记单位与即用即付价格相比，使用 Azure 数据资源管理器节省资金。 使用 Azure 数据资源管理器保留容量时，您可以承诺使用 Azure 数据资源管理器一年或三年，以便对 Azure 数据资源管理器的标记成本进行大幅折扣。 要购买 Azure 数据资源管理器保留容量，只需指定术语，它将应用于所有区域中的 Azure 数据资源管理器的所有部署。

通过购买预订，您可以预付一年或三年的加价费用。 购买预留后，Azure 数据资源管理器的标记费用将不再按即用即付费率收取。 Azure 数据资源管理器群集已在运行或新部署的群集将自动获得好处。 此保留不包括与群集关联的计算、网络或存储费用。 这些资源的预留容量需要单独购买。 在预留期限结束时，计费权益将过期，Azure 数据资源管理器标记单位以即用即付价格计费。 预订不会自动续订。 有关定价信息，请参阅[Azure 数据资源管理器定价页](https://azure.microsoft.com/pricing/details/data-explorer/)。

您可以在[Azure 门户](https://portal.azure.com)中购买 Azure 数据资源管理器保留容量。 要购买 Azure 数据资源管理器保留容量：

* 您必须是至少一个企业或即用即付订阅的所有者。
* 对于企业订阅，必须在 [EA 门户](https://ea.azure.com)中启用“添加预留实例”****。 或者，如果禁用该设置，则必须是订阅上的 EA 管理员。
* 对于云解决方案提供商 （CSP） 计划，只有管理员代理或销售代理才能购买 Azure 数据资源管理器预留容量。

有关企业客户和即用即付客户如何收取预订费用的详细信息，请参阅：
* [了解适用于企业合约的 Azure 预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) 
* [了解即用即付订阅的 Azure 预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage.md)。

## <a name="determine-the-right-markup-usage-before-purchase"></a>在购买前确定正确的加价用法

预留的大小应基于现有或即将部署的 Azure 数据资源管理器群集使用的 Azure 数据资源管理器标记单元的总数。 标记单元数等于生产中的 Azure 数据资源管理器引擎群集内核数（不包括开发/测试 SKU）。 

## <a name="buy-azure-data-explorer-reserved-capacity"></a>购买 Azure 数据资源管理器保留容量

1. 登录到 Azure[门户](https://portal.azure.com)。
1. 选择**所有服务** > **预订** > **立即购买**。 选择 **"添加"**
1. 在 **"选择产品类型"** 窗格中，选择**Azure 数据资源管理器**以购买 Azure 数据资源管理器标记单元的新预留。 
1. 选择 **"购买"**
1. 填写必填字段。 

    ![采购页面](media/pricing-reserved-capacity/purchase-page.png)

1. 在 **"成本"** 部分中查看 Azure 数据资源管理器标记预留容量预留的成本。
1. 选择“购买”。****
1. 选择“查看此预订”**** 以查看购买的状态。

## <a name="cancellations-and-exchanges"></a>取消和更换

如果需要取消 Azure 数据资源管理器预留容量预留，可能需要支付 12% 的提前终止费。 退款基于您的购买价格的最低价格或预订的当前价格。 退款限制为每年 50,000 美元。 收到的退款为按比例计算出的余额减去 12% 的提前终止费。 若要请求取消，请在 Azure 门户中访问该预留，并选择“退款”**** 创建支持请求。

如果需要将 Azure 数据资源管理器预留容量预留更改为其他术语，则可以将其更改为其他值相等或更大的保留。 新预留的期限开始日期不是从交换的预留延续。 1 年或 3 年期限从创建新预留开始算起。 若要请求交换，请在 Azure 门户中访问该预留，并选择“交换”**** 创建支持请求。

有关如何交换或退款预订的更多信息，请参阅[预订交换和退款](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

## <a name="manage-your-reserved-capacity-reservation"></a>管理预留容量预留

Azure 数据资源管理器标记单位预留折扣将自动应用于与 Azure 数据资源管理器预留容量预留范围和属性匹配的标记单位数。 


> [!NOTE]
> * 您可以通过[Azure 门户](https://portal.azure.com)、PowerShell、CLI 或通过 API 更新 Azure 数据资源管理器保留容量预留的范围。
> * 要了解如何管理 Azure 数据资源管理器预留容量，请参阅管理[Azure 数据资源管理器保留容量](../cost-management-billing/reservations/understand-azure-data-explorer-reservation-charges.md)。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 预订的详细信息，请参阅以下文章：

* [什么是 Azure 预留？](../cost-management-billing/reservations/save-compute-costs-reservations.md)
* [管理 Azure 预留](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [了解 Azure 预留折扣](../cost-management-billing/reservations/understand-reservation-charges.md)
* [了解即用即付订阅的预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
* [了解企业合约的预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [合作伙伴中心云解决方案提供商 (CSP) 计划中的 Azure 预订](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。
