---
title: 使用门户部署 Azure 点 Vm
description: 如何使用 Azure PowerShell 部署专色 Vm 以节省成本。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/17/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: ef5fa0aafca1312480f51614a1ba1692c09a13b8
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816569"
---
# <a name="deploy-spot-vms-using-the-azure-portal"></a>使用 Azure 门户部署专色 Vm

使用 [污点 vm](../spot-vms.md) ，你可以显著节省成本。 当 Azure 需要恢复容量时，Azure 基础结构将逐出点 Vm。 因此，专色 Vm 非常适合用于处理中断的工作负荷，如批处理作业、开发/测试环境、大型计算工作负荷等。

基于区域和 SKU，污点 Vm 的定价是可变的。 有关详细信息，请参阅适用于 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 和 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的 VM 定价。 有关设置最大价格的详细信息，请参阅 [污点 vm-定价](../spot-vms.md#pricing)。

你可以选择为 VM 设置你愿意支付的最大价格（每小时）。 可使用最多5个小数位 (USD) ，为专色 VM 设置最大价格。 例如，该值的 `0.05701` 最大价格为 $0.05701 美元/小时。 如果将最大价格设置为 `-1` ，则不会根据价格收回 VM。 VM 的价格将是当前的价格价格或标准 VM 的价格，只要容量和配额可用，此价格就越小。

收回 VM 后，你可以选择删除 VM 和基础磁盘，或解除分配 VM，以便以后可以重新启动 vm。


## <a name="create-the-vm"></a>创建 VM

创建使用点 Vm 的 VM 的过程与 [快速入门](quick-create-portal.md)中所述的过程相同。 部署 VM 时，可以选择使用 Azure 点实例。


在 " **基本** 信息" 选项卡上的 " **实例详细信息** " 部分中， **不** 是使用 Azure 点实例的默认值。

![屏幕截图选择 "否，不使用 Azure 点实例"](media/spot-portal/no.png)

选择 **"是"** 时，该部分将展开，你可以选择 [逐出类型和逐出策略](../spot-vms.md#eviction-policy)。 

![用于选择 "是" 的屏幕截图，使用 Azure 点实例](media/spot-portal/yes.png)


## <a name="next-steps"></a>后续步骤

你还可以使用 [PowerShell](spot-powershell.md)创建专色 vm。