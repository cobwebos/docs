---
title: 使用门户部署 Azure Spot VM
description: 了解如何使用 Azure PowerShell 部署 Spot VM 以节省成本。
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 8845d0f9277384c1cc32b31b5ea5151cd0668c79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77158972"
---
# <a name="preview-deploy-spot-vms-using-the-azure-portal"></a>预览：使用 Azure 门户部署 Spot VM

使用[Spot VM](spot-vms.md)使您能够利用我们未使用的容量，从而显著节省成本。 在 Azure 需要返回容量的任何时间点，Azure 基础结构将驱逐 Spot VM。 因此，Spot VM 非常适合处理批处理作业、开发/测试环境、大型计算工作负载等中断的工作负载。

现货 VM 的定价基于区域和 SKU 是可变的。 有关详细信息，请参阅[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)和[Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的 VM 定价。 有关设置最高价格的详细信息，请参阅[现货 VM - 定价](spot-vms.md#pricing)。

您可以选择为 VM 设置您愿意每小时支付的最高价格。 Spot VM 的最高价格可以用美元 （USD） 设置，最多使用 5 个小数位。 例如，该值`0.05701`将是每小时 0.05701 美元的最高价格。 如果将最高价格设置为`-1`，则 VM 不会根据价格被逐出。 VM 的价格将是现货的当前价格或标准 VM 的价格，只要有容量和配额可用，标准 VM 的价格就更少了。

> [!IMPORTANT]
> 竞价实例当前处于公共预览版中。
> 不建议生产工作负载使用此预览版本。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>


## <a name="create-the-vm"></a>创建 VM

创建使用 Spot VM 的 VM 的过程与[快速入门](quick-create-portal.md)中详细说明的过程相同。 部署 VM 时，可以选择使用 Azure 点实例。


在 **"基本"** 选项卡上，在 **"实例详细信息**"部分中，"**否**"是使用 Azure 点实例的默认值。

![选择"否"的屏幕截图，不要使用 Azure 点实例](media/spot-portal/no.png)

它你选择**是**，部分展开，你可以选择你的[驱逐类型和驱逐策略](spot-vms.md#eviction-policy)。 

![选择"是"的屏幕截图，请使用 Azure 点实例](media/spot-portal/yes.png)


## <a name="next-steps"></a>后续步骤

您还可以使用[PowerShell](spot-powershell.md)创建 Spot VM。