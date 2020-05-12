---
title: 通过预留容量为 Azure 应用服务节省费用
description: 了解如何通过预留容量节省 Azure 独立应用服务印花费。
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: banders
ms.openlocfilehash: d6d91b455501e2deec4f5ea36e48fafd17144b2c
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628140"
---
# <a name="save-costs-for-azure-app-service-isolated-stamp-fee-with-reserved-capacity"></a>通过预留容量节省 Azure 独立应用服务印花费

可以通过承诺在三年内使用预留的量，节省 Azure 独立应用服务印花费。 若要购买独立印花费预留容量，需选择要在其中部署印花的 Azure 区域，以及要购买的印花数。

在你购买预留容量后，与预留属性匹配的独立印花费使用量不再按即用即付费率进行收费。 预留自动应用到与预留容量范围和区域匹配的独立印花数。 不需为独立印花分配预留。 预留不适用于辅助角色，因此与印花相关的任何其他资源将单独收费。

如果预留容量到期，独立印花会继续运行，但按即用即付的费率进行计费。 预留容量不会自动续订。

## <a name="determine-the-right-reservation-to-purchase"></a>确定要购买的正确预留

购买预留是指你承诺在接下来的三年中使用预留的量。 查看使用数据，确定你在未来会持续使用和可能使用的应用服务独立印花量。

另外，确保你了解独立印花如何发出 Linux 或 Windows 计量。

- 默认情况下，空的独立印花发出 Windows 印花计量。 例如，在没有部署辅助角色的情况下。 如果 Windows 辅助角色部署到印花上，它会继续发出此计量。
- 如果部署 Linux 辅助角色，则计量更改为 Linux 印花计量。
- 如果同时部署 Linux 和 Windows 辅助角色，则印花发出 Windows 记量。

因此，印花计量可以在印花生存期中在 Windows 和 Linux 之间来回变换。

如果印花上有一个或多个 Windows 辅助角色，请购买 Windows 印花预留。 只购买 Linux 印花预留的前提是计划只  在印花上部署 Linux 辅助角色。

## <a name="buy-isolated-stamp-reserved-capacity"></a>购买独立印花预留容量

可在 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D)中购买独立印花预留容量。 通过[提前付款或按月付款](monthly-payments-reservations.md)的方式为预留付款。 若要购买预留容量，你必须拥有至少一个企业订阅的所有者角色，或者拥有使用即用即付费率的单个订阅的所有者角色。

- 对于企业订阅，必须在 [EA 门户](https://ea.azure.com/)中启用“添加预留实例”选项  。 或者，如果禁用了该设置，则必须是 EA 管理员。
- 对于云解决方案提供商 (CSP) 计划，只有管理员代理或销售代理可以购买 Azure Synapse Analytics 预留容量。

**若要进行购买，请执行以下操作：**

1. 转到 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D)。
1. 选择一个订阅。 使用“订阅”列表，选择用于支付预留容量费用的订阅。  将向订阅的付款方式收取预留容量的费用。 订阅类型必须为企业协议（套餐编号：MS-AZR-0017P 或 MS-AZR-0148P），或即用即付（产品/服务编号：MS-AZR-0003P 或 MS-AZR-0023P）或 CSP 订阅。
    - 对于企业订阅，从注册的货币承诺余额中扣除费用或作为超额收取费用。
    - 对于即用即付订阅，将向订阅的信用卡或发票付款方式收取费用。
1. 选择“范围”，以便选择订阅范围。 
    - **单个资源组范围** - 仅将预留折扣应用到所选资源组中匹配的资源。
    - **单个订阅范围** - 将预留折扣应用到所选订阅中匹配的资源。
    - **共享范围** - 将预留折扣应用到计费上下文中符合条件的订阅中的匹配资源。 对于企业协议客户，计费上下文为注册。 对于采用即用即付费率的单个订阅，计费范围是由帐户管理员创建的所有符合条件的订阅。
1. 选择“区域”，以便选择一个  预留容量涵盖的 Azure 区域，然后将预留添加到购物车。
1. 选择一个独立计划类型，然后单击“选择”。   
    ![示例 ](./media/prepay-app-service-isolated-stamp/app-service-isolated-stamp-select.png)
1. 输入要预留的独立应用服务印花的数量。 例如，输入数量 3 即可每个区域获得 3 个预留印花。 单击“下一步:  查看 + 购买”。
1. 检查信息，并单击“立即购买”。 

购买后，转到[预留](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)，查看购买状态并随时对其进行监视。

## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

可以在一定的限制下对预留执行取消、交换或退款操作。 有关详细信息，请参阅 [Azure 预留的自助交换和退款](exchange-and-refund-azure-reservations.md)。

## <a name="discount-application-shown-in-usage-data"></a>在使用数据中显示的折扣应用情况

对于获得预留折扣的使用量，使用数据的有效价格为零。 使用数据显示每个预留中的每个印花实例的预留折扣。

若要详细了解预留折扣在使用数据中的显示情况，而且你是企业协议 (EA) 客户，请参阅[获取企业协议预留成本和使用情况](understand-reserved-instance-usage-ea.md)。 否则，请参阅[了解采用即用即付费率的个人订阅的 Azure 预留使用情况](understand-reserved-instance-usage.md)。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Azure 预订的详细信息，请参阅以下文章：
  - [什么是 Azure 预订？](save-compute-costs-reservations.md)
  - [了解如何应用 Azure 应用服务独立印花预留折扣](reservation-discount-app-service-isolated-stamp.md)
  - [了解企业合约的预留使用情况](understand-reserved-instance-usage-ea.md)
