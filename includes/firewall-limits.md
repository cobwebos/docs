---
title: include 文件
description: include 文件
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 04/07/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: fb95301d4faa958a677c42bc8092ac52a7c95c8b
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "80813712"
---
| 资源 | 限制 |
| --- | --- |
| 数据吞吐量 |30 Gbps<sup>1</sup> |
|规则|10,000. 所有规则类型合并。|
|最大 DNAT 规则|298<br>如果为 TCP 和 UDP 配置了规则的协议，则它计为两个规则。|
|最小 AzureFirewallSubnet 大小 |/26|
|网络和应用程序规则的端口范围|1 - 65535|
|公共 IP 地址|最大值为 100 个（目前，仅为前五个公共 IP 地址添加 SNAT 端口。|
|IP 组 IP 地址|50 个或更少 IP 组：每个防火墙实例最多 5000 个单独的 IP 地址。<br>51 - 100 个 IP 组：每个防火墙实例 500 个单独的 IP 地址。<br><br>有关详细信息，请参阅[Azure 防火墙中的 IP 组（预览）](../articles/firewall/ip-groups.md#ip-address-limits)
|路由表|默认情况下，AzureFirewallSubnet 使用其 NextHopType 值设置为“Internet”**** 的 0.0.0.0/0 路由。<br><br>Azure 防火墙必须具有直接的 Internet 连接。 如果 AzureFirewallSubnet 知道通过 BGP 的本地网络的默认路由，则必须将其替代为 0.0.0.0/0 UDR，将 NextHopType 值设置为 Internet 以保持 Internet 直接连接********。 默认情况下，Azure 防火墙不支持强制的安全加密链路连接到本地网络。<br><br>但是，如果你的配置要求强制的安全加密链路连接到本地网络，Microsoft 将基于具体的情况提供支持。 请联系支持人员，以便我们可以查看你的情况。 如果接受，我们将允许你的订阅，确保保持所需的防火墙 Internet 连接。|

<sup>1</sup>如果需要增加这些限制，请与 Azure 支持部门联系。
