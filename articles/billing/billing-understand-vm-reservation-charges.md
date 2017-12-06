---
title: "了解 Azure 保留虚拟机实例折扣应用 | Microsoft Docs"
description: "了解如何将 Azure 保留 VM 实例折扣应用于正在运行的 VM。"
services: billing
documentationcenter: 
author: vikramdesai01
manager: vikdesai
editor: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2017
ms.author: vikdesai
ms.openlocfilehash: 3fd12bd3c51eeef57c896da030a83e447dc3e8ff
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2017
---
# <a name="understand-how-the-reserved-virtual-machine-instance-discount-is-applied"></a>了解如何应用保留虚拟机实例折扣
购买保留 VM 实例后，保留折扣将自动应用于与预订的属性和数量匹配的虚拟机。 预订涵盖虚拟机的基础结构成本。 下表说明了虚拟机在购买预订后的成本。 在所有情况下，将按标准费率收取存储和网络费用。

| 虚拟机类型  | 预订的费用 |    
|-----------------------|--------------------------------------------| 
|未安装其他软件的 Linux VM | 预订涵盖 VM 基础结构成本。|
|具有软件费用的 Linux VM（例如，Red Hat） | 预订涵盖基础结构成本。 将收取其他软件费用。|
|未安装其他软件的 Windows VM |预订涵盖基础结构成本。 将收取 Windows 软件费用。|
|安装了其他软件（例如，SQL server）的 Windows VM | 预订涵盖基础结构成本。 将收取 Windows 软件和其他软件费用。|
|具有 [Azure 混合权益](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/hybrid-use-benefit-licensing)的 Windows VM | 预订涵盖基础结构成本。 Azure 混合权益涵盖 Windows 软件成本。 其他任何软件都是单独收费的。| 

## <a name="application-of-reservation-discount-to-non-windows-vms"></a>将预订折扣应用于非 Windows VM
 预订折扣按小时应用于正在运行的 VM 实例。 已购买的预订将与正在运行的 VM 所发送的使用情况信息进行匹配以应用预订折扣。 下图说明了如何将预订应用于应计费的 VM 使用量。 该说明基于一次预订购买和两个匹配的 VM 实例。

![保留 VM 实例应用程序](media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1.  保留 VM 实例线上方的任何使用量将按常规即用即付费率进行收费。 对于保留 VM 实例线下方的任何使用量不收取任何费用，因为这些使用已在购买预订时付费。
2.  在第 1 个小时内，实例 1 运行了 0.75 小时，实例 2 运行了 0.5 小时。 第 1 个小时的总体使用情况为 1.25 小时。 将按即用即付费率收取剩余 0.25 小时的费用。
3.  在第 2 个小时和第 3 个小时内，这两个实例都各运行了 1 小时。 一个实例的费用由预订费用涵盖，按即用即付费率对另一个实例收费。
4.  在第 4 个小时内，实例 1 运行了 0.5 小时，实例 2 运行了 1 小时。 预订费用完全涵盖了实例 1 的费用，并涵盖了实例 2 的 0.5 小时费用。 将按即用即付费率收取剩余 0.5 小时的费用。

若要了解预订应用情况并在计费使用情况报告中查看该信息，请参阅[了解保留 VM 实例使用情况](https://go.microsoft.com/fwlink/?linkid=862757)。

## <a name="application-of-reservation-discount-to-windows-vms"></a>将预订折扣应用于 Windows VM
正在运行 Windows VM 实例时，将应用预订以涵盖基础结构成本。 对 Windows VM 的 VM 基础结构成本应用预订与对非 Windows VM 的 VM 基础结构成本应用预订相同。 将按 vCPU 对 Windows 软件单独收费。 请参阅 [Windows 软件的预订费用](https://go.microsoft.com/fwlink/?linkid=862756)。 可以使用 [Windows Server 的 Azure 混合权益] (https://docs.microsoft.com/zh-cn/azure/virtual-machines/windows/hybrid-use-benefit-licensing) 涵盖 Windows 授权成本。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果需要帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。
