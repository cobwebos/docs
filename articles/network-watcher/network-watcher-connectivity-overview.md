---
title: "Azure 网络观察程序中的连接检查简介 | Microsoft Docs"
description: "本页概述网络观察程序的连接功能"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: jdial
ms.openlocfilehash: 16ceef9c923b6a933a5caf752991b466346e0ebc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-connectivity-check-in-azure-network-watcher"></a>Azure 网络观察程序中的连接检查简介

网络观察程序的连接功能可以检查虚拟机 (VM) 之间的直接 TCP 连接、完全限定的域名 (FQDN)、URI 或 IPv4 地址。 网络方案比较复杂，它们是使用网络安全组、防火墙、用户定义的路由和 Azure 提供的资源实现的。 复杂的配置给连接问题的排查带来了困难。 网络观察程序有助于减少查找和检测连接问题的时间。 通过返回的结果，可以分析连接问题是平台问题还是用户配置问题造成的。 可以使用 [PowerShell](network-watcher-connectivity-powershell.md)、[Azure CLI](network-watcher-connectivity-cli.md) 和 [REST API](network-watcher-connectivity-rest.md) 检查连接。

> [!IMPORTANT]
> 连接检查需要虚拟机扩展 `AzureNetworkWatcherExtension`。 有关在 Windows VM 上安装扩展的信息，请访问[适用于 Windows 的 Azure 网络观察程序代理虚拟机扩展](../virtual-machines/windows/extensions-nwa.md)；有关 Linux VM 的信息，请访问[适用于 Linux 的 Azure 网络观察程序代理虚拟机扩展](../virtual-machines/linux/extensions-nwa.md)。

## <a name="response"></a>响应

下表显示了运行完连接检查后返回的属性。

|属性  |说明  |
|---------|---------|
|ConnectionStatus     | 连接检查的状态。 可能的结果为 **Reachable** 和 **Unreachable**。        |
|AvgLatencyInMs     | 连接检查期间的平均延迟，以毫秒为单位。 （仅当检查状态为 reachable 时才显示）        |
|MinLatencyInMs     | 连接检查期间的最小延迟，以毫秒为单位。 （仅当检查状态为 reachable 时才显示）        |
|MaxLatencyInMs     | 连接检查期间的最大延迟，以毫秒为单位。 （仅当检查状态为 reachable 时才显示）        |
|ProbesSent     | 检查期间发送的探测数。 最大值为 100。        |
|ProbesFailed     | 检查期间失败的探测数。 最大值为 100。        |
|Hops     | 从源到目标的跃点路径。        |
|Hops[].Type     | 资源的类型。 可能的值为 **Source**、**VirtualAppliance**、**VnetLocal** 和 **Internet**。        |
|Hops[].Id | 跃点的唯一标识符。|
|Hops[].Address | 跃点的 IP 地址。|
|Hops[].ResourceId | 如果跃点是 Azure 资源，则表示跃点的 ResourceID。 如果跃点是 Internet 资源，则 ResourceID 为 **Internet**。 |
|Hops[].NextHopIds | 创建的下一跃点的唯一标识符。|
|Hops[].Issues | 在该跃点上检查期间遇到的问题集合。 如果未出现任何问题，则该值为空。|
|Hops[].Issues[].Origin | 当前跃点上出现问题的位置。 可能的值包括：<br/> **Inbound** - 问题出现在从前一跃点到当前跃点的链接上<br/>**Outbound** - 问题出现在从当前跃点到下一跃点的链接上<br/>**Local** - 问题出现在当前跃点上。|
|Hops[].Issues[].Severity | 检测到的问题的严重性。 可能的值为 **Error** 和 **Warning**。 |
|Hops[].Issues[].Type |所发现问题的类型。 可能的值包括： <br/>**CPU**<br/>**内存**<br/>**GuestFirewall**<br/>**DnsResolution**<br/>**NetworkSecurityRule**<br/>**UserDefinedRoute** |
|Hops[].Issues[].Context |有关发现的问题的详细信息。|
|Hops[].Issues[].Context[].key |返回的键值对中的键。|
|Hops[].Issues[].Context[].value |返回的键值对中的值。|

下面是在跃点上发现的问题示例。

```json
"Issues": [
    {
        "Origin": "Outbound",
        "Severity": "Error",
        "Type": "NetworkSecurityRule",
        "Context": [
            {
                "key": "RuleName",
                "value": "UserRule_Port80"
            }
        ]
    }
]
```
## <a name="fault-types"></a>错误类型

连接检查返回有关连接的错误类型。 下表提供了返回的当前错误类型的列表。

|类型  |说明  |
|---------|---------|
|CPU     | CPU 利用率较高。       |
|内存     | 内存利用率较高。       |
|GuestFirewall     | 虚拟机防火墙配置导致流量被阻止。        |
|DNSResolution     | 目标地址的 DNS 解析失败。        |
|NetworkSecurityRule    | 流量被 NSG 规则阻止（已返回规则）        |
|UserDefinedRoute|用户定义的路由或系统路由导致流量被丢弃。 |

### <a name="next-steps"></a>后续步骤

访问[使用 Azure 网络观察程序检查连接](network-watcher-connectivity-powershell.md)，了解如何验证资源的连接。

<!--Image references-->
[1]: ./media/network-watcher-next-hop-overview/figure1.png

