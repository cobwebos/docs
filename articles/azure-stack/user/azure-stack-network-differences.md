---
title: "Azure 堆栈网络： 差异和注意事项"
description: "使用 Azure 堆栈中的网络时，了解有关的差异和注意事项。"
services: azure-stack
keywords: 
author: mattbriggs
manager: femila
ms.author: mabrigg
ms.date: 02/28/2018
ms.topic: article
ms.service: azure-stack
ms.openlocfilehash: 4c881a5f5e64ddc9fc67060208f3bef6ae0f5028
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2018
---
# <a name="considerations-for-azure-stack-networking"></a>有关 Azure 堆栈网络注意事项

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

Azure 堆栈中的网络提供的许多功能在 Azure 中，查找你应了解在开始部署之前存在一些差异。


本文概述了网络和 Azure 堆栈中的其功能的唯一注意事项。 若要了解有关高级 Azure 堆栈和 Azure 之间的差异信息，请参阅[密钥注意事项](azure-stack-considerations.md)主题。


## <a name="cheat-sheet-networking-differences"></a>速查表： 网络差异

|服务 | 特性 | Azure （全局） | Azure Stack |
| --- | --- | --- | --- |
| DNS | 多租户 DNS | 支持| 尚不支持|
| |DNS AAAA 记录|支持|不受支持|
| |每个订阅的 DNS 区域|100 （默认值）<br>可以请求增加。|100|
| |每个区域设置的 DNS 记录|5000 （默认值）<br>可以请求增加。|5000|
||区域委派的名称服务器|Azure 提供四个名称服务器创建的每个用户 （租户） 区域。|Azure 堆栈提供两个名称服务器创建的每个用户 （租户） 区域。|
| 虚拟网络|虚拟网络对等互连|通过 Azure 主干网络连接的同一区域中的两个虚拟网络。|尚不支持|
| |IPv6 地址|你可作为的一部分分配的 IPv6 地址[网络接口配置](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions)。|支持仅 IPv4。|
|VPN 网关|点到站点 VPN 网关|支持|尚不支持|
| |Vnet 到 Vnet 网关|支持|尚不支持|
| |VPN 网关 Sku|支持 Basic、 GW1、 GW2、 GW3、 标准的高性能、 非常高的性能。 |支持基本、 标准和高性能的 Sku。|
|负载均衡器|IPv6 公共 IP 地址|将一个 IPv6 公共 IP 地址分配给负载平衡器的支持。|支持仅 IPv4。|
|网络观察程序|网络观察程序租户网络监视功能|支持|尚不支持|
|CDN|内容交付网络配置文件|支持|尚不支持|
|应用程序网关|第 7 层负载平衡|支持|尚不支持|
|通信管理器|将为最佳应用程序性能和可靠性的传入流量的路由。|支持|尚不支持|
|快速路由|设置以快速的专用连接到 Microsoft 云服务从你的本地基础结构或共同租用设施。|支持|用于连接到 Express Route 线路的 Azure 堆栈的支持。|

## <a name="next-steps"></a>接下来的步骤

[Azure Stack 中的 DNS](azure-stack-dns.md)
