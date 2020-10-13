---
title: Azure 虚拟机规模集的 OS 映像升级的维护控制概述
description: 了解如何使用维护控制来控制何时将自动 OS 映像升级部署到 Azure 虚拟机规模集。
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/11/2020
ms.author: jushiman
ms.openlocfilehash: 55cbc5db534dd1b05f91a24e0104b1f2dc110547
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90532587"
---
# <a name="preview-maintenance-control-for-azure-virtual-machine-scale-sets"></a>预览版： Azure 虚拟机规模集的维护控制 

使用维护控制管理虚拟机规模集的 [自动 OS 映像升级](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) 。

维护控制允许你决定何时将更新应用到虚拟机规模集中的 OS 磁盘，只需要一个更简单、更可预测的体验。 

维护配置跨订阅和资源组生效。

整个工作流归结为以下步骤： 
- 创建维护配置。
- 将虚拟机规模集与维护配置关联。
- 启用自动 OS 升级。

> [!IMPORTANT]
> Azure 虚拟机规模集的 OS 映像升级的维护控制当前为公共预览版。
> 此预览版不附带服务级别协议，我们不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


## <a name="limitations"></a>限制

- Vm 必须位于规模集中。
- 用户必须具有“资源参与者”  访问权限。
- 维护配置中的维护持续时间必须是5小时或更长时间。
- 维护配置中的维护重复项必须设置为 "Day"


## <a name="management-options"></a>管理选项

可以使用以下任一选项来创建和管理维护配置：

- [Azure PowerShell](virtual-machine-scale-sets-maintenance-control-powershell.md)


## <a name="next-steps"></a>后续步骤

了解如何使用维护控制和 PowerShell 控制何时将维护应用于 Azure 虚拟机规模集。

> [!div class="nextstepaction"]
> [使用 PowerShell 进行虚拟机规模集维护控制](virtual-machine-scale-sets-maintenance-control-powershell.md)
