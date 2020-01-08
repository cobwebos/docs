---
title: 将 Azure 网络资源移动到新订阅或资源组
description: 使用 Azure 资源管理器将虚拟网络和其他网络资源移动到新的资源组或订阅中。
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 0cd6887d3489f2ffede0f5e3d63533a33a6ccc04
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75485229"
---
# <a name="move-guidance-for-networking-resources"></a>移动网络资源指南

本文介绍如何在特定情况下移动虚拟网络和其他网络资源。

## <a name="dependent-resources"></a>依赖资源

移动虚拟网络时，还必须移动其从属资源。 对于 VPN 网关，必须移动 IP 地址、虚拟网络网关和所有关联的连接资源。 本地网络网关可以位于不同的资源组中。

若要将具有网络接口卡的虚拟机移动到新的订阅，必须移动所有相关资源。 移动网络接口卡的虚拟网络，虚拟网络的所有其他网络接口卡，以及 VPN 网关。

有关详细信息，请参阅[跨订阅移动方案](../move-resource-group-and-subscription.md#scenario-for-move-across-subscriptions)。

## <a name="peered-virtual-network"></a>对等互连虚拟网络

若要移动对等的虚拟网络，必须首先禁用虚拟网络对等互连。 在禁用后，可以移动虚拟网络。 在移动后，重新启用虚拟网络对等互连。

## <a name="subnet-links"></a>子网链接

如果虚拟网络的任何子网包含资源导航链接，则无法将虚拟网络移动到其他订阅。 例如，如果 Azure Redis 缓存资源部署到某个子网，则该子网具有资源导航链接。

## <a name="next-steps"></a>后续步骤

有关移动资源的命令，请参阅[将资源移动至新资源组或订阅](../move-resource-group-and-subscription.md)。
