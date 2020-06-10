---
title: include 文件
description: include 文件
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 05/14/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 4e00b0c46c79dc058d19076a396d055ba1d41b87
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673520"
---
| 资源 | 限制 |
| --- | --- |
| 数据吞吐量 |30 Gbps<sup>1</sup> |
|规则|10,000。 已组合所有规则类型。|
|最高 DNAT 规则数目|298<br>如果同时为 TCP 和 UDP 配置规则的协议，则会将其计为两个规则。|
|最小 AzureFirewallSubnet 大小 |/26|
|网络和应用程序规则的端口范围|1 - 65535|
|公共 IP 地址|最多 250 个。 所有公共 IP 地址都可以在 DNAT 规则中使用，它们都对可用的 SNAT 端口有影响。|
|IP 组中的 IP 地址|不多于 50 个 IP 组：每个防火墙实例最多有 5000 个单独的 IP 地址。<br>51 - 100 个 IP 组：每个防火墙实例各自有 500 个单独的 IP 地址。<br><br>有关详细信息，请参阅 [Azure 防火墙中的 IP 组（预览版）](../articles/firewall/ip-groups.md#ip-address-limits)
|路由表|默认情况下，AzureFirewallSubnet 具有一个 NextHopType 值设置为 Internet 的 0.0.0.0/0 路由。<br><br>Azure 防火墙必须具有直接的 Internet 连接。 如果 AzureFirewallSubnet 知道通过 BGP 的本地网络的默认路由，则必须将其替代为 0.0.0.0/0 UDR，将 NextHopType 值设置为 Internet 以保持 Internet 直接连接 。 默认情况下，Azure 防火墙不支持强制的安全加密链路连接到本地网络。<br><br>但是，如果你的配置要求强制的安全加密链路连接到本地网络，Microsoft 将基于具体的情况提供支持。 请联系支持人员，以便我们可以查看你的情况。 如果接受，我们将允许你的订阅，确保保持所需的防火墙 Internet 连接。|

<sup>1</sup>如果需要增加这些限制，请联系 Azure 支持部门。
