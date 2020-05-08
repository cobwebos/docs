---
title: Azure EA 虚拟机预留实例
description: 本文总结了针对 VM 预留实例的 Azure 预留如何帮助你节省企业注册的费用。
author: bandersmsft
ms.author: banders
ms.date: 05/01/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: 4321d2b48a3672b537cf0f50c5e02e3196e68ed8
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691169"
---
# <a name="azure-ea-vm-reserved-instances"></a>Azure EA 虚拟机预留实例

本文总结了针对 VM 预留实例的 Azure 预留如何帮助你节省企业注册的费用。 有关预留的详细信息，请参阅[什么是 Azure 保留？](../reservations/save-compute-costs-reservations.md)。

## <a name="reservation-exchanges-and-refunds"></a>预留交换和退款

可以用一个预留来交换同一类型的另一个预留。 如果不再需要某个预留，还可以请求预留退款（每年最多 50,000 美元）。 Azure 门户可用于预留的交换或退款。 有关详细信息，请参阅 [Azure 预留的自助交换和退款](../reservations/exchange-and-refund-azure-reservations.md)。

## <a name="reservation-costs-and-usage"></a>预留成本和使用情况

企业协议客户可以通过 Azure 门户和 REST REST API 查看成本和使用情况数据。 对于预留成本和使用情况，可以：

- 获取预留购买数据。
- 了解哪个订阅、资源组或资源使用了预留。
- 预留使用费的分摊。
- 计算预留节省额。
- 获取预留未充分利用的数据。
- 摊销预留成本。

有关预留成本和使用情况的详细信息，请参阅[获取企业协议预留成本和使用情况](../reservations/understand-reserved-instance-usage-ea.md)。

有关定价的信息，请参阅 [Linux 虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)或 [Windows 虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)。

## <a name="reserved-instances-api-support"></a>预留实例 API 支持

可以使用 Azure API 以编程方式为组织获取有关 Azure 服务或软件预留的信息。 有关详细信息，请参阅[适用于 Azure 预留自动化的 API](../reservations/reservation-apis.md)。

## <a name="azure-reserved-virtual-machine-instances"></a>Azure 虚拟机预留实例

预留实例最多可将虚拟机费用降低到所有虚拟机的即用即付价格的 72%；与 Azure 混合权益搭配使用时最多可节省 82%。 通过预付一年或三年的费用，可以为工作负载设置优先级并更好地做预算和执行预测。 还可以随业务需求的变化交换或取消预留。

### <a name="how-to-buy-reserved-virtual-machine-instances"></a>如何购买虚拟机预留实例

要购买 Azure 虚拟机预留实例，Enterprise Azure 合约管理员必须在 [Azure EA 门户](https://ea.azure.com/)“合约”选项卡的“合约详细信息”部分中启用“预留实例”购买选项    。

一旦启用了 EA 合约以添加预留实例，任何拥有与 EA 合约关联的有效订阅的帐户所有者都可以在 [Azure 门户](https://aka.ms/reservations)中购买虚拟机预留实例。 有关详细信息，请参阅 [Prepay for virtual machines and save money with Reserved Virtual Machine Instances](https://go.microsoft.com/fwlink/?linkid=861721)（通过虚拟机预留实例预付虚拟机款项以节省资金）。

### <a name="how-to-view-reserved-instance-purchase-details"></a>如何查看预留实例购买详细信息

可通过 [Azure 门户](https://aka.ms/reservations)左侧的“预留”菜单或从 [Azure EA 门户](https://ea.azure.com/)查看预留实例购买详细信息  。 从左侧菜单选择“报表”，然后在“使用情况摘要”选项卡上向下滚动到“按服务收费”部分    。滚动到该部分的底部，该部分末尾列出预留实例的购买和使用情况（在服务名称旁指定“1 年”或“3 年”），例如：Standard_DS1_v2 eastus 1 年或者 Standard_D2s_v3 eastus2 3 年。

### <a name="how-can-i-change-the-subscription-associated-with-reserved-instance-or-transfer-my-reserved-instance-benefits-to-a-subscription-under-the-same-account"></a>如何更改与我的预留实例关联的订阅或者将预留实例权益转让给同一帐户下的某个订阅？

可以通过以下方式更改接收预留实例权益的订阅：

- 登录到 [Azure 门户](https://aka.ms/reservations)。
- 通过关联同一帐户下的不同订阅来更新所应用的订阅范围。

有关更改预留范围的详细信息，请参阅[更改预留范围](../reservations/manage-reserved-vm-instance.md#change-the-reservation-scope)。

### <a name="how-to-view-reserved-instance-usage-details"></a>如何查看预留实例使用情况详细信息

你可以在 [Azure 门户](https://aka.ms/reservations)或 [Azure EA 门户](https://ea.azure.com/)中，通过“报表” > “使用情况摘要” > “按服务收费”查看预留实例使用情况详细信息（对于有权查看账单信息的 EA 客户而言）    。 可通过含“预留”一词的服务名称标识预留实例，例如：“预留-基础 VM”或“虚拟机预留-Windows Svr（1 核心）”。

使用情况详细信息和高级报表下载 CSV 包含额外的预留实例使用情况信息。 “附加信息”字段有助于标识预留实例使用情况  。

如果未使用 Azure 混合权益购买 Azure 虚拟机预留实例，预留实例将有两个计价器（硬件和软件）。 如果使用了 Azure 混合权益来购买预留实例，则在预留实例使用情况详细信息中不会出现软件计价器。

### <a name="reserved-instance-billing"></a>预留实例计费

企业客户可使用货币承诺来购买 Azure 虚拟机预留实例。 如果合约的货币承诺余额足以购买预留实例，系统将从货币承诺余额中扣除该额度且不会向你发送此次购买的发票。

即使 Azure EA 客户的货币承诺已全部用完，仍然可以购买预留实例，购买费用发票将开在下一个超额帐单上。 预留实例超额（如果有）是常规超额发票的一部分。

### <a name="reserved-instance-expiration"></a>预留实例到期

你将在预留前 30 天和到期时收到电子邮件通知。 预留到期时，所部署的 VM 会继续运行，并按即用即付费率计费。 有关详细信息，请参阅[虚拟机预留实例产品/服务](https://azure.microsoft.com/pricing/reserved-vm-instances/)。

## <a name="next-steps"></a>后续步骤
- 有关 Azure 预留的详细信息，请参阅[什么是 Azure 保留？](../reservations/save-compute-costs-reservations.md)。
- 若要详细了解企业预留成本和使用情况，请参阅[获取企业协议预留成本和使用情况](../reservations/understand-reserved-instance-usage-ea.md)。
- 有关定价的信息，请参阅 [Linux 虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)或 [Windows 虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)。
