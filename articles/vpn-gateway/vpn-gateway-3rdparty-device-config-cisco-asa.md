---
title: "示例配置 - 连接到 Azure VPN 网关的 Cisco ASA 设备 | Microsoft Docs"
description: "本文提供连接到 Azure VPN 网关的 Cisco ASA 设备的示例配置。"
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: 
ms.assetid: a8bfc955-de49-4172-95ac-5257e262d7ea
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: yushwang
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: 10466b8928e2cd687f7961a2956b6d60823b82be
ms.contentlocale: zh-cn
ms.lasthandoff: 07/10/2017


---
# <a name="sample-configuration-cisco-asa-device-ikev2no-bgp"></a>示例配置：Cisco ASA 设备（IKEv2/无 BGP）
本文提供连接到 Azure VPN 网关的 Cisco ASA 设备的示例配置。

## <a name="device-at-a-glance"></a>设备概览

|                        |                                   |
| ---                    | ---                               |
| 设备供应商          | Cisco                             |
| 设备型号           | ASA（自适应安全设备） |
| 目标版本         | 8.4+                              |
| 测试的模型           | ASA 5505                          |
| 测试的版本         | 9.2                               |
| IKE 版本            | **IKEv2**                         |
| BGP                    | **否**                            |
| Azure VPN 网关类型 | **基于路由**的 VPN 网关       |
|                        |                                   |

> [!NOTE]
> 1. 以下配置根据[此文](vpn-gateway-connect-multiple-policybased-rm-ps.md)中所述，结合“UserPolicyBasedTrafficSelectors”选项使用自定义 IPsec/IKE 策略将 Cisco ASA 设备连接到 Azure **基于路由**的 VPN 网关。
> 2. 它要求 ASA 设备中的 **IKEv2** 使用基于访问列表的配置，而不是基于 VTI 的配置。
> 3. 请查阅 VPN 设备供应商规范，确保本地 VPN 设备支持该策略。

## <a name="vpn-device-requirements"></a>VPN 设备要求
Azure VPN 网关使用标准 IPsec/IKE 协议套件建立 S2S VPN 隧道。 请参阅[关于 VPN 设备](vpn-gateway-about-vpn-devices.md)，了解 Azure VPN 网关的详细 IPsec/IKE 协议参数和默认加密算法。 （可选）可以根据[关于加密要求](vpn-gateway-about-compliance-crypto.md)中所述，为特定的连接准确指定加密算法与密钥长度的组合。 如果选择加密算法与密钥强度的特定组合，请确保在 VPN 设备上使用相应的规范。

## <a name="single-vpn-tunnel"></a>单一 VPN 隧道
此拓扑由 Azure VPN 网关与本地 VPN 设备之间的单个 S2S VPN 隧道构成。 （可选）可以配置跨 VPN 隧道的 BGP。

![单一隧道](./media/vpn-gateway-3rdparty-device-config-cisco-asa/singletunnel.png)

