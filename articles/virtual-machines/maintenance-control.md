---
title: 使用 Azure 门户对 Azure 虚拟机进行维护控制概述
description: 了解如何使用维护控制来控制何时将维护应用到 Azure Vm。
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: 4c5e30d0607db2d529ae41ebab6dc82e925ff2a8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82139194"
---
# <a name="managing-platform-updates-with-maintenance-control"></a>管理包含维护控制的平台更新 

使用维护控制管理平台更新，无需重新启动。 Azure 会频繁更新其基础结构，以提高可靠性、性能、安全性或启动新功能。 大多数更新对用户是透明的。 某些敏感工作负荷（例如游戏、媒体流式处理和财务交易）不能容忍几乎几秒钟的时间冻结或断开连接以进行维护。 维护控制提供了在35天滚动窗口中等待平台更新并将其应用的选项。 

维护控制允许您决定何时将更新应用于隔离的 Vm 和 Azure 专用主机。

借助维护控制，您可以：
- 批处理更新为一个更新包。
- 请等待35天，以应用更新。 
- 使用 Azure Functions 自动执行维护时段的平台更新。
- 维护配置跨订阅和资源组工作。 

## <a name="limitations"></a>限制

- Vm 必须位于[专用主机](./linux/dedicated-hosts.md)上，或使用[独立的 VM 大小](./linux/isolation.md)创建。
- 35天后，将自动应用更新。
- 用户必须具有**资源参与者**访问权限。

## <a name="management-options"></a>管理选项

您可以使用以下任一选项来创建和管理维护配置：

- [Azure CLI](maintenance-control-cli.md)
- [Azure PowerShell](maintenance-control-powershell.md)
- [Azure 门户](maintenance-control-portal.md)

## <a name="next-steps"></a>后续步骤

若要了解详细信息，请参阅[维护和更新](maintenance-and-updates.md)。
