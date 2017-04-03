---
title: "Azure 网络观察程序中的“下一跃点”简介 | Microsoft 文档"
description: "此页概述了网络观察程序的“下一跃点”功能"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 864185e62fb6c3cef4116824b36ee7e5d3447662
ms.lasthandoff: 03/04/2017

---

# <a name="introduction-to-next-hop-in-azure-network-watcher"></a>Azure 网络观察程序中的“下一跃点”简介

来自 VM 的流量将基于与 NIC 关联的有效路由发送到目标。 “下一跃点”功能可获取来自特定虚拟机和 NIC 的数据包的下一跃点类型和 IP 地址。 这可帮助确定是将数据包定向到目标，还是将流量定向到黑洞。 用户进行的不正确的路由配置（将流量定向到本地位置或虚拟设备）可能会导致连接问题。 “下一跃点”功能也返回与下一跃点关联的路由表。 查询下一跃点时，如果路由定义为用户定义的路由，则将返回该路由。 否则，“下一跃点”功能返回“系统路由”。

![下一跃点概述][1]

下面是查询下一跃点时可以返回的下一跃点类型的列表。

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* 无

### <a name="next-steps"></a>后续步骤

通过访问[检查 VM 上的下一跃点](network-watcher-check-next-hop-portal.md)，了解如何使用下一跃点发现网络连接问题

<!--Image references-->
[1]: ./media/network-watcher-next-hop-overview/figure1.png














