---
title: Azure VPN 网关：加密要求
description: 本文介绍加密要求和 Azure VPN 网关
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: yushwang
ms.openlocfilehash: 93e13592d9c434b159ad4f4c10ef30328941c64e
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75902825"
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>关于加密要求和 Azure VPN 网关

本文介绍如何配置 Azure VPN 网关，满足 Azure 中跨界 S2S VPN 隧道和 VNet 到 VNet 连接的加密要求。

## <a name="about-ikev1-and-ikev2-for-azure-vpn-connections"></a>关于 Azure VPN 连接的 IKEv1 和 IKEv2

过去，我们仅允许基本 Sku 使用 IKEv1 连接，并允许除基本 Sku 之外的所有 VPN 网关 Sku 使用 IKEv2 连接。 基本 Sku 只允许1个连接，并提供其他限制，如性能，使用仅支持 IKEv1 协议的旧设备的客户的体验有限。 为了增强使用 IKEv1 协议的客户体验，我们现在允许除基本 SKU 之外的所有 VPN 网关 Sku 使用 IKEv1 连接。 有关详细信息，请参阅[VPN 网关 sku](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku)。

![Azure VPN 网关 IKEv1 和 IKEv2 连接](./media/vpn-gateway-about-compliance-crypto/ikev1-ikev2-connections.png)

如果将 IKEv1 和 IKEv2 连接应用到同一个 VPN 网关，则这两个连接之间的传输将自动启用。

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>关于 Azure VPN 网关的 IPsec 和 IKE 策略参数

IPsec 和 IKE 协议标准支持采用各种组合的各种加密算法。 如果未请求特定的加密算法和参数组合，则 Azure VPN 网关会使用一组默认方案。 选择默认策略集，最大限度地实现默认配置中各种第三方 VPN 设备的互操作性。 因此，策略和方案数无法涵盖所有可能的可用加密算法和密钥强度组合。

Azure VPN 网关的默认策略集在文章：[关于用于站点到站点 Vpn 网关连接的 vpn 设备和 IPsec/IKE 参数](vpn-gateway-about-vpn-devices.md)一文中列出。

## <a name="cryptographic-requirements"></a>加密要求

对于需要特定加密算法或参数的通信，通常由于符合性或安全性要求，你现在可以将其 Azure VPN 网关配置为使用具有特定加密算法和密钥强度的自定义 IPsec/IKE 策略，而不是使用 Azure 默认策略集。

例如，Azure VPN 网关的 IKEv2 主模式策略仅使用 Diffie-hellman 组2（1024位），而你可能需要指定要在 IKE 中使用的更强的组，例如组14（2048位）、组24（2048位 MODP 组）或 ECP （椭圆曲线组）256或384位（分别为组19和组20）。 类似的要求也适用于 IPsec 快速模式策略。

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>借助 Azure VPN 网关自定义 IPsec/IKE 策略

Azure VPN 网关现支持根据连接自定义 IPsec/IKE 策略。 对于站点到站点或 VNet 到 VNet 连接，可为具有所需密钥强度的 IPsec 和 IKE 选择特定加密算法组合，如下例所示：

![ipsec-ike-policy](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

可创建 IPsec/IKE 策略并将其应用于新的或现有的连接。

### <a name="workflow"></a>工作流

1. 为连接拓扑创建虚拟网络、VPN 网关或本地网络网关，如其他操作文档所述
2. 创建 IPsec/IKE 策略
3. 可在创建 S2S 或 VNet 到 VNet 连接时应用该策略
4. 如已创建连接，可对现有连接应用或更新策略

## <a name="ipsecike-policy-faq"></a>IPsec/IKE 策略常见问题解答

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]

## <a name="next-steps"></a>后续步骤

若要了解在连接上配置自定义 IPsec/IKE 策略的分步说明，请参阅[配置 IPsec/IKE 策略](vpn-gateway-ipsecikepolicy-rm-powershell.md)。

另请参阅[连接多个基于策略的 VPN 设备](vpn-gateway-connect-multiple-policybased-rm-ps.md)，了解有关 UsePolicyBasedTrafficSelectors 选项的详细信息。
