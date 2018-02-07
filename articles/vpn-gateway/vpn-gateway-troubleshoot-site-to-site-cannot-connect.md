---
title: "排查 Azure 站点到站点 VPN 连接无法建立连接的问题 | Microsoft Docs"
description: "了解如何排查站点到站点 VPN 连接突然停止工作，不能重新建立连接的问题。"
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: 
tags: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/13/2017
ms.author: genli
ms.openlocfilehash: 96a1705d651b9a2d17a466b9c43721bec7b4972c
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2018
---
# <a name="troubleshooting-an-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>故障排除：Azure 站点到站点 VPN 连接无法建立连接并停止工作

在本地网络与 Azure 虚拟网络之间配置站点到站点 VPN 连接后，VPN 连接突然停止工作，不能重新建立连接。 本文提供解决此问题的故障排除步骤。 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>疑难解答步骤

若要解决该问题，请先尝试[重置 Azure VPN 网关](vpn-gateway-resetgw-classic.md)并重置从本地 VPN 设备建立的隧道。 如果问题仍然存在，请遵循以下步骤确定问题的原因。

### <a name="prerequisite-step"></a>先决条件步骤

检查 Azure VPN 网关的类型。

1. 转到 [Azure 门户](https://portal.azure.com)。

2. 有关类型信息，请查看 VPN 网关的“概述”页。
    
    ![网关概述](media\vpn-gateway-troubleshoot-site-to-site-cannot-connect\gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>步骤 1. 检查是否已验证本地 VPN 设备

1. 检查是否使用的是[已验证的 VPN 设备和操作系统版本](vpn-gateway-about-vpn-devices.md#devicetable)。 如果设备是未经验证的 VPN 设备，你可能需要与设备制造商联系，了解是否存在兼容性问题。

2. 确保已正确配置 VPN 设备。 有关详细信息，请参阅[编辑设备配置示例](vpn-gateway-about-vpn-devices.md#editing)。

### <a name="step-2-verify-the-shared-key"></a>步骤 2. 验证共享密钥

比较本地 VPN 设备和 Azure 虚拟网络 VPN 中的共享密钥，确保密钥匹配。 

若要查看 Azure VPN 连接的共享密钥，请使用以下方法之一：

**Azure 门户**

1. 转到创建的 VPN 网关站点到站点连接。

2. 在“设置”部分中，单击“共享密钥”。
    
    ![共享密钥](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

对于 Azure 资源管理器部署模型：

    Get-AzureRmVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>

对于经典部署模型：

    Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName

### <a name="step-3-verify-the-vpn-peer-ips"></a>步骤 3. 验证 VPN 对等 IP

-   Azure 的“本地网络网关”对象中的 IP 定义应与本地设备的 IP 相匹配。
-   在本地设备中设置的 Azure 网关 IP 定义应与 Azure 网关 IP 匹配。

### <a name="step-4-check-udr-and-nsgs-on-the-gateway-subnet"></a>步骤 4. 检查网关子网上的 UDR 和 NSG

检查并删除网关子网中的用户定义的路由 (UDR) 或网络安全组 (NSG)，然后测试结果。 如果问题得到解决，请验证 NSG 或 UDR 应用的设置。

### <a name="step-5-check-the-on-premises-vpn-device-external-interface-address"></a>步骤 5。 检查本地 VPN 设备的外部接口地址

- 如果 VPN 设备面向 Internet 的 IP 地址包含在 Azure 的“本地网络”定义中，可能会出现偶发的断开连接。
- 设备的外部接口必须直接在 Internet 上。 在 Internet 和设备之间应该没有网络地址转换或防火墙。
- 若要将防火墙群集配置为具有虚拟 IP，必须中断群集并直接向可以与网关连接的公共接口公开 VPN 设备。

### <a name="step-6-verify-that-the-subnets-match-exactly-azure-policy-based-gateways"></a>步骤 6. 验证子网是否完全匹配（基于 Azure 策略的网关）

-   验证虚拟网络地址空间与 Azure 虚拟网络和本地定义之间的子网是否完全匹配。
-   验证“本地网络网关”与本地网络本地定义之间的子网是否完全匹配。

### <a name="step-7-verify-the-azure-gateway-health-probe"></a>步骤 7. 验证 Azure 网关的运行状况探测

1. 转到运行状况探测。

2. 单击证书警告。
3. 如果收到响应，则可认为 VPN 网关正常。 如果未收到响应，则可能表示网关不正常，或者网关子网上的某个 NSG 导致出现问题。 以下文本是示例响应：

    &lt;?xml version="1.0"?>  <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Primary Instance: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6</string&gt;

### <a name="step-8-check-whether-the-on-premises-vpn-device-has-the-perfect-forward-secrecy-feature-enabled"></a>步骤 8。 检查本地 VPN 设备是否已启用完全向前保密功能

完全向前保密功能可能会导致断开连接问题。 如果 VPN 设备已启用完全向前保密，请禁用该功能。 然后更新 VPN 网关的 IPsec 策略。

## <a name="next-steps"></a>后续步骤

-   [配置与虚拟网络的站点到站点连接](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
-   [配置站点到站点 VPN 连接的 IPsec/IKE 策略](vpn-gateway-ipsecikepolicy-rm-powershell.md)
