---
title: 虚拟网络业务连续性 | Microsoft Docs
description: 了解在发生影响 Azure 虚拟网络的 Azure 服务中断事件时该怎么办。
services: virtual-network
documentationcenter: ''
author: NarayanAnnamalai
manager: jefco
editor: ''
ms.assetid: ad260ab9-d873-43b3-8896-f9a1db9858a5
ms.service: virtual-network
ms.workload: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: narayan
ms.reviewer: aglick
ms.openlocfilehash: 3f91d24bff0bec540ff0e7964f21c2f47c03638c
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876171"
---
# <a name="virtual-network--business-continuity"></a>虚拟网络 - 业务连续性

## <a name="overview"></a>概述
虚拟网络 (VNet) 是网络在云中的逻辑表示形式。 这样一来，可以定义自己的专用 IP 地址空间，并将网络划分为子网。 VNet 用作托管计算资源（如 Azure 虚拟机和云服务（Web/辅助角色））的信任边界。 VNet 允许在其中托管的资源之间建立直接的专用 IP 通信。 可通过 VPN 网关或 ExpressRoute 将虚拟网络链接至本地网络。

VNet 是在区域范围内创建。 可以在两个不同的区域 (例如美国东部和美国西部)*创建*具有相同地址空间的 vnet, 但由于它们具有相同的地址空间, 因此无法将它们连接在一起。 

## <a name="business-continuity"></a>业务连续性

可通过多种不同的方式中断应用程序。 某个区域可能会因自然灾害而完全中断，或因多个设备/服务的故障而局部中断。 每种情况对 VNet 服务的影响是不同的。

**问：如果整个区域发生中断, 我该怎么办？例如，如果某区域因自然灾害而完全中断，我该怎么办？区域中托管的虚拟网络会怎样？**

答：在服务中断期间, 受影响区域的虚拟网络和资源将无法访问。

![简单虚拟网络示意图](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**问：可以在不同的区域中重新创建相同的虚拟网络？**

答：虚拟网络是相当轻量级的资源。 可以调用 Azure API 在不同的区域中创建具有相同地址空间的 VNet。 若要重新创建曾存在于受影响区域中的相同环境，请调用 API 来重新部署之前的云服务 Web 和辅助角色，以及虚拟机。 如果有本地连接（如在混合部署中），还必须部署新的 VPN 网关并连接本地网络。

若要创建虚拟网络，请参阅[创建虚拟网络](manage-virtual-network.md#create-a-virtual-network)。

**问：是否可以提前在另一个区域中重新创建给定区域中的 VNet 副本？**

答：是的, 你可以提前创建两个 Vnet, 在两个不同的区域中使用相同的专用 IP 地址空间和资源。 如果将在 VNet 中托管面向 Internet 的服务，必须设置流量管理器，将流量异地路由到可用区域。 不过，不能将两个地址空间相同的 VNet 连接到本地网络，因为这样做可能会导致路由问题发生。 当一个区域中的 VNet 发生灾难和丢失时，可以将可用区域中具有匹配地址空间的其他 VNet 连接到本地网络。

若要创建虚拟网络，请参阅[创建虚拟网络](manage-virtual-network.md#create-a-virtual-network)。