请参阅[单一隧道设置](vpn-gateway-3rdparty-device-config-overview.md#singletunnel)，获取有关构建 Azure 配置的详细分步说明。

### <a name="network-and-vpn-gateway-information"></a>网络和 VPN 网关信息
本部分列出本示例的参数。

| **Parameter**                | **值**                    |
| ---                          | ---                          |
| VNet 地址前缀        | 10.11.0.0/16<br>10.12.0.0/16 |
| Azure VPN 网关 IP         | Azure_Gateway_Public_IP      |
| 本地地址前缀 | 10.51.0.0/16<br>10.52.0.0/16 |
| 本地 VPN 设备 IP    | OnPrem_Device_Public_IP     |
| * VNet BGP ASN                | 65010                        |
| *Azure BGP 对等 IP           | 10.12.255.30                 |
| *本地 BGP ASN         | 65050                        |
| *本地 BGP 对等 IP     | 10.52.255.254                |
|                              |                              |

* (*) 仅适用于 BGP 的可选参数。

### <a name="ipsecike-policy--parameters"></a>IPsec/IKE 策略和参数

下表列出本示例中使用的 IPsec/IKE 算法和参数。 请查阅 VPN 设备规范，确保 VPN 设备型号和固件版本支持上面列出的所有算法。

| IPsec/IKEv2  | **值**                            |
| ---              | ---                                  |
| IKEv2 加密 | AES256                               |
| IKEv2 完整性  | SHA384                               |
| DH 组         | DHGroup24                            |
| IPsec 加密 | AES256                               |
| IPsec 完整性  | SHA1                                 |
| PFS 组        | PFS24                                |
| QM SA 生存期   | 7200 秒                         |
| 流量选择器 | UsePolicyBasedTrafficSelectors $True |
| 预共享密钥   | PreSharedKey                         |
|                  |                                      |

- (*) 在某些设备上，如果 GCM-AES 用作 IPsec 加密算法，则 IPsec 完整性必须为“null”。

### <a name="device-notes"></a>设备说明

>[!NOTE]
>
> 1. IKEv2 支持需要 ASA 8.4 和更高版本。
> 2. 更高的 DH 和 PFS 组支持（组超过 5 个）需要 ASA 版本 9.x。
> 3. IPsec 加密为 AES-GCM 且 IPsec 完整性为 SHA-256、SHA-384、SHA-512 的支持需要在较新的 ASA 硬件上安装 ASA 版本 9.x；**不**支持 ASA 5505、5510、5520、5540、5550、5580 （请查看供应商规范来确认是否受支持。）
>


### <a name="sample-device-configurations"></a>示例设备配置
以下脚本基于上面列出的拓扑和参数提供示例配置。 S2S VPN 隧道配置由以下部分组成：

1. 接口和路由
2. 访问列表
3. IKE 策略和参数（第 1 阶段或主模式）
4. IPsec 策略和参数（第 2 阶段或快速模式）
5. 其他参数（TCP MSS 钳位等）

>[!IMPORTANT] 
>请确保完成下面列出的附加配置，并将占位符替换为实际值：
> 
> - 内部和外部接口的接口配置
> - 内部/专用和外部/公共网络的路由
> - 确保所有名称和策略编号在设备上唯一
> - 确保设备支持加密算法
> - 将以下占位符替换为实际值
>   - 外部接口名称："outside"
>   - Azure_Gateway_Public_IP
>   - OnPrem_Device_Public_IP
>   - IKE Pre_Shared_Key
>   - VNet 和本地网络网关名称（VNetName、LNGName）
>   - VNet 和本地网络地址前缀
>   - 正确的网络掩码

#### <a name="sample-configuration"></a>示例配置

```
! Sample ASA configuration for connecting to Azure VPN gateway
!
! Tested hardware: ASA 5505
! Tested version:  ASA version 9.2(4)
!
! Replace the following place holders with your actual values:
!   - Interface names - default are "outside" and "inside"
!   - <Azure_Gateway_Public_IP>
!   - <OnPrem_Device_Public_IP>
!   - <Pre_Shared_Key>
!   - <VNetName>*
!   - <LNGName>* ==> LocalNetworkGateway - the Azure resource that represents the
!     on-premises network, specifies network prefixes, device public IP, BGP info, etc.
!   - <PrivateIPAddress> ==> Replace it with a private IP address if applicable
!   - <Netmask> ==> Replace it with appropriate netmasks
!   - <Nexthop> ==> Replace it with the actual nexthop IP address
!
! (*) Must be unique names in the device configuration
!
! ==> Interface & route configurations
!
!     > <OnPrem_Device_Public_IP> address on the outside interface or vlan
!     > <PrivateIPAddress> on the inside interface or vlan; e.g., 10.51.0.1/24
!     > Route to connect to <Azure_Gateway_Public_IP> address
!
!     > Example:
!
!       interface Ethernet0/0
!        switchport access vlan 2
!       exit
!
!       interface vlan 1
!        nameif inside
!        security-level 100
!        ip address <PrivateIPAddress> <Netmask>
!       exit
!
!       interface vlan 2
!        nameif outside
!        security-level 0
!        ip address <OnPrem_Device_Public_IP> <Netmask>
!       exit
!
!       route outside 0.0.0.0 0.0.0.0 <NextHop IP> 1
!
! ==> Access lists
!
!     > Most firewall devices deny all traffic by default. Create access lists to
!       (1) Allow S2S VPN tunnels between the ASA and the Azure gateway public IP address
!       (2) Construct traffic selectors as part of IPsec policy or proposal
!
access-list outside_access_in extended permit ip host <Azure_Gateway_Public_IP> host <OnPrem_Device_Public_IP>
!
!     > Object group that consists of all VNet prefixes (e.g., 10.11.0.0/16 &
!       10.12.0.0/16)
!
object-group network Azure-<VNetName>
 description Azure virtual network <VNetName> prefixes
 network-object 10.11.0.0 255.255.0.0
 network-object 10.12.0.0 255.255.0.0
exit
!
!     > Object group that corresponding to the <LNGName> prefixes.
!       E.g., 10.51.0.0/16 and 10.52.0.0/16. Note that LNG = "local network gateway".
!       In Azure network resource, a local network gateway defines the on-premises
!       network properties (address prefixes, VPN device IP, BGP ASN, etc.)
!
object-group network <LNGName>
 description On-Premises network <LNGName> prefixes
 network-object 10.51.0.0 255.255.0.0
 network-object 10.52.0.0 255.255.0.0
exit
!
!     > Specify the access-list between the Azure VNet and your on-premises network.
!       This access list defines the IPsec SA traffic selectors.
!
access-list Azure-<VNetName>-acl extended permit ip object-group <LNGName> object-group Azure-<VNetName>
!
!     > No NAT required between the on-premises network and Azure VNet
!
nat (inside,outside) source static <LNGName> <LNGName> destination static Azure-<VNetName> Azure-<VNetName>
!
! ==> IKEv2 configuration
!
!     > General IKEv2 configuration - enable IKEv2 for VPN
!
group-policy DfltGrpPolicy attributes
 vpn-tunnel-protocol ikev1 ikev2
exit
!
crypto isakmp identity address
crypto ikev2 enable outside
!
!     > Define IKEv2 Phase 1/Main Mode policy
!       - Make sure the policy number is not used
!       - integrity and prf must be the same
!       - DH group 14 and above require ASA version 9.x.
!
crypto ikev2 policy 1
 encryption       aes-256
 integrity        sha384
 prf              sha384
 group            24
 lifetime seconds 86400
exit
!
!     > Set connection type and pre-shared key
!
tunnel-group <Azure_Gateway_Public_IP> type ipsec-l2l
tunnel-group <Azure_Gateway_Public_IP> ipsec-attributes
 ikev2 remote-authentication pre-shared-key <Pre_Shared_Key> 
 ikev2 local-authentication  pre-shared-key <Pre_Shared_Key> 
exit
!
! ==> IPsec configuration
!
!     > IKEv2 Phase 2/Quick Mode proposal
!       - AES-GCM and SHA-2 requires ASA version 9.x on newer ASA models. ASA
!         5505, 5510, 5520, 5540, 5550, 5580 are not supported.
!       - ESP integrity must be null if AES-GCM is configured as ESP encryption
!
crypto ipsec ikev2 ipsec-proposal AES-256
 protocol esp encryption aes-256
 protocol esp integrity  sha-1
exit
!
!     > Set access list & traffic selectors, PFS, IPsec protposal, SA lifetime
!       - This sample uses "Azure-<VNetName>-map" as the crypto map name
!       - ASA supports only one crypto map per interface, if you already have
!         an existing crypto map assigned to your outside interface, you must use
!         the same crypto map name, but with a different sequence number for
!         this policy
!       - "match address" policy uses the access-list "Azure-<VNetName>-acl" defined 
!         previously
!       - "ipsec-proposal" uses the proposal "AES-256" defined previously 
!       - PFS groups 14 and beyond requires ASA version 9.x.
!
crypto map Azure-<VNetName>-map 1 match address Azure-<VNetName>-acl
crypto map Azure-<VNetName>-map 1 set pfs group24
crypto map Azure-<VNetName>-map 1 set peer <Azure_Gateway_Public_IP>
crypto map Azure-<VNetName>-map 1 set ikev2 ipsec-proposal AES-256
crypto map Azure-<VNetName>-map 1 set security-association lifetime seconds 7200
crypto map Azure-<VNetName>-map interface outside
!
! ==> Set TCP MSS to 1350
!
sysopt connection tcpmss 1350
!
```

## <a name="simple-debugging-commands"></a>简单调试命令

下面是用于调试的一些 ASA 命令：

1. 显示 IPsec 和 IKE SA
    - "show crypto ipsec sa"
    - "show crypto ikev2 sa"
2. 进入调试模式 - 这可能会在控制台上产生很大的噪声
    - "debug crypto ikev2 platform <level>"
    - "debug crypto ikev2 protocol <level>"
3. 列出当前配置
    - "show run" - 显示设备上的当前配置；可以使用不同的子命令列出特定的配置部分。 例如，"show run crypto"、"show run access-list"、"show run tunnel-group"，等等。


## <a name="next-steps"></a>后续步骤
有关配置主动-主动跨界连接和 VNet 到 VNet 连接的步骤，请参阅[为跨界连接和 VNet 到 VNet 连接配置主动-主动 VPN 网关](vpn-gateway-activeactive-rm-powershell.md)。


