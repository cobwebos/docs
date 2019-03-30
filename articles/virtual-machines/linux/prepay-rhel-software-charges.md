---
title: 购买 Azure 预订的 Red Hat Enterprise Linux 计划-|Microsoft Docs
description: 了解如何预付款购买的 Red Hat 使用情况和即用即付成本节省资金。
services: virtual-machines-linux
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/18/2019
ms.author: yashar
ms.openlocfilehash: e90241d9da140ca33d1aae8ffc5636f8eb2fbc74
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652904"
---
# <a name="prepay-for-red-hat-enterprise-linux-plans-from-azure-reservations"></a>预付款购买的 Azure 保留项中的 Red Hat Enterprise Linux 计划

预付款购买的 Red Hat 使用情况，即用即付成本节省资金。 折扣仅适用于 Red Hat 指标而不是在虚拟机使用情况。 可以单独购买虚拟机预留，以节省更多成本。

您可以购买在 Azure 门户中的 Red Hat 软件计划。 若要购买计划：

- 你必须至少具有一个企业或即用即付订阅的所有者角色。
- 对于企业订阅，必须在 [EA 门户](https://ea.azure.com)中启用“添加预留实例”。 或者，如果禁用了该设置，则必须是订阅的 EA 管理员。
- 对于云解决方案提供商 (CSP) 程序中，管理代理或销售代理可以购买的 Red Hat 计划。

## <a name="buy-a-red-hat-software-plan"></a>购买的 Red Hat 软件计划

1. 在 Azure 门户中转到[预留](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)。
1. 选择**添加**并选择 Red Hat Linux。
1. 填写必填字段。 任何与您购买的特性相匹配的 Red Hat Linux VM 获取该折扣。 实际获得折扣的部署数取决于所选范围和数量。

    | 字段      | 描述|
    |:------------|:--------------|
    |名称        |此购买的名称。|
    |订阅|用于支付此计划的订阅。 将向订阅的付款方式收取预订的预付费用。 订阅类型必须为企业协议（套餐编号：MS-AZR-0017P 或 MS-AZR-0148P），或即用即付（产品/服务编号：MS-AZR-0003P 或 MS-AZR-0023P）。 对于企业订阅，从注册的货币承诺余额中扣除费用或作为超额收取费用。 对于即用即付订阅，将向订阅的信用卡或发票付款方式收取费用。|
    |范围       |范围可以包含一个订阅或多个订阅（共享范围）。 如果选择： <ul><li>单个订阅的计划折扣应用于此订阅中的 Red Hat Linux 使用情况。 </li><li>（共享）-计划折扣应用于计费上下文中的任何订阅中的 Red Hat Linux 使用情况。 对于企业客户，共享范围是注册范围，包括注册中的所有订阅。 对于即用即付客户，共享范围是由帐户管理员创建的所有即用即付订阅。</li></ul>|
    |计划     |选择 Red Hat Linux 计划。 确定要购买的帮助，请参阅的了解如何应用 Red Hat Linux Enterprise 软件预订折扣。|
    |VM 大小     |Red Hat Linux 定价取决于 VM 上目前的 Vcpu 数量。 选择选项，它表示在 Red Hat Linux Vm 上的 Vcpu 数。|
    |术语        |一年或三年。|
    |数量    |有关计划的购买此 Red Hat Linux 的 Vm 数。 数量是正在运行可以获得计费折扣的 Red Hat Linux 实例的数目。|
1. 选择“购买”。
1. 选择“查看此预订”以查看购买的状态。

预订折扣会自动应用于与预订匹配任何正在运行 Red Hat 虚拟机。 折扣仅适用于 Red Hat 计量。 此计划不涵盖 VM 计算费用。

## <a name="discount-applies-to-different-vm-sizes"></a>折扣适用于不同的 VM 大小

保留 VM 实例，如 Red Hat Linux 计划提供实例大小灵活性。 这意味着你折扣应用甚至在部署是从 Red Hat 计划购买的不同大小的 VM。 有关详细信息，请参阅的了解如何应用 Red Hat Linux Enterprise 软件预订折扣。

## <a name="cancellation-and-exchanges-not-allowed"></a>不允许取消和交换

无法取消或交换所购买的 Red Hat 计划。 请检查你的使用情况，确保购买的是正确的计划。 确定要购买的帮助，请参阅的了解如何应用 Red Hat Linux Enterprise 软件预订折扣。

## <a name="next-steps"></a>后续步骤

若要了解如何管理预留，请参阅[管理 Azure 预留](../../billing/billing-manage-reserved-vm-instance.md)。

若要了解更多信息，请参阅下列文章：

- [什么是 Azure 预订？](../../billing/billing-save-compute-costs-reservations.md)
- [管理 Azure 中的预留](../../billing/billing-manage-reserved-vm-instance.md)
- 了解如何应用 Red Hat 预订折扣
- [了解即用即付订阅的预留使用情况](../../billing/billing-understand-reserved-instance-usage.md)
- [了解企业合约的预留使用情况](../../billing/billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果仍有疑问，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。