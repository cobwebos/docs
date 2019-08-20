---
title: 将 Azure 网络资源移动到新订阅或资源组 |Microsoft Docs
description: 使用 Azure 资源管理器将虚拟网络和其他网络资源移动到新的资源组或订阅中。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: c41ab1c3309243fa3a96d907f620ffeff10376a1
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69626257"
---
# <a name="move-guidance-for-networking-resources"></a>移动网络资源指南

本文介绍如何在特定情况下移动虚拟网络和其他网络资源。

## <a name="dependent-resources"></a>从属资源

移动虚拟网络时，还必须移动其从属资源。 对于 VPN 网关，必须移动 IP 地址、虚拟网络网关和所有关联的连接资源。 本地网络网关可以位于不同的资源组中。

若要移动带网络接口卡的虚拟机，必须移动所有依赖的资源。 移动与该网络接口卡对应的虚拟网络、该虚拟网络的所有其他网络接口卡，以及 VPN 网关。

## <a name="state-of-dependent-resources"></a>从属资源的状态

如果源或目标资源组包含虚拟网络, 则在移动过程中将检查虚拟网络的所有从属资源的状态。 如果其中的任何资源处于失败状态, 则会阻止移动。 例如, 如果使用虚拟网络的虚拟机出现故障, 则会阻止移动。 即使虚拟机不是要移动的资源之一, 而且不在移动的资源组中, 也会阻止移动。 若要避免此问题, 请将资源移到没有虚拟网络的资源组。

## <a name="peered-virtual-network"></a>对等的虚拟网络

若要移动对等的虚拟网络，必须首先禁用虚拟网络对等互连。 在禁用后，可以移动虚拟网络。 在移动后，重新启用虚拟网络对等互连。

## <a name="subnet-links"></a>子网链接

如果虚拟网络的任何子网包含资源导航链接，则无法将虚拟网络移动到其他订阅。 例如，如果 Azure Redis 缓存资源部署到某个子网，则该子网具有资源导航链接。

## <a name="next-steps"></a>后续步骤

有关移动资源的命令，请参阅[将资源移动至新资源组或订阅](../resource-group-move-resources.md)。
