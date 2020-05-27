---
title: include 文件
description: include 文件
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 05/06/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 5a5f831e72da6bedaf12d3ed82e79f0250a09062
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82876033"
---
| 资源 | 限制 |
| --- | --- |
| 数据吞吐量 |30 Gbps<sup>1</sup> |
|规则|10000。 所有规则类型都是组合的。|
|最 DNAT 规则|298<br>如果为 TCP 和 UDP 配置规则的协议，则会将其计为两个规则。|
|最小 AzureFirewallSubnet 大小 |/26|
|网络和应用程序规则的端口范围|1 - 65535|
|公共 IP 地址|DNAT 和 SNAT 端口最大为250。|
|IP 组 IP 地址|50个或更少 IP 组：每个防火墙实例的最大5000单个 IP 地址。<br>51-100 IP 组：500每个防火墙实例的各个 IP 地址。<br><br>有关详细信息，请参阅[Azure 防火墙中的 IP 组（预览版）](../articles/firewall/ip-groups.md#ip-address-limits)
|路由表|默认情况下，AzureFirewallSubnet 使用其 NextHopType 值设置为“Internet”**** 的 0.0.0.0/0 路由。<br><br>Azure 防火墙必须具有直接的 Internet 连接。 如果 AzureFirewallSubnet 知道通过 BGP 的本地网络的默认路由，则必须将其替代为 0.0.0.0/0 UDR，将 NextHopType 值设置为 Internet 以保持 Internet 直接连接********。 默认情况下，Azure 防火墙不支持强制的安全加密链路连接到本地网络。<br><br>但是，如果你的配置要求强制的安全加密链路连接到本地网络，Microsoft 将基于具体的情况提供支持。 请联系支持人员，以便我们可以查看你的情况。 如果接受，我们将允许你的订阅，确保保持所需的防火墙 Internet 连接。|

<sup>1</sup>如果需要增加这些限制，请与 Azure 支持部门联系。
