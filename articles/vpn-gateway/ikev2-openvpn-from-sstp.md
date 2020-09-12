---
title: 从 SSTP 过渡到 OpenVPN 或 IKEv2 | Azure VPN 网关
description: 本文帮助你了解如何克服 SSTP 的 128 个并发连接的限制。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: aec5a08bf56cd9a9ba18159ffc28a129163571b0
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89426276"
---
# <a name="transition-to-openvpn-protocol-or-ikev2-from-sstp"></a>从 SSTP 过渡到 OpenVPN 协议或 IKEv2

点到站点 (P2S) VPN 网关连接用于创建从单个客户端计算机到虚拟网络的安全连接。 可通过从客户端计算机启动连接来建立 P2S 连接。 本文适用于资源管理器部署模型，介绍如何通过过渡到 OpenVPN 协议或 IKEv2，来克服 SSTP 的 128 个并发连接的限制。

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>P2S 使用哪种协议？

点到站点 VPN 可使用以下协议之一：

* OpenVPN&reg; 协议，一个基于 SSL/TLS 的 VPN 协议。  由于大多数防火墙都会打开 SSL 所用的出站 TCP 端口 443，因此 SSL VPN 解决方案可以穿透防火墙。 OpenVPN 可用于从 Android、iOS（11.0 及更高版本）、Windows、Linux 和 Mac 设备（OSX 10.13 及更高版本）进行连接。

* 安全套接字隧道协议 (SSTP)，一个基于 SSL 的专属 VPN 协议。  由于大多数防火墙都会打开 SSL 所用的出站 TCP 端口 443，因此 SSL VPN 解决方案可以穿透防火墙。 只有 Windows 设备支持 SSTP。 Azure 支持所有采用 SSTP 的 Windows 版本（Windows 7 和更高版本）。 无论网关 SKU 是什么，SSTP 最多仅支持 128 个并发连接。 

* IKEv2 VPN，这是一种基于标准的 IPsec VPN 解决方案。 IKEv2 VPN 可用于从 Mac 设备进行连接（OSX 10.11 和更高版本）。


>[!NOTE]
>P2S 的 IKEv2 和 OpenVPN 仅可用于资源管理器部署模型。 它们不可用于经典部署模型。 基本网关 SKU 不支持 IKEv2 或 OpenVPN 协议。 如果使用的是基本 SKU，则必须删除再重新创建生产 SKU 虚拟网络网关。
>

## <a name="migrating-from-sstp-to-ikev2-or-openvpn"></a>从 SSTP 迁移到 IKEv2 或 OpenVPN

在某些情况下，你可能想要支持与 VPN 网关建立 128 个以上的并发 P2S 连接，但此时使用的是 SSTP。 对于此类情况，需要迁移到 IKEv2 或 OpenVPN 协议。

### <a name="option-1---add-ikev2-in-addition-to-sstp-on-the-gateway"></a>选项 1 - 在网关上除了使用 SSTP 以外，还添加 IKEv2

这是最简单的做法。 SSTP 和 IKEv2 可以在同一网关上共存，它们能够支持更多的并发连接。 只需在现有网关上启用 IKEv2，并重新下载客户端即可。

将 IKEv2 添加到现有 SSTP VPN 网关不会影响现有的客户端，可将这些客户端配置为在较小的批中使用 IKEv2，或者只是将新客户端配置为使用 IKEv2。 如果同时为 SSTP 和 IKEv2 配置了某个 Windows 客户端，该客户端首先会尝试使用 IKEV2 进行连接，如果失败，将回退到 SSTP。

IKEv2 使用非标准 UDP 端口，因此，你需要确保这些端口未在用户的防火墙中遭到阻止。  使用的端口是 UDP 500 和 4500。

若要将 IKEv2 添加到现有网关，只需在门户中转到虚拟网络网关下的“点到站点配置”选项卡，然后从下拉框中选择“IKEv2 和 SSTP (SSL)”。 

![点到站点](./media/ikev2-openvpn-from-sstp/sstptoikev2.png "IKEv2")


### <a name="option-2---remove-sstp-and-enable-openvpn-on-the-gateway"></a>选项 2 - 删除 SSTP 并在网关上启用 OpenVPN

由于 SSTP 和 OpenVPN 都是基于 TLS 的协议，它们不能在同一个网关上共存。 如果你决定从 SSTP 迁移到 OpenVPN，则必须禁用 SSTP，并在网关上启用 OpenVPN。 此操作会导致现有客户端断开与 VPN 网关的连接，直到在客户端上配置了新的配置文件。

如果需要，可以同时启用 OpenVPN 和 IKEv2。 OpenVPN 基于 TLS，使用标准的 TCP 443 端口。 若要切换到 OpenVPN，请在门户中转到虚拟网络网关下的“点到站点配置”选项卡，然后从下拉框中选择“OpenVPN (SSL)”或“IKEv2 和 OpenVPN (SSL)”。  

