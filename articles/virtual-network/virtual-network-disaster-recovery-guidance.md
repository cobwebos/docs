---
title: "发生影响 Azure 虚拟网络的 Azure 服务中断事件时该怎么办 | Microsoft 文档"
description: "了解在发生影响 Azure 虚拟网络的 Azure 服务中断事件时该怎么办。"
services: virtual-network
documentationcenter: 
author: NarayanAnnamalai
manager: jefco
editor: 
ms.assetid: ad260ab9-d873-43b3-8896-f9a1db9858a5
ms.service: virtual-network
ms.workload: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: narayan;aglick
ms.openlocfilehash: 4e125406d2e798138c45e3fbbf61a610afab69fc
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="virtual-network--business-continuity"></a>虚拟网络 - 业务连续性
## <a name="overview"></a>概述
虚拟网络 (VNet) 是网络在云中的逻辑表示形式。 这样一来，可以定义自己的专用 IP 地址空间，并将网络划分为子网。 VNet 用作托管计算资源（如 Azure 虚拟机和云服务（Web/辅助角色））的信任边界。 VNet 允许在其中托管的资源之间建立直接的专用 IP 通信。 虚拟网络也可通过混合选项（例如 VPN 网关或 ExpressRoute）链接到本地网络。

VNet 是在区域范围内创建。 可以在两个不同的区域创建具有相同地址空间的 VNet（即美国东部和美国西部，但相互之间无法直接连接）。 

## <a name="business-continuity"></a>业务连续性
可通过多种不同的方式中断应用程序。 给定区域可能会因自然灾害而完全中断，或因多个设备/服务故障而局部中断。 每种情况对 VNet 服务的影响是不同的。

**问：如果发生整个区域中断（即，如果某区域因自然灾害而完全中断），该怎么办？区域中托管的虚拟网络会怎样？**

答：在服务中断期间，受影响区域中的虚拟网络和资源始终保持无法访问的状态。

![简单虚拟网络示意图](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**问：我怎样才能在不同的区域中重新创建相同的虚拟网络？**

答：虚拟网络 (VNet) 是相当轻量级的资源。 可以调用 Azure API 在不同的区域中创建具有相同地址空间的 VNet。 若要重新创建曾存在于受影响区域中的相同环境，必须调用 API 来重新部署之前的云服务（Web/辅助角色）和虚拟机。 如果有本地连接（如在混合部署中），还必须运转 VPN 网关并连接本地网络。

有关创建 VNet 的说明，请单击[此处](virtual-networks-create-vnet-arm-pportal.md)。 

**问：能否在其他区域提前重新创建给定区域的 VNet 副本？**

答：能。可以在不同区域中提前创建两个使用相同的专用 IP 地址空间和资源的 VNet。 如果客户在 VNet 中托管面向 Internet 的服务，必须设置流量管理器，将流量异地路由到可用区域。 不过，客户不能将两个地址空间相同的 VNet 连接到本地网络，因为这样做可能会导致路由问题发生。 当一个区域中的 VNet 发生灾难和丢失时，客户可以将可用区域中具有匹配地址空间的其他 VNet 连接到本地网络。

有关创建 VNet 的说明，请单击[此处](virtual-networks-create-vnet-arm-pportal.md)。

