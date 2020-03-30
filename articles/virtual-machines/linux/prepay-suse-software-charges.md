---
title: 软件计划的预付款 - Azure 预订
description: 了解如何为软件计划预付费用，以节省您即用即付成本的费用。
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 3bb7a62433993f1af26b1ce8bcb4ed258c34623c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973128"
---
# <a name="prepay-for-azure-software-plans"></a>为 Azure 软件计划预付

在 Azure 中预付费 SUSE 和 RedHat 软件使用情况时，可以节省即用即付成本。 折扣仅适用于 SUSE 和 RedHat 仪表，不适用于虚拟机使用情况。 您可以单独购买虚拟机的预订，以节省更多费用。

您可以在 Azure 门户中购买 SUSE 和 RedHat 软件计划。 若要购买计划：

- 您必须具有至少一个企业或个人订阅的所有者角色，并带有即用即付定价。
- 对于企业订阅，必须在 [EA 门户](https://ea.azure.com/)中启用“添加预留实例”选项****。 如果禁用该设置，则必须是订阅的 EA 管理员。
- 对于云解决方案提供商 （CSP） 计划，管理员代理或销售代理可以购买软件计划。

## <a name="buy-a-software-plan"></a>购买软件计划

1. 登录到 Azure 门户并转到["预订](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)"。
2. 单击"**添加**"，然后选择要购买的软件计划。
填写必填字段。 任何与您购买的产品的属性相匹配的 SUSE Linux VM 或 RedHat VM 都会获得折扣。 实际获得折扣的部署数取决于所选范围和数量。
3. 选择一个订阅。 它用来支付计划的费用。
订阅付款方式按预订的前期费用收费。 订阅类型必须是企业协议（产品/服务编号：MS-AZR-0017P 或 MS-AZR-0148P）或具有即用即付定价（产品/服务编号：MS-AZR-0003P 或 MS-AZR-0023P）的个人协议。
    - 对于企业订阅，从注册的货币承诺余额中扣除费用或作为超额收取费用。
    - 对于具有即用即付定价的个人订阅，费用将按订阅的信用卡或发票付款方式计费。
4. 选择一个范围。 范围可以包含一个订阅或多个订阅（共享范围）。
    - 单个订阅 - 计划折扣应用于订阅中的匹配使用情况。
    - 共享 - 计划折扣应用于计费上下文中任何订阅中的匹配实例。 对于企业客户，计费上下文是注册，包括注册中的所有订阅。 对于具有即用即付定价客户的单个计划，计费上下文是所有由帐户管理员创建的即用即付定价订阅的单个计划。
5. 选择产品以选择 VM 大小和映像类型。 折扣仅适用于所选的 VM 大小。
6. 选择一年或三年期限。
7. 选择数量，即可获取计费折扣的预付费 VM 实例数。
8. 将产品添加到购物车中，查看和购买。

预订折扣将自动应用于您预付的软件仪表。 VM 计算费用不在计划范围内。 您可以单独购买 VM 预留。

## <a name="discount-applies-to-different-suse-vm-sizes"></a>折扣适用于不同的 SUSE VM 大小

与保留的 VM 实例一样，SUSE Linux 计划提供实例大小灵活性。 即使您部署的 VM 大小与您购买的 SUSE 计划不同，您的折扣也会适用。 有关详细信息，请参阅[了解如何应用软件计划折扣](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)。

## <a name="redhat-plan-discount"></a>RedHat 计划折扣

计划仅适用于红帽企业 Linux 虚拟机。 折扣不适用于红帽企业 Linux SAP HANA VM 或红帽企业 Linux SAP 业务应用 VM。

RedHat 计划折扣仅适用于您在购买时选择的 VM 大小。 RHEL 计划购买后不能退款或交换。


## <a name="cancellation-and-exchanges-not-allowed"></a>不允许取消和交换

您无法取消或交换您购买的 SUSE 或 RedHat 计划。 请检查你的使用情况，确保购买的是正确的计划。 有关确定购买内容的帮助，请参阅[了解如何应用软件计划折扣](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

要了解如何管理预留，请参阅[管理 Azure 预留](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)。

若要了解详细信息，请参阅以下文章：

- [什么是 Azure 预留？](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [管理 Azure 中的预留](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [了解如何应用 SUSE 预留折扣](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)
- [了解即用即付订阅的预留使用情况](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [了解企业合约的预留使用情况](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
