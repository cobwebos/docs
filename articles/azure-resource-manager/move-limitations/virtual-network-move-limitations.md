---
title: 将 Azure 虚拟网络资源移到新订阅或资源组 |Microsoft Docs
description: 使用 Azure 资源管理器将虚拟网络移到新的资源组或订阅。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 809d403a463048910a284e7f8fcdc18cf7c65b69
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723488"
---
# <a name="move-guidance-for-virtual-networks"></a>虚拟网络的迁移指南

本文介绍如何移动虚拟网络的特定方案。

## <a name="dependent-resources"></a>依赖资源

移动虚拟网络时，还必须移动其从属资源。 对于 VPN 网关，必须移动 IP 地址、虚拟网络网关和所有关联的连接资源。 本地网络网关可以位于不同的资源组中。

若要移动带网络接口卡的虚拟机，必须移动所有依赖的资源。 移动网络接口卡的虚拟网络、 虚拟网络和 VPN 网关中的所有其他网络接口卡。

## <a name="peered-virtual-network"></a>对等互连的虚拟网络

若要移动对等的虚拟网络，必须首先禁用虚拟网络对等互连。 在禁用后，可以移动虚拟网络。 在移动后，重新启用虚拟网络对等互连。

## <a name="subnet-links"></a>子网链接

如果虚拟网络的任何子网包含资源导航链接，则无法将虚拟网络移动到其他订阅。 例如，如果 Azure Redis 缓存资源部署到某个子网，则该子网具有资源导航链接。

## <a name="next-steps"></a>后续步骤

有关移动资源的命令，请参阅[将资源移动至新资源组或订阅](../resource-group-move-resources.md)。
