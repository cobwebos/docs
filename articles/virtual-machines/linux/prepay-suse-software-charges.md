---
title: 为软件计划预付-Azure 预订
description: 了解如何预先为软件计划投入资金，以节省即用即付费用。
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 3bb7a62433993f1af26b1ce8bcb4ed258c34623c
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75973128"
---
# <a name="prepay-for-azure-software-plans"></a>为 Azure 软件计划预付

当你在 Azure 中为你的 SUSE 和 RedHat 软件使用进行预付时，你可以通过即用即付成本节省资金。 折扣仅适用于 SUSE 和 RedHat 计量，不适用于虚拟机使用量。 可以为虚拟机单独购买预留，以实现额外的节省。

可以在 Azure 门户中购买 SUSE 和 RedHat 软件计划。 若要购买计划：

- 对于至少一个企业或个人订阅，你必须至少拥有一个使用即用即付定价的所有者角色。
- 对于企业订阅，必须在 [EA 门户](https://ea.azure.com/)中启用“添加预留实例”选项。 如果禁用此设置，则必须是订阅的 EA 管理员。
- 对于云解决方案提供商（CSP）程序，管理代理或销售代理可以购买软件计划。

## <a name="buy-a-software-plan"></a>购买软件计划

1. 登录到 Azure 门户并中转到 "[预订](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)"。
2. 单击 "**添加**"，然后选择要购买的软件计划。
填写必填字段。 任何与所购买内容的属性相匹配的 SUSE Linux VM 或 RedHat VM 均可获得折扣。 实际获得折扣的部署数取决于所选范围和数量。
3. 选择一个订阅。 它用于为计划付费。
订阅支付方法将收取预订的前期成本。 订阅类型必须是企业协议（产品/服务编号： BC-OP-NT-AZR-Ms-azr-0017p 或 BC-OP-NT-AZR-Ms-azr-0148p）或使用即用即付定价的个人协议（产品/服务编号： MS-BC-OP-NT-AZR-Ms-azr-0003p 或-bc-op-nt-azr）。
    - 对于企业订阅，从注册的货币承诺余额中扣除费用或作为超额收取费用。
    - 对于使用即用即付定价的单个订阅，将对订阅的信用卡或发票付款方式收取费用。
4. 选择一个范围。 范围可以包含一个订阅或多个订阅（共享范围）。
    - 单个订阅-计划折扣应用于订阅中的匹配使用情况。
    - 共享-计划折扣应用于计费上下文中任何订阅中的匹配实例。 对于企业客户，计费上下文是注册，并且包括注册中的所有订阅。 对于使用即用即付定价客户的单个计划，计费上下文是由帐户管理员创建的即用即付定价订阅的所有计划。
5. 选择产品以选择 VM 大小和映像类型。 折扣仅适用于所选 VM 大小。
6. 选择一年或三年的期限。
7. 选择数量，即可以获取计费折扣的预付 VM 实例的数目。
8. 将产品添加到购物车，查看和购买。

预订折扣将自动应用于你提前支付的软件计量器。 计划未涵盖 VM 计算费用。 可以单独购买 VM 预留。

## <a name="discount-applies-to-different-suse-vm-sizes"></a>折扣适用于不同的 SUSE VM 大小

与保留 VM 实例一样，SUSE Linux 计划提供实例大小的灵活性。 即使部署的 VM 的大小不同于所购买的 SUSE 计划，你的折扣仍适用。 有关详细信息，请参阅[了解如何应用软件计划折扣](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)。

## <a name="redhat-plan-discount"></a>RedHat 计划折扣

计划仅适用于 Red Hat Enterprise Linux 虚拟机。 此折扣不适用于 RedHat Enterprise Linux SAP HANA Vm 或 RedHat Enterprise Linux SAP Business Apps Vm。

RedHat 计划折扣仅适用于你在购买时选择的 VM 大小。 在购买后，不能退还或交换 RHEL 计划。


## <a name="cancellation-and-exchanges-not-allowed"></a>不允许取消和交换

您无法取消或交换您购买的 SUSE 或 RedHat 计划。 请检查你的使用情况，确保购买的是正确的计划。 若要帮助确定要购买的内容，请参阅[了解如何应用软件计划折扣](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

若要了解如何管理预留，请参阅[管理 Azure 预留](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)。

若要了解详细信息，请参阅以下文章：

- [什么是 Azure 预订？](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [管理 Azure 中的预留](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [了解如何应用 SUSE 预留折扣](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)
- [了解即用即付订阅的预留使用情况](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [了解企业合约的预留使用情况](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
