---
title: 了解 Azure 预留实例折扣 - Azure 计费 | Microsoft Docs
description: 了解如何将 Azure 虚拟机预留实例折扣应用于正在运行的虚拟机。
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: yashar
ms.openlocfilehash: a0800bafc3d6b858387e28a3b75bc7b3a6bfe6e8
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301407"
---
# <a name="understand-how-the-reserved-instance-discount-is-applied"></a>了解如何应用预留实例折扣
购买 Azure 虚拟机预留实例后，Azure 预留实例折扣将自动应用于与预留实例的属性和数量匹配的虚拟机。 预留实例涵盖虚拟机的基础结构成本。 下表说明了虚拟机在购买预留实例后的成本。 在所有情况下，将按标准费率收取存储和网络费用。

| 虚拟机类型  | 通过预留实例收费 |    
|-----------------------|--------------------------------------------|
|未安装其他软件的 Linux VM | 预留实例涵盖 VM 基础结构成本。|
|具有软件费用的 Linux VM（例如，Red Hat） | 预留实例涵盖基础结构成本。 将收取其他软件费用。|
|未安装其他软件的 Windows VM |预留实例涵盖基础结构成本。 将收取 Windows 软件费用。|
|安装了其他软件（例如，SQL server）的 Windows VM | 预留实例涵盖基础结构成本。 将收取 Windows 软件和其他软件费用。|
|具有 [Azure 混合权益](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)的 Windows VM | 预留实例涵盖基础结构成本。 Azure 混合权益涵盖 Windows 软件成本。 其他任何软件都是单独收费的。| 

## <a name="application-of-reserved-instance-discount-to-non-windows-vms"></a>将预留实例折扣应用于非 Windows VM
 预留实例折扣按小时应用于正在运行的 VM 实例。 已购买的预留实例将与正在运行的 VM 所发送的使用情况信息进行匹配以应用预留实例折扣。 对于可能无法运行整个小时的 VM，将从其他未使用预留实例的 VM（包括同时运行的 VM）填充预留实例。 在一个小时结束时，将锁定该小时内的 VM 预留实例应用。 如果 VM 未运行一小时或该小时内同时运行的 VM 未填充该小时的预留实例，则该小时的预留实例未充分利用。 下图说明了如何将预留实例应用于应计费的 VM 使用量。 该说明基于一次预留实例购买和两个匹配的 VM 实例。

![一个已应用的预留实例和两个匹配的 VM 实例的屏幕截图](media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1.  预留实例线上方的任何使用量将按常规即用即付费率进行收费。 对于预留实例线下方的任何使用量不收取任何费用，因为这些使用已在购买预留实例时付费。
2.  在第 1 个小时内，实例 1 运行了 0.75 小时，实例 2 运行了 0.5 小时。 第 1 个小时的总体使用情况为 1.25 小时。 将按即用即付费率收取剩余 0.25 小时的费用。
3.  在第 2 个小时和第 3 个小时内，这两个实例都各运行了 1 小时。 一个实例的费用由预留实例费用涵盖，按即用即付费率对另一个实例收费。
4.  在第 4 个小时内，实例 1 运行了 0.5 小时，实例 2 运行了 1 小时。 预留实例费用完全涵盖了实例 1 的费用，并涵盖了实例 2 的 0.5 小时费用。 将按即用即付费率收取剩余 0.5 小时的费用。

若要了解预留实例应用情况并在计费使用情况报告中查看该信息，请参阅[了解预留实例使用情况](https://go.microsoft.com/fwlink/?linkid=862757)。

## <a name="application-of-reserved-instance-discount-to-windows-vms"></a>将预留实例折扣应用于 Windows VM
正在运行 Windows VM 实例时，将应用预留实例以涵盖基础结构成本。 Windows VM 的将预留实例应用于 VM 基础结构的成本与非 Windows VM 的相同。 将按 vCPU 对 Windows 软件单独收费。 请参阅[预留实例的 Windows 软件成本](https://go.microsoft.com/fwlink/?linkid=862756)。 可以利用 [Windows Server 的 Azure 混合权益] (https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)) 来涵盖 Windows 许可费用。

## <a name="next-steps"></a>后续步骤
若要了解有关预留实例的详细信息，请参阅以下文章：

- [通过 Azure 预留实例节省虚拟机资金](billing-save-compute-costs-reservations.md)
- [通过预留实例为虚拟机预付资金](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [管理预留实例](billing-manage-reserved-vm-instance.md)
- [了解如何应用预留实例折扣](billing-understand-vm-reservation-charges.md)
- [了解即用即付订阅的预订实例使用情况](billing-understand-reserved-instance-usage.md)
- [了解企业许可登记表的预订实例使用情况](billing-understand-reserved-instance-usage-ea.md)
- [了解 CSP 订阅的保留实例使用量](https://docs.microsoft.com/partner-center/azure-reservations)
- [预订实例未包含的 Windows 软件成本](billing-reserved-instance-windows-software-costs.md)


## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果仍有疑问，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。
