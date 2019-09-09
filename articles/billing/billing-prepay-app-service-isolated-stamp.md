---
title: 通过保留容量节省 Azure App Service 隔离的戳记费用
description: 了解如何节省预留容量 Azure App Service 隔离的戳记。
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.openlocfilehash: 5161f18499c082b7064eec2be612557ba09eec97
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806361"
---
# <a name="save-costs-for-azure-app-service-isolated-stamp-fee-with-reserved-capacity"></a>通过保留容量节省 Azure App Service 隔离的戳记费用

你可以通过提交到你的戳记使用期限为三年的预订来节省 Azure App Service 隔离的戳记费。 若要购买独立的戳记费用保留容量，你需要选择将在其中部署 stamp 的 Azure 区域以及要购买的 stamp 数量。

当你购买预订时，与预订属性匹配的独立图章费使用量不再按即用即付费率收费。 预订将自动应用于与预留容量范围和区域匹配的隔离 stamp 的数目。 不需要为隔离的 stamp 分配预留。 预订不适用于工作人员，因此与 stamp 关联的任何其他资源将单独收费。

当保留容量到期时，隔离的戳记会继续运行，但会按即用即付费率计费。 预留容量不会自动续订。

## <a name="determine-the-right-reservation-to-purchase"></a>确定要购买的正确预订

购买预订后，你将在今后三年内使用保留数量进行提交。 检查你的使用情况数据，确定你始终使用的独立应用服务戳记数量，并可能会在将来使用。

此外，请确保了解独立戳记如何发出 Linux 或 Windows 计量器。

- 默认情况下，空的独立标记将发出 Windows 戳记计量器。 例如，未部署工作线程。 如果 Windows 辅助角色部署在戳记上，它会继续发出此指标。
- 如果部署 Linux 辅助角色，则计量器将更改为 Linux 戳记计量器。
- 在部署 Linux 和 Windows 辅助角色的情况下，戳记会发出 Windows 计量器。

因此，在戳记的整个生命周期内，戳记会在 Windows 和 Linux 之间发生变化。

如果在戳记上有一个或多个 Windows 工作线程，请购买 Windows 戳记保留。 仅当你计划在戳记上_只有_linux 辅助角色时，才应购买 linux 戳记保留。

## <a name="buy-isolated-stamp-reserved-capacity"></a>购买独立的戳记保留容量

可以在[Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D)中购买独立的戳记保留容量。 提前支付预订费用，[按月](billing-monthly-payments-reservations.md)支付。 若要购买预留容量，你必须拥有至少一个企业订阅的所有者角色，或具有即用即付费率的单个订阅。

- 对于企业订阅，必须在[EA 门户](https://ea.azure.com/)中启用 "**添加保留实例**" 选项。 或者，如果禁用此设置，则必须是 EA 管理员。
- 对于云解决方案提供商（CSP）程序，只有管理代理或销售代理可以购买 SQL 数据仓库保留容量。

**购买：**

1. 请参阅[Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D)。
1. 选择一个订阅。 使用 "**订阅**" 列表选择用于支付预留容量的订阅。 订阅的付款方式支付了预留容量的费用。 订阅类型必须为企业协议（套餐编号：MS-AZR-0017P 或 MS-AZR-0148P），或即用即付（产品/服务编号：BC-OP-NT-AZR-Ms-azr-0003p 或 BC-OP-NT-AZR-0023P）或 CSP 订阅。
    - 对于企业订阅，从注册的货币承诺余额中扣除费用或作为超额收取费用。
    - 对于即用即付订阅，将向订阅的信用卡或发票付款方式收取费用。
1. 选择一个**范围**以选择订阅作用域。
    - **单个资源组范围**-仅将预订折扣应用于所选资源组中的匹配资源。
    - **单个订阅范围**-将预订折扣应用于所选订阅中的匹配资源。
    - **共享作用域**-将预订折扣应用于计费上下文中符合条件的订阅中的匹配资源。 对于企业协议客户，计费上下文是注册。 对于使用即用即付费率的各个订阅，计费范围是由帐户管理员创建的所有符合条件的订阅。
1. 选择一个**区域**，以选择预留容量涵盖的 Azure 区域，并将该预留添加到购物车。
1. 选择一个独立的计划类型，然后单击 "**选择**"。  
    ![实例](./media/billing-prepay-app-service-isolated-stamp/app-service-isolated-stamp-select.png)
1. 输入要保留的独立应用服务戳记的数量。 例如，如果数量为三个，则会向你提供三个保留的区域。 单击“下一步:**查看 + 购买**。
1. 查看并单击 "**立即购买**"。

购买后，请前往[预订](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)以查看购买状态并随时监视。

## <a name="cancel-exchange-or-refund-reservations"></a>取消、交换或退款预订

您可以取消、交换或退款保留，但有一些限制。 有关详细信息，请参阅[Azure 预订的自助服务交换和退款](billing-azure-reservations-self-service-exchange-and-refund.md)。

## <a name="discount-application-shown-in-usage-data"></a>使用情况数据中显示的折扣应用程序

对于获取预订折扣的使用量，使用情况数据的有效价格为零。 使用情况数据显示每个预订中每个 stamp 实例的预订折扣。

有关预订折扣在使用情况数据中的显示方式的详细信息，请参阅[获取企业协议预订成本和使用情况](billing-understand-reserved-instance-usage-ea.md)（如果你是企业协议（EA）客户）。 否则，请参阅[使用即用即付费率了解个人订阅的 Azure 保留使用情况](billing-understand-reserved-instance-usage.md)。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Azure 预订的详细信息，请参阅以下文章：
  - [什么是 Azure 预订？](billing-save-compute-costs-reservations.md)
  - [了解如何应用 Azure App Service 隔离戳记预订折扣](billing-reservation-discount-app-service-isolated-stamp.md)
  - [了解企业合约的预留使用情况](billing-understand-reserved-instance-usage-ea.md)
