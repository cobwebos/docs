---
title: 将 Azure 网络资源移动到新订阅或资源组
description: 使用 Azure 资源管理器将虚拟网络和其他网络资源移到新的资源组或订阅。
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: c4e6acb52f6342c57fb1db9fc3e83d90d6d01285
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150825"
---
# <a name="move-guidance-for-networking-resources"></a>网络资源移动指南

本文介绍如何针对特定方案移动虚拟网络和其他网络资源。

## <a name="dependent-resources"></a>依赖资源

移动虚拟网络时，还必须移动其从属资源。 对于 VPN 网关，必须移动 IP 地址、虚拟网络网关和所有关联的连接资源。 本地网络网关可以位于不同的资源组中。

若要将具有网络接口卡的虚拟机移动到新的订阅，必须移动所有相关资源。 移动与该网络接口卡对应的虚拟网络、该虚拟网络的所有其他网络接口卡，以及 VPN 网关。

有关详细信息，请参阅[跨订阅移动方案](../resource-group-move-resources.md#scenario-for-move-across-subscriptions)。

## <a name="peered-virtual-network"></a>对等的虚拟网络

若要移动对等的虚拟网络，必须首先禁用虚拟网络对等互连。 在禁用后，可以移动虚拟网络。 在移动后，重新启用虚拟网络对等互连。

## <a name="subnet-links"></a>子网链接

如果虚拟网络的任何子网包含资源导航链接，则无法将虚拟网络移动到其他订阅。 例如，如果 Azure Redis 缓存资源部署到某个子网，则该子网具有资源导航链接。

## <a name="next-steps"></a>后续步骤

有关移动资源的命令，请参阅[将资源移动至新资源组或订阅](../resource-group-move-resources.md)。
