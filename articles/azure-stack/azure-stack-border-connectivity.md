---
title: "边框的集成的 Azure 堆栈系统的连接网络集成注意事项 |Microsoft 文档"
description: "了解可以执行哪些操作来规划数据中心的多节点 Azure 堆栈的边框网络连接。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: 93dd609df90adac2c84ba8c62cf0d18f55a317bb
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="border-connectivity"></a>边框连接 
网络集成规划是为成功集成的 Azure 堆栈系统部署、 操作和管理的重要前提条件。 边框连接规划开始通过选择使用边界网关协议 (BGP) 的动态路由。 这要求分配 16 位 BGP 自治系统编号 （公共或私有），或使用静态路由，其中一个静态默认路由分配给边界设备。

> [!IMPORTANT]
> 顶部架 (TOR) 交换机需要包含点对点 Ip 的第 3 层上行 (/ 30 网络) 上的物理接口配置。 它不支持将第 2 层上行用于支持 Azure 堆栈操作的 TOR 交换机。 

## <a name="bgp-routing"></a>BGP 路由
使用 BGP 等动态路由协议可以保证你的系统时始终会注意到网络更改和便于管理。 

下图中所示，，播发的专用 IP 被限制使用前缀列表 TOR 交换机上的空间。 前缀列表拒绝的专用 IP 子网并将其应用为 TOR 和边框之间的连接上一个路由映射。

软件负载平衡器 (SLB) 运行的 Azure 堆栈解决方案内同级到 TOR 设备，以便它可以动态公布的 VIP 地址。

若要确保用户流量立即，并以透明方式从恢复失败，VPC 或 MLAG TOR 设备之间配置允许使用多底盘链接到的主机和 HSRP 或提供的 VRRP 聚合网络 IP 网络的冗余。

![BGP 路由](media/azure-stack-border-connectivity/bgp-routing.png)

## <a name="static-routing"></a>静态路由
使用静态路由将更固定的配置添加到的边框和 TOR 设备。 它需要的任何更改之前进行全面分析。 配置错误导致的问题可能需要更多时间，具体取决于所做的更改的回滚。 它不是建议的路由方法，但它支持。

将 Azure 堆栈集成到你的网络环境使用此方法，该边框设备必须配置指向要发送到外部网络流量的 TOR 设备的静态路由公共 Vip。

TOR 设备必须使用静态默认路由将所有流量都发送到边框设备配置。 此规则的一个流量例外是为私有将无法使用访问控制列表上 TOR 边框连接到应用的空间。

其他所有内容应与第一种方法相同。 BGP 动态路由将仍能在机架内因为它是基本工具，用于 SLB 和其他组件，无法禁用或删除。

![静态路由](media/azure-stack-border-connectivity/static-routing.png)

## <a name="transparent-proxy"></a>透明代理
如果你的数据中心要求使用代理的所有流量，则必须配置*透明代理*处理从机架来处理根据策略，将你的网络上的区域之间的通信的所有流量。

> [!IMPORTANT]
> Azure 堆栈解决方案不支持普通的 web 代理。  

透明代理 （也称为截获、 内联或强制的代理） 截获在网络层的普通的通信，而无需任何特殊的客户端配置。 客户端不需要注意的代理服务器存在。

![透明代理](media/azure-stack-border-connectivity/transparent-proxy.png)

## <a name="next-steps"></a>后续步骤
[DNS 集成](azure-stack-integrate-dns.md)