![点到站点](./media/ikev2-openvpn-from-sstp/sstptoopenvpn.png "OpenVPN")

配置网关后，在[部署并配置 OpenVPN 客户端](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-openvpn-clients)之前，现有客户端将无法进行连接。

如果使用的是 Windows 10，则还可以使用[适用于 Windows 的 Azure VPN 客户端](https://docs.microsoft.com/azure/vpn-gateway/openvpn-azure-ad-client#to-download-the-azure-vpn-client)


## <a name="frequently-asked-questions"></a>常见问题
### <a name="what-are-the-client-configuration-requirements"></a>客户端配置要求是什么？

>[!NOTE]
>对于 Windows 客户端，你必须具有客户端设备上的管理员权限，才能发起从客户端设备到 Azure 的 VPN 连接。
>

用户使用 Windows 和 Mac 设备上的本机 VPN 客户端建立 P2S 连接。 Azure 提供一个 VPN 客户端配置 zip 文件，其中包含这些本机客户端连接到 Azure 时所需的设置。

* 对于 Windows 设备，VPN 客户端配置包括用户在其设备上安装的安装程序包。
* 对于 Mac 设备，该配置包括用户在其设备上安装的 mobileconfig 文件。

该 zip 文件还提供 Azure 端上的一些重要设置的值，使用这些设置可为这些设备创建你自己的配置文件。 其中一些值包括 VPN 网关地址、配置的隧道类型、路由，以及用于网关验证的根证书。

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

### <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>哪些网关 SKU 支持 P2S VPN？

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* 有关网关 SKU 的建议，请参阅[关于 VPN 网关设置](vpn-gateway-about-vpn-gateway-settings.md#gwsku)。

>[!NOTE]
>基本 SKU 不支持 IKEv2 或 RADIUS 身份验证。
>

### <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>在 P2S 的 VPN 网关上配置了哪些 IKE/IPsec 策略？


**IKEv2**

|**Cipher** | **完整性** | **PRF** | **DH 组** |
|---        | ---            | ---        | ---     |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_24 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_14 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_ECP384 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_ECP256 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_24 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_14 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_ECP384 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_ECP256 |
|AES256     |   SHA384        | SHA384    | GROUP_24 |
|AES256     |   SHA384        | SHA384    | GROUP_14 |
|AES256     |   SHA384        | SHA384    | GROUP_ECP384 |
|AES256     |   SHA384        | SHA384    | GROUP_ECP256 |
|AES256     |   SHA256        | SHA256    | GROUP_24 |
|AES256     |   SHA256        | SHA256    | GROUP_14 |
|AES256     |   SHA256        | SHA256    | GROUP_ECP384 |
|AES256     |   SHA256        | SHA256    | GROUP_ECP256 |
|AES256     |   SHA256        | SHA256    | GROUP_2 |

**IPsec**

|**Cipher** | **完整性** | **PFS 组** |
|---        | ---            | ---        |
|GCM_AES256    | GCM_AES256 | GROUP_NONE |
|GCM_AES256    | GCM_AES256 | GROUP_24 |
|GCM_AES256    | GCM_AES256 | GROUP_14 |
|GCM_AES256    | GCM_AES256 | GROUP_ECP384 |
|GCM_AES256    | GCM_AES256 | GROUP_ECP256 |
| AES256    | SHA256 | GROUP_NONE |
| AES256    | SHA256 | GROUP_24 |
| AES256    | SHA256 | GROUP_14 |
| AES256    | SHA256 | GROUP_ECP384 |
| AES256    | SHA256 | GROUP_ECP256 |
| AES256    | SHA1 | GROUP_NONE |

### <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>在 P2S 的 VPN 网关上配置了哪些 TLS 策略？
**TLS**

|**策略** |
|---| 
|TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 |
|TLS_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_RSA_WITH_AES_256_CBC_SHA256 |

### <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>如何配置 P2S 连接？

P2S 配置需要相当多的特定步骤。 以下文章包含引导你完成 P2S 配置的步骤，以及用于配置 VPN 客户端设备的链接：

* [配置 P2S 连接 - RADIUS 身份验证](point-to-site-how-to-radius-ps.md)

* [配置 P2S 连接 - Azure 本机证书身份验证](vpn-gateway-howto-point-to-site-rm-ps.md)

* [配置 OpenVPN](vpn-gateway-howto-openvpn.md)

## <a name="next-steps"></a>后续步骤

* [配置 P2S 连接 - RADIUS 身份验证](point-to-site-how-to-radius-ps.md)

* [配置 P2S 连接 - Azure 本机证书身份验证](vpn-gateway-howto-point-to-site-rm-ps.md)

**“OpenVPN”是 OpenVPN Inc. 的商标。**
