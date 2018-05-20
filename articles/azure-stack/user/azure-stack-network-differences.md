---
title: Azure Stack 网络：差异和注意事项
description: 了解 Azure Stack 中网络的差异和用法注意事项。
services: azure-stack
keywords: ''
author: mattbriggs
manager: femila
ms.author: mabrigg
ms.date: 05/14/2018
ms.topic: article
ms.service: azure-stack
ms.openlocfilehash: 2a4c5bce072970f158a89763ebdf4132eafe9cbe
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
---
# <a name="considerations-for-azure-stack-networking"></a>Azure Stack 网络注意事项

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure 堆栈网络具有许多提供 Azure 网络的功能。 但是，有一些在部署 Azure 堆栈网络之前应该了解的重要区别。

本文概述了 Azure 堆栈网络和其功能的唯一注意事项。 有关 Azure Stack 与 Azure 之间大致差异的详细信息，请参阅[重要注意事项](azure-stack-considerations.md)主题。

## <a name="cheat-sheet-networking-differences"></a>速查表：网络差异

|服务 | 功能 | Azure（公有云） | Azure Stack |
| --- | --- | --- | --- |
| DNS | 多租户 DNS | 支持| 尚不支持|
| |DNS AAAA 记录|支持|不支持|
| |每个订阅的 DNS 区域数|100（默认值）<br>可以请求增加。|100|
| |每个区域的 DNS 记录集数|5000（默认值）<br>可以请求增加。|5000|
||用于区域委派的名称服务器|Azure 中为创建每个用户 （租户） 区域提供四个名称服务器。|Azure Stack 为创建的每个用户（租户）区域提供两个名称服务器。|
| 虚拟网络|虚拟网络对等互连|通过 Azure 主干网络连接同一区域中的两个虚拟网络。|尚不支持|
| |IPv6 地址|可以分配 IPv6 地址作为[网络接口配置](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions)的一部分。|仅支持 IPv4。|
|VPN 网关|点到站点 VPN 网关|支持|尚不支持|
| |VNet 到 VNet 网关|支持|尚不支持|
| |VPN 网关 SKU|支持基本、GW1、GW2、GW3、标准高性能、超高性能 SKU。 |支持基本、标准和高性能 SKU。|
|负载均衡|IPv6 公共 IP 地址|支持将 IPv6 公共 IP 地址分配给负载均衡器。|仅支持 IPv4。|
|网络观察程序|网络观察程序租户网络监视功能|支持|尚不支持|
|CDN|内容分发网络配置文件|支持|尚不支持|
|应用程序网关|7 层负载均衡|支持|尚不支持|
|流量管理器|路由传入的流量，以获得最佳应用程序性能和可靠性。|支持|尚不支持|
|Express Route|设置以快速的专用连接到 Microsoft 云服务从你的本地基础结构或共同租用设施。|支持|支持将 Azure Stack 连接到 Express Route 线路。|

## <a name="next-steps"></a>后续步骤

[Azure Stack 中的 DNS](azure-stack-dns.md)
