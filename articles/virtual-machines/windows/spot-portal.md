---
title: 使用门户部署 Azure 点 Vm
description: 了解如何使用 Azure PowerShell 部署专色 Vm 以节省成本。
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/20/2019
ms.author: cynthn
ms.openlocfilehash: 682ee86c373c715080ef1baf82b473242a2e43db
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74782042"
---
# <a name="preview-deploy-spot-vms-using-the-azure-portal"></a>预览：使用 Azure 门户部署专色 Vm

使用[污点 vm](spot-vms.md) ，你可以显著节省成本。 当 Azure 需要恢复容量时，Azure 基础结构将逐出点 Vm。 因此，专色 Vm 非常适合用于处理中断的工作负荷，如批处理作业、开发/测试环境、大型计算工作负荷等。

基于区域和 SKU，污点 Vm 的定价是可变的。 有关详细信息，请参阅适用于[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)和[Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的 VM 定价。 有关设置最大价格的详细信息，请参阅[污点 vm-定价](spot-vms.md#pricing)。

你可以选择为 VM 设置你愿意支付的最大价格（每小时）。 可使用最多5个小数位来设置专色 VM 的最大价格（美元）。 例如，值 `0.05701`的最大价格为 $0.05701 美元/小时。 如果将最大价格设置为 `-1`，则不会根据价格收回 VM。 VM 的价格将是当前的价格价格或标准 VM 的价格，只要容量和配额可用，此价格就越小。

> [!IMPORTANT]
> 污点实例当前为公共预览版。
> 不建议将此预览版本用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
> 对于公共预览版的早期部分，污点实例将具有固定价格，因此不会有任何基于价格的逐出。

## <a name="create-the-vm"></a>创建 VM

创建使用点 Vm 的 VM 的过程与[快速入门](quick-create-portal.md)中所述的过程相同。 部署 VM 时，可以选择使用 Azure 点实例。


在 "**基本**信息" 选项卡上的 "**实例详细信息**" 部分中，**不**是使用 Azure 点实例的默认值。

![屏幕截图选择 "否，不使用 Azure 点实例"](media/spot-portal/no.png)

选择 **"是"** 时，该部分将展开，你可以选择[逐出类型和逐出策略](spot-vms.md#eviction-policy)。 

![用于选择 "是" 的屏幕截图，使用 Azure 点实例](media/spot-portal/yes.png)


## <a name="next-steps"></a>后续步骤

你还可以使用[PowerShell](spot-powershell.md)创建专色 vm。