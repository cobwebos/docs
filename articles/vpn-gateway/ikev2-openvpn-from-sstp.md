---
title: 从 SSTP 过渡到 OpenVPN 或 IKEv2 |Azure VPN 网关
description: 本文可帮助您了解克服 SSTP 的 128 并发连接限制的方法。
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: alzam
ms.openlocfilehash: 60c3a7f4f12993f475b3d03fd0915971d7673a5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80143100"
---
# <a name="transition-to-openvpn-protocol-or-ikev2-from-sstp"></a>从 SSTP 过渡到 OpenVPN 协议或 IKEv2

点到站点 (P2S) VPN 网关连接用于创建从单个客户端计算机到虚拟网络的安全连接。 可通过从客户端计算机启动连接来建立 P2S 连接。 本文适用于资源管理器部署模型，并讨论如何通过过渡到 OpenVPN 协议或 IKEv2 来克服 SSTP 的 128 个并发连接限制。

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>P2S 使用哪种协议？

点到站点 VPN 可使用以下协议之一：

* **OpenVPN&reg;协议**，基于 SSL/TLS 的 VPN 协议。 由于大多数防火墙都会打开 SSL 所用的出站 TCP 端口 443，因此 SSL VPN 解决方案可以穿透防火墙。 OpenVPN 可用于从 Android、iOS（11.0 及更高版本）、Windows、Linux 和 Mac 设备（OSX 10.13 及更高版本）进行连接。

* **安全套接字隧道协议 （SSTP），** 一种基于 SSL 的专有 VPN 协议. 由于大多数防火墙都会打开 SSL 所用的出站 TCP 端口 443，因此 SSL VPN 解决方案可以穿透防火墙。 只有 Windows 设备支持 SSTP。 Azure 支持所有采用 SSTP 的 Windows 版本（Windows 7 和更高版本）。 **SSTP 仅支持多达 128 个并发连接，而不考虑网关 SKU**。

* IKEv2 VPN，这是一种基于标准的 IPsec VPN 解决方案。 IKEv2 VPN 可用于从 Mac 设备进行连接（OSX 10.11 和更高版本）。


>[!NOTE]
>P2S 的 IKEv2 和 OpenVPN 仅可用于资源管理器部署模型。 它们不可用于经典部署模型。 基本网关 SKU 不支持 IKEv2 或 OpenVPN 协议。 如果使用基本 SKU，则必须删除并重新创建生产 SKU 虚拟网络网关。
>

## <a name="migrating-from-sspt-to-ikev2-or-openvpn"></a>从 SSPT 迁移到 IKEv2 或 OpenVPN

在某些情况下，您可能希望支持到 VPN 网关的 128 个并发 P2S 连接，但使用 SSTP。 在这种情况下，您需要迁移到 IKEv2 或 OpenVPN 协议。

### <a name="option-1---add-ikev2-in-addition-to-sstp-on-the-gateway"></a>选项 1 - 除了网关上的 SSTP 外添加 IKEv2

这是最简单的选项。 SSTP 和 IKEv2 可以共存于同一网关上，并为您提供数量较多的并发连接。 只需在现有网关上启用 IKEv2 并重新下载客户端即可。

将 IKEv2 添加到现有 SSTP VPN 网关不会影响现有客户端，您可以将其配置为小批量使用 IKEv2，或者仅将新客户端配置为使用 IKEv2。 如果为 SSTP 和 IKEv2 配置了 Windows 客户端，它将尝试首先使用 IKEV2 进行连接，如果失败，它将回落到 SSTP。

**IKEv2 使用非标准 UDP 端口，因此您需要确保这些端口不会在用户的防火墙上被阻止。正在使用的端口是 UDP 500 和 4500。**

要将 IKEv2 添加到现有网关，只需转到门户中虚拟网络网关下的"点对点配置"选项卡，然后从下拉框中选择**IKEv2 和 SSTP （SSL）。**

![点对点](./media/ikev2-openvpn-from-sstp/sstptoikev2.png "IKEv2")


### <a name="option-2---remove-sstp-and-enable-openvpn-on-the-gateway"></a>选项 2 - 删除 SSTP 并在网关上启用 OpenVPN

由于 SSTP 和 OpenVPN 都是基于 TLS 的协议，因此它们不能共存于同一网关上。 如果您决定从 SSTP 迁移到 OpenVPN，您必须禁用 SSTP 并在网关上启用 OpenVPN。 此操作将导致现有客户端失去与 VPN 网关的连接，直到客户端上配置了新配置文件。

如果需要，您可以与 IKEv2 一起启用 OpenVPN。 OpenVPN 基于 TLS，使用标准 TCP 443 端口。 要切换到 OpenVPN，请转到门户中虚拟网络网关下的"点对点配置"选项卡，然后从下拉框中选择**OpenVPN （SSL）** 或**IKEv2 和 OpenVPN （SSL）。**

![点对点](./media/ikev2-openvpn-from-sstp/sstptoopenvpn.png "OpenVPN")

配置网关后，在[部署和配置 OpenVPN 客户端](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-openvpn-clients)之前，现有客户端将无法连接。

如果使用 Windows 10，也可以对 Windows 使用[Azure VPN 客户端](https://docs.microsoft.com/azure/vpn-gateway/openvpn-azure-ad-client#to-download-the-azure-vpn-client)


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

### <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>哪个网关 SKU 支持 P2S VPN？

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* 有关网关 SKU 的建议，请参阅[关于 VPN 网关设置](vpn-gateway-about-vpn-gateway-settings.md#gwsku)。

>[!NOTE]
>基本 SKU 不支持 IKEv2 或 RADIUS 身份验证。
>

### <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>在 P2S 的 VPN 网关上配置了哪些 IKE/IPsec 策略？


**IKEv2**

|**Cipher** | **诚信** | **PRF** | **DH 组** |
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

**Ipsec**

|**Cipher** | **诚信** | **PFS 组** |
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
