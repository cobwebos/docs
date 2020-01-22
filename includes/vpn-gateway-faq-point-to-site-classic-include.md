---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3c1e34bb418f9be2e26afc117343f1fa50bd8566
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2020
ms.locfileid: "76308990"
---
此常见问题解答适用于使用经典部署模型的 P2S 连接。

### <a name="what-client-operating-systems-can-i-use-with-point-to-site"></a>点到站点连接允许使用哪些客户端操作系统？

支持以下客户端操作系统：

* Windows 7（32 位和 64 位）
* Windows Server 2008 R2（仅 64 位）
* Windows 8（32 位和 64 位）
* Windows 8.1（32 位和 64 位）
* Windows Server 2012（仅 64 位）
* Windows Server 2012 R2（仅 64 位）
* Windows 10

### <a name="can-i-use-any-software-vpn-client-that-supports-sstp-for-point-to-site"></a>是否可以使用支持将 SSTP 用于点到站点连接的任何软件 VPN 客户端？

不。 仅支持所列出的 Windows 操作系统版本。

### <a name="how-many-vpn-client-endpoints-can-exist-in-my-point-to-site-configuration"></a>在我的点到站点配置中，可以存在多少 VPN 客户端终结点？

VPN 客户端终结点的数量取决于网关 sku 和协议。
[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>能否将我自己的内部 PKI 根 CA 用于点到站点连接？

可以。 以前只可使用自签名根证书。 现在还可以上传最多 20 个根证书。

### <a name="can-i-traverse-proxies-and-firewalls-by-using-point-to-site"></a>是否可以使用点到站点连接穿越代理和防火墙？

可以。 我们使用安全套接字隧道协议 (SSTP) 作为隧道来穿越防火墙。 此隧道显示为 HTTPS 连接。

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>如果重新启动进行过点到站点配置的客户端计算机，是否会自动重新连接 VPN？

默认情况下，客户端计算机将不会自动重新建立 VPN 连接。

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>点到站点在 VPN 客户端上是否支持自动重新连接和 DDNS？

不。 点到站点 VPN 中当前不支持自动重新连接和 DDNS。

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-for-the-same-virtual-network"></a>同一虚拟网络中是否可以同时存在站点到站点和点到站点配置？

可以。 如果网关使用 RouteBased VPN 类型，这两种解决方案都可行。 对于经典部署模型，需要一个动态网关。 我们不支持将点到站点连接用于静态路由 VPN 网关，也不支持将其用于使用 **-VpnType PolicyBased** cmdlet 的网关。

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>能否将点到站点客户端配置为同时连接到多个虚拟网络？

可以。 但是，虚拟网络的 IP 前缀不得重叠，并且点到站点地址空间在虚拟网络之间不得重叠。

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>预计通过站点到站点连接或点到站点连接的吞吐量有多少？

很难维持 VPN 隧道的准确吞吐量。 IPsec 和 SSTP 是重重加密的 VPN 协议。 本地网络与 Internet 之间的延迟和带宽也限制了吞吐量。
