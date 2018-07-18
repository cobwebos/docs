---
title: 排查 Azure 站点到站点 VPN 间歇性地断开连接的问题 | Microsoft Docs
description: 了解如何排查站点到站点 VPN 定期连接断开的问题。
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 9c827469080195054d4ff70ab72fc123365a73df
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
ms.locfileid: "34160559"
---
# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>故障排除：Azure 站点到站点 VPN 间歇性地断开连接

可能会遇到新的或现有的 Microsoft Azure 站点到站点 VPN 连接不稳定或定期断开连接的问题。 本文提供了故障排除步骤，以帮助你确定并解决问题的原因。 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>疑难解答步骤

### <a name="prerequisite-step"></a>先决条件步骤

检查 Azure 虚拟网络网关的类型：

1. 转到 [Azure 门户](https://portal.azure.com)。
2. 有关类型信息，请查看虚拟网络网关的**概述**页。
    
    ![网关的概述](media\vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently\gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>步骤 1 检查是否已验证本地 VPN 设备

1. 检查是否使用的是[已验证的 VPN 设备和操作系统版本](vpn-gateway-about-vpn-devices.md#devicetable)。 如果未验证 VPN 设备，可能需要与设备制造商联系以了解是否存在任何兼容性问题。
2. 确保已正确配置 VPN 设备。 有关详细信息，请参阅[编辑设备配置示例](vpn-gateway-about-vpn-devices.md#editing)。

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>步骤 2 检查安全关联设置（适用于基于策略的 Azure 虚拟网络网关）

1. 请确保 Microsoft Azure 的**本地网络网关**定义中的虚拟网络、子网和范围与本地 VPN 设备上的配置相同。
2. 验证安全关联设置是否匹配。

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>步骤 3 检查网关子网上用户定义的路由或网络安全组

网关子网上用户定义的路由可能会限制某些流量，并允许其他流量。 这使得 VPN 连接看起来对于某些流量不可靠，而对于其他流量很可靠。 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>步骤 4 检查“每个子网对一个 VPN 隧道”设置（适用于基于策略的虚拟网络网关）

请确保本地 VPN 设备设置为对基于策略的虚拟网络网关采用**每个子网对一个 VPN 隧道**。

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>步骤 5 检查安全关联限制（适用于基于策略的虚拟网络网关）

基于策略的虚拟网络网关具有 200 个子网安全关联对的限制。 如果 Azure 虚拟网络子网数乘以本地子网数大于 200，请参阅“偶发的子网断开连接”。

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>步骤 6 检查本地 VPN 设备外部接口地址

- 如果 VPN 设备面向 Internet 的 IP 地址包含在 Azure 的**本地网络网关**定义中，你可能会遇到偶发的断开连接。
- 设备的外部接口必须直接在 Internet 上。 在 Internet 和设备之间应该没有网络地址转换 (NAT) 或防火墙。
-  如果将防火墙群集配置为具有虚拟 IP，则必须中断群集并直接向可以与网关连接的公共接口公开 VPN 设备。

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>步骤 7 检查本地 VPN 设备是否已启用“完全向前保密”

“完全向前保密”功能可能会导致断开连接问题。 如果 VPN 设备已启用“完全向前保密”，请禁用该功能。 然后[更新虚拟网络网关 IPsec 策略](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy)。

## <a name="next-steps"></a>后续步骤

- [配置与虚拟网络的站点到站点连接](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [配置站点到站点 VPN 连接的 IPsec/IKE 策略](vpn-gateway-ipsecikepolicy-rm-powershell.md)

