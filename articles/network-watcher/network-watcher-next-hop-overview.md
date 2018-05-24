---
title: Azure 网络观察程序中的“下一跃点”简介 | Microsoft 文档
description: 本文概述了网络观察程序的“下一个跃点”功能。
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: bbb782e700781dcfedbbd340c7d10db53767b035
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32180379"
---
# <a name="use-next-hop-to-diagnose-virtual-machine-routing-problems"></a>使用“下一个跃点”功能来诊断虚拟机路由问题

来自虚拟机 (VM) 的流量将基于与网络接口 (NIC) 关联的有效路由发送到目标。 “下一个跃点”功能可获取来自特定 VM 和 NIC 的数据包的下一跃点类型和 IP 地址。 了解下一个跃点可帮助你确定流量是否定向到预定目标，或流量是否无处发送。 不正确的路由配置（将流量定向到本地位置或虚拟设备）可能会导致连接问题。 “下一跃点”功能也返回与下一跃点关联的路由表。 如果路由定义为用户定义的路由，则将返回该路由。 否则，“下一个跃点”功能返回“系统路由”。

![下一跃点概述](./media/network-watcher-next-hop-overview/figure1.png)

由“下一个跃点”功能返回的下一个跃点如下所示：

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* VnetPeering
* 无

要详细了解每种下一个跃点类型，请参阅[路由概述](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)。

## <a name="next-steps"></a>后续步骤

要了解如何使用“下一个跃点”功能来诊断 VM 络路由问题，请参阅[诊断 VM 网络路由问题](diagnose-vm-network-routing-problem.md)。
