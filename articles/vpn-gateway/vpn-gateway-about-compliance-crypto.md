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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75902825"
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>关于加密要求和 Azure VPN 网关

本文介绍如何配置 Azure VPN 网关，满足 Azure 中跨界 S2S VPN 隧道和 VNet 到 VNet 连接的加密要求。

## <a name="about-ikev1-and-ikev2-for-azure-vpn-connections"></a>关于用于 Azure VPN 连接的 IKEv1 和 IKEv2

传统上，我们只允许将 IKEv1 连接用于基本 SKU，允许将 IKEv2 连接用于除基本 SKU 之外的所有 VPN 网关 SKU。 基本 SKU 只允许使用 1 个连接，并且有其他限制（例如性能限制）。使用只支持 IKEv1 协议的旧设备的客户其体验会受限。 为了增强使用 IKEv1 协议的客户的体验，我们现在允许将 IKEv1 连接用于除基本 SKU 之外的所有 VPN 网关 SKU。 有关详细信息，请参阅 [VPN 网关 SKU](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku)。

![Azure VPN 网关 IKEv1 和 IKEv2 连接](./media/vpn-gateway-about-compliance-crypto/ikev1-ikev2-connections.png)

将 IKEv1 和 IKEv2 连接应用到同一 VPN 网关时，会自动启用这两个连接之间的传输。

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>关于 Azure VPN 网关的 IPsec 和 IKE 策略参数

IPsec 和 IKE 协议标准支持采用各种组合的各种加密算法。 如果不要求使用特定加密算法和参数组合，则 Azure VPN 网关会使用一组默认方案。 选择默认策略集，最大限度地实现默认配置中各种第三方 VPN 设备的互操作性。 因此，策略和方案数无法涵盖所有可能的可用加密算法和密钥强度组合。

本文列出了 Azure VPN 网关的默认策略集：[关于 VPN 设备和站点到站点 VPN 网关连接的 IPsec/IKE 参数](vpn-gateway-about-vpn-devices.md)。

## <a name="cryptographic-requirements"></a>加密要求

对于需特定加密算法或参数的通信，通常由于符合性或安全性要求，你现在可配置其 Azure VPN 网关，使用具有特定加密算法和密钥强度的自定义 IPsec/IKE 策略，而不是使用 Azure 默认策略集。

例如，Azure VPN 网关的 IKEv2 主模式策略仅使用 Diffie-Hellman 组 2（1024 位），而你可能需要指定更强的组用于 IKE，例如组 14（2048 位）、组 24（2048 位 MODP 组）或 ECP（椭圆曲线组）256 或 384 位（分别为组 19 和组 20）。 类似的要求也适用于 IPsec 快速模式策略。

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
