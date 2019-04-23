---
title: include 文件
description: include 文件
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 3/25/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 0467359cd9d6a067e519a62532f00459bc5f68cb
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59803426"
---
| 资源 | 默认限制 |
| --- | --- |
| 数据吞吐量 |30 Gbps<sup>1</sup> |
|规则|10000，所有规则类型组合。|
|最小 AzureFirewallSubnet 大小 |/26|
|网络和应用程序规则的端口范围|0-64,000。 目前正在努力放宽此限制。|
|路由表|默认情况下，AzureFirewallSubnet 0.0.0.0/0 路由的 NextHopType 值设置为**Internet**。<br><br>如果启用强制隧道连接到本地通过 ExpressRoute 或 VPN 网关，可能需要显式配置为 Internet 设置的 NextHopType 值 0.0.0.0/0 用户定义的路由 (UDR) 并将其与你 AzureFirewallSubnet 关联。 此设置将替代的潜在默认网关返回到你的本地网络的 BGP 播发。 如果你的组织要求 Azure 防火墙，以将你的本地网络上返回到默认网关通信的强制隧道，请联系支持。 我们可以维护你的订阅以确保所需的 Internet 连接防火墙的允许列表。|

<sup>1</sup>如果需要增加这些限制，请联系 Azure 支持部门。
