---
title: "关于 Azure 点到站点 VPN 连接 | Microsoft 文档"
description: "可以借助本文了解点到站点连接，并确定要使用的 P2S VPN 网关身份验证类型。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: f19b58ad8f12169acefd74bfe86a28ad950dad34
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="about-point-to-site-vpn"></a>关于点到站点 VPN

点到站点 (P2S) VPN 网关连接用于创建从单个客户端计算机到虚拟网络的安全连接。 可通过从客户端计算机启动连接来建立 P2S 连接。 对于要从远程位置（例如从家里或会议室）连接到 Azure VNet 的远程工作者，此解决方案很有用。 如果只有一些客户端需要连接到 VNet，则还可以使用 P2S VPN 这一解决方案来代替 S2S VPN。

## <a name="protocol"></a>P2S 使用哪种协议？

点到站点 VPN 可使用以下协议之一：

* 安全套接字隧道协议 (SSTP)，这是一种基于 SSL 的专属协议。 由于大多数防火墙都会打开 SSL 所用的 TCP 端口 443，因此 SSL VPN 解决方案可以穿透防火墙。 只有 Windows 设备支持 SSTP。 Azure 支持所有采用 SSTP 的 Windows 版本（Windows 7 和更高版本）。

* IKEv2 VPN，这是一种基于标准的 IPsec VPN 解决方案。 IKEv2 VPN 可用于从 Mac 设备进行连接（OSX 10.11 和更高版本）。

如果在混合客户端环境中包括 Windows 和 Mac 设备，请同时配置 SSTP 和 IKEv2。

>[!NOTE]
>IKEv2 for P2S 目前处于预览状态。
>

## <a name="authentication"></a>如何对 P2S VPN 客户端进行身份验证？

在 Azure 接受 P2S VPN 连接之前，必须先对用户进行身份验证。 Azure 提供两种机制用于对连接方用户进行身份验证。

### <a name="authenticate-using-native-azure-certificate-authentication"></a>使用本机 Azure 证书身份验证进行身份验证

使用本机 Azure 证书身份验证时，设备上的客户端证书用于对连接方用户进行身份验证。 客户端证书从受信任的根证书生成，并安装在每台客户端计算机上。 可以使用通过企业解决方案生成的根证书，也可以生成自签名证书。

客户端证书的验证由 VPN 网关执行，在建立 P2S VPN 连接期间发生。 验证时需要使用根证书，必须将该证书上传到 Azure。 

### <a name="authenticate-using-active-directory-ad-domain-server"></a>使用 Active Directory (AD) 域服务器进行身份验证

AD 域身份验证可让用户使用其组织域凭据连接到 Azure。 它需要一台与 AD 服务器集成的 RADIUS 服务器。 组织也可以利用其现有的 RADIUS 部署。   
 可将 RADIUS 服务器部署在本地或 Azure VNET 中。 在身份验证期间，Azure VPN 网关充当传递设备，在 RADIUS 服务器与连接方设备之间来回转发身份验证消息。 因此，RADIUS 服务器必须能够访问网关。 如果 RADIUS 服务器位于本地，需要建立从 Azure 到本地站点的 VPN S2S 连接，才能实现这种访问。  
 RADIUS 服务器还能与 AD 证书服务集成。 这样，便可以使用 RADIUS 服务器以及用于 P2S 证书身份验证的企业证书部署，作为 Azure 证书身份验证的替代方法。 此方法的优点是不需要将根证书和吊销的证书上传到 Azure。

RADIUS 服务器还能与其他外部标识系统集成。 这样就为 P2S VPN 提供了大量的身份验证选项，包括多重身份验证选项。

>[!NOTE]
>适用于 P2S 的 RADIUS 身份验证目前处于预览状态。
>

![point-to-site]](./media/point-to-site-about/p2s.png "Point-to-Site")

### <a name="configuration-requirements-for-client-devices"></a>客户端设备的配置要求

用户使用 Windows 和 Mac 设备上的本机 VPN 客户端建立 P2S 连接。 Azure 提供一个 VPN 客户端配置 zip 文件，其中包含这些本机客户端连接到 Azure 时所需的设置。

  * 对于 Windows 设备，VPN 客户端配置包括用户在其设备上安装的安装程序包。
  * 对于 Mac 设备，该配置包括用户在其设备上安装的 mobileconfig 文件。

该 zip 文件还提供 Azure 端上的一些重要设置的值，使用这些设置可为这些设备创建你自己的配置文件。 其中一些值包括 VPN 网关地址、配置的隧道类型、路由，以及用于网关验证的根证书。

### <a name="which-gateway-skus-support-p2s-vpn"></a>哪些网关 SKU 支持 P2S VPN？

[!INCLUDE [p2s-skus](../../includes/vpn-gateway-table-point-to-site-skus-include.md)]

* 聚合吞吐量基准基于对通过单个网关聚合的多个隧道的测量。 受 Internet 流量情况和应用程序行为影响，该吞吐量无法保证。
* 可在“定价”页上找到定价信息 
* 可在“SLA”页上查看 SLA（服务级别协议）信息。

## <a name="faqcert"></a>本机 Azure 证书身份验证常见问题解答

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faqradius"></a>RADIUS 身份验证常见问题解答

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>后续步骤

[配置 P2S 连接 - RADIUS 身份验证](point-to-site-how-to-radius-ps.md)

[配置 P2S 连接 - Azure 本机证书身份验证](vpn-gateway-howto-point-to-site-rm-ps.md)
