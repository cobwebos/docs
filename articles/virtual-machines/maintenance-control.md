---
title: 使用 Azure 门户对 Azure 虚拟机进行维护控制概述
description: 了解如何使用维护控制来控制何时向 Azure VM 应用维护。
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: 950f4cfda73b40db0de8ba035868573cda1a5017
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84675785"
---
# <a name="managing-platform-updates-with-maintenance-control"></a>使用维护控制管理平台更新 

使用维护控制管理不需要重启的平台更新。 Azure 会频繁更新其基础结构，以提高可靠性、性能、安全性或推出新功能。 大多数更新对用户是透明的。 一些敏感的工作负载，如游戏、媒体流和金融交易，甚至不能容忍 VM 冻结或断开连接几秒钟来进行维护。 维护控制允许你等待平台更新并在 35 天滚动时段内应用它们。 

维护控制允许你决定何时向独立 VM 和 Azure 专用主机应用更新。

使用维护控制，你可以：
- 将许多更新作为一个更新包进行批处理。
- 等待长达 35 天才应用更新。 
- 使用 Azure Functions 在维护时段内自动执行平台更新。
- 维护配置跨订阅和资源组生效。 

## <a name="limitations"></a>限制

- VM 必须位于[专用主机](./linux/dedicated-hosts.md)上，或者必须使用[独立 VM 大小](./linux/isolation.md)进行创建。
- 35 天后，更新将自动应用。
- 用户必须具有“资源参与者”  访问权限。

## <a name="management-options"></a>管理选项

可以使用以下任一选项来创建和管理维护配置：

- [Azure CLI](maintenance-control-cli.md)
- [Azure PowerShell](maintenance-control-powershell.md)
- [Azure 门户](maintenance-control-portal.md)

## <a name="next-steps"></a>后续步骤

若要了解详细信息，请参阅[维护和更新](maintenance-and-updates.md)。
