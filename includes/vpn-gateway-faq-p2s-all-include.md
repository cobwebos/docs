---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ec684e5e6fa2ef8e9ed30be49f59e8aa7ef3a28b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79117152"
---
### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>在我的点到站点配置中，可以有多少 VPN 客户端终结点？

这取决于网关 SKU。 有关支持的连接数的详细信息，请参阅[网关 SKU](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)。

### <a name="what-client-operating-systems-can-i-use-with-point-to-site"></a><a name="supportedclientos"></a>点到站点连接可以用于哪些客户端操作系统？

支持以下客户端操作系统：

* Windows 7（32 位和 64 位）
* Windows Server 2008 R2（仅 64 位）
* Windows 8.1（32 位和 64 位）
* Windows Server 2012（仅 64 位）
* Windows Server 2012 R2（仅 64 位）
* Windows Server 2016（仅 64 位）
* Windows 10
* Mac OS X 版本 10.11 或更高版本
* Linux (StrongSwan)
* iOS

[!INCLUDE [TLS](vpn-gateway-tls-updates.md)]

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>能否使用点到站点功能穿越代理和防火墙？

Azure 支持三种类型的点到站点 VPN 选项：

* 安全套接字隧道协议 (SSTP)。 SSTP 是 Microsoft 专有的基于 SSL 的解决方案，它可以穿透防火墙，因为大多数防火墙都打开了 443 SSL 使用的出站 TCP 端口。

* OpenVPN。 OpenVPN 是基于 SSL 的解决方案，它可以穿透防火墙，因为大多数防火墙都打开了 443 SSL 使用的出站 TCP 端口。

* IKEv2 VPN。 IKEv2 VPN 是一个基于标准的 IPsec VPN 解决方案，它使用出站 UDP 端口 500 和 4500 以及 IP 协议号。 50。 防火墙并非始终打开这些端口，因此，IKEv2 VPN 有可能无法穿过代理和防火墙。

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>如果重新启动进行过点到站点配置的客户端计算机，是否会自动重新连接 VPN？

默认情况下，客户端计算机将不自动重新建立 VPN 连接。

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>点到站点在 VPN 客户端上是否支持自动重新连接和 DDNS？

点到站点 VPN 中当前不支持自动重新连接和 DDNS。

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>对于同一虚拟网络，站点到站点和点到站点配置能否共存？

是的。 对于资源管理器部署模型，必须为网关使用 RouteBased VPN 类型。 对于经典部署模型，需要一个动态网关。 不支持将点到站点配置用于静态路由 VPN 网关或 PolicyBased VPN 网关。

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-network-gateways-at-the-same-time"></a>能否将点到站点客户端配置为同时连接到多个虚拟网络网关？

根据所使用的 VPN 客户端软件，你可能可以连接到多个虚拟网络网关，前提是，要连接到的虚拟网络在它们或客户端要从中进行连接的网络之间不存在冲突的地址空间。  尽管 Azure VPN 客户端支持多个 VPN 连接，但在任何给定时间，都只能建立一个连接。

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>能否将点到站点客户端配置为同时连接到多个虚拟网络？

可以。与其他 VNet 对等互连的 VNet 中部署的虚拟网络网关之间的点到站点连接可能可以访问其他对等互连 VNet。  如果对等互连 VNet 使用 UseRemoteGateway/AllowGatewayTransit 功能，点到站点客户端将能够连接到这些对等互连 VNet。  有关详细信息，请参阅[此文](../articles/vpn-gateway/vpn-gateway-about-point-to-site-routing.md)。

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>预计通过站点到站点连接或点到站点连接的吞吐量有多少？

很难维持 VPN 隧道的准确吞吐量。 IPsec 和 SSTP 是重重加密的 VPN 协议。 本地网络与 Internet 之间的延迟和带宽也限制了吞吐量。 对于仅具有 IKEv2 点到站点 VPN 连接的 VPN 网关，期望可以实现的总吞吐量取决于网关 SKU。 有关吞吐量的详细信息，请参阅[网关 SKU](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)。

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp-andor-ikev2"></a>是否可以将任何软件 VPN 客户端用于支持 SSTP 和/或 IKEv2 的点到站点配置？

不是。 只能将 Windows 上的本机 VPN 客户端用于 SSTP，只能将 Mac 上的本机 VPN 客户端用于 IKEv2。 但是，可以在所有平台上使用 OpenVPN 客户端，以便通过 OpenVPN 协议进行连接。 请参阅支持的客户端操作系统的列表。

### <a name="does-azure-support-ikev2-vpn-with-windows"></a>Azure 是否支持使用 Windows 的 IKEv2 VPN？

在 Windows 10 和 Server 2016 上支持 IKEv2。 但是，若要使用 IKEv2，必须在本地安装更新并设置注册表项值。 Windows 10 以前的 OS 版本不受支持，并且只能使用 SSTP 或 **OpenVPN® 协议**。

为运行 IKEv2 准备 Windows 10 或 Server 2016：

1. 安装更新。

   | OS 版本 | Date | 编号/链接 |
   |---|---|---|
   | Windows Server 2016<br>Windows 10 版本 1607 | 2018 年 1 月 17 日 | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
   | Windows 10 版本 1703 | 2018 年 1 月 17 日 | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
   | Windows 10 版本 1709 | 2018 年 3 月 22 日 | [KB4089848](https://www.catalog.update.microsoft.com/search.aspx?q=kb4089848) |
   |  |  |  |

2. 设置注册表项值。 在注册表中创建“HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload”REG_DWORD 键或将其设置为 1。

### <a name="what-happens-when-i-configure-both-sstp-and-ikev2-for-p2s-vpn-connections"></a>为 P2S VPN 连接配置 SSTP 和 IKEv2 时，会发生什么情况？

在混合环境（包括 Windows 和 Mac 设备）中同时配置了 SSTP 和 IKEv2 时，Windows VPN 客户端始终将先尝试使用 IKEv2 隧道，但如果 IKEv2 连接不成功将回退到 SSTP。 MacOSX 将仅通过 IKEv2 进行连接。

### <a name="other-than-windows-and-mac-which-other-platforms-does-azure-support-for-p2s-vpn"></a>除了 Windows 和 Mac 以外，Azure 还支持在其他哪些平台上使用 P2S VPN？

Azure 支持将 Windows、Mac 和 Linux 用于 P2S VPN。

### <a name="i-already-have-an-azure-vpn-gateway-deployed-can-i-enable-radius-andor-ikev2-vpn-on-it"></a>我已部署 Azure VPN 网关。 是否可在该网关上启用 RADIUS 和/或 IKEv2 VPN？

是的，可以使用 Powershell 或 Azure 门户在已部署的网关上启用这些新功能，前提是所用网关 SKU 支持 RADIUS 和/或 IKEv2。 例如，VPN 网关基本 SKU 不支持 RADIUS 或 IKEv2。

### <a name="how-do-i-remove-the-configuration-of-a-p2s-connection"></a><a name="removeconfig"></a>如何删除 P2S 连接的配置？

可以通过 Azure CLI 和 PowerShell 使用以下命令删除 P2S 配置：

#### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$gw=Get-AzVirtualNetworkGateway -name <gateway-name>`  
$gw.VPNClientConfiguration = $null`  
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw`
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az network vnet-gateway update --name <gateway-name> --resource-group <resource-group name> --remove "vpnClientConfiguration"
```
