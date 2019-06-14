---
title: 预付款购买的软件计划-Azure 预订 |Microsoft Docs
description: 了解如何可以预付款购买的软件计划可节省即用即付成本。
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2019
ms.author: banders
ms.openlocfilehash: e15dcdbbcaed32d836bb751ef93ce17e90bd6905
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60771237"
---
# <a name="prepay-for-azure-software-plans"></a>为 Azure 软件计划预付

时你预付费用在 Azure 中您 SUSE 和 RedHat 软件使用情况，可通过即用即付成本节省开支。 折扣仅适用于 SUSE 和 RedHat 标准而不是在虚拟机使用情况。 您可以购买保留虚拟机分别以节省更多空间。

您可以购买在 Azure 门户中的 SUSE 和 RedHat 软件计划。 若要购买计划：

- 必须具有至少一个企业或即用即付订阅的所有者角色。
- 对于企业订阅**添加保留实例**必须在启用选项[EA 门户](https://ea.azure.com/)。 如果禁用此设置，则您必须是 EA 管理员的订阅。
- 对于云解决方案提供商 (CSP) 程序中，管理代理或销售代理可以购买的软件计划。

## <a name="buy-a-software-plan"></a>购买的软件计划

1. 登录到 Azure 门户并转到[预订](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)。
2. 单击**添加**，然后选择你想要购买的软件计划。
填写必填字段。 所有 SUSE Linux VM 或与您购买的特性相匹配的 RedHat VM 都获取该折扣。 实际获得折扣的部署数取决于所选范围和数量。
3. 选择一个订阅。 它用于购买计划。
订阅付款方式收取预订的预付费用。 订阅类型必须为企业协议 (产品/服务编号：MS-AZR-0017P 或 MS-AZR-0148P），或即用即付（产品/服务编号：MS-AZR-0003P 或 MS-AZR-0023P）。
    - 对于企业订阅，从注册的货币承诺余额中扣除费用或作为超额收取费用。
    - 对于即用即付订阅，订阅的信用卡或发票付款方式计费费用。
4. 选择作用域。 范围可以包含一个订阅或多个订阅（共享范围）。
    - 单个订阅的计划折扣应用到匹配的订阅中的使用情况。
    - （共享）-计划折扣将应用到匹配的任何订阅中计费上下文实例中。 适用于企业客户计费上下文注册并在注册中包含的所有订阅。 对于即用即付客户计费上下文是由帐户管理员创建的所有即用即付订阅。
5. 选择要选择 VM 大小和图像类型的产品。 折扣适用于所选 VM 大小。
6. 选择 1 年或三年的术语。
7. 选择数量，即可以获得计费折扣的预付 VM 实例数。
8. 将产品添加到购物车、 查看和购买。

预订折扣将自动应用于预支付软件计量。 计划不涵盖 VM 的计算费用。 可以单独购买的 VM 保留项。

## <a name="discount-applies-to-different-suse-vm-sizes"></a>折扣适用于不同的 SUSE VM 大小

保留 VM 实例，例如 SUSE Linux 计划提供实例大小灵活性。 享受折扣甚至适用于部署是从 SUSE 计划购买的不同大小的 VM。 有关详细信息，请参阅[了解如何应用软件计划折扣](../../billing/billing-understand-suse-reservation-charges.md)。

## <a name="redhat-plan-discount"></a>RedHat 计划折扣

计划是仅适用于 Red Hat Enterprise Linux 虚拟机。 折扣不适用于 RedHat Enterprise Linux SAP HANA Vm 或 RedHat Enterprise Linux SAP 业务应用程序 Vm。

RedHat 计划折扣仅适用于在购买时选择的 VM 大小。 无法退款或购买后交换 RHEL 计划。


## <a name="cancellation-and-exchanges-not-allowed"></a>不允许取消和交换

无法取消或 exchange 购买 SUSE 或 RedHat 计划。 请检查你的使用情况，确保购买的是正确的计划。 若要确定要购买的帮助，请参阅[了解如何应用软件计划折扣](../../billing/billing-understand-suse-reservation-charges.md)。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

若要了解如何管理预留，请参阅[管理 Azure 预留](../../billing/billing-manage-reserved-vm-instance.md)。

若要了解更多信息，请参阅下列文章：

- [什么是 Azure 预订？](../../billing/billing-save-compute-costs-reservations.md)
- [管理 Azure 中的预留](../../billing/billing-manage-reserved-vm-instance.md)
- [了解如何应用 SUSE 预留折扣](../../billing/billing-understand-suse-reservation-charges.md)
- [了解即用即付订阅的预留使用情况](../../billing/billing-understand-reserved-instance-usage.md)
- [了解企业合约的预留使用情况](../../billing/billing-understand-reserved-instance-usage-ea.md)
