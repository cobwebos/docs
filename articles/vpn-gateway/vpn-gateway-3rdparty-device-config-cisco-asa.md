---
title: "将 Cisco ASA 设备连接到 Azure VPN 网关的示例配置 | Microsoft Docs"
description: "本文提供将 Cisco ASA 设备连接到 Azure VPN 网关的示例配置。"
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
ms.openlocfilehash: fbe22b70b4fe3463ffc7b0e9a7ebd683f681117d
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="sample-configuration-cisco-asa-device-ikev2no-bgp"></a>示例配置：Cisco ASA 设备（IKEv2/无 BGP）
本文提供将 Cisco 自适应安全 (ASA) 设备连接到 Azure VPN 网关的示例配置。 该示例适用于运行 IKEv2 而不使用边界网关协议 (BGP) 的 Cisco ASA 设备。 

## <a name="device-at-a-glance"></a>设备概览

|                        |                                   |
| ---                    | ---                               |
| 设备供应商          | Cisco                             |
| 设备型号           | ASA                               |
| 目标版本         | 8.4 及更高版本                     |
| 测试的模型           | ASA 5505                          |
| 测试的版本         | 9.2                               |
| IKE 版本            | IKEv2                             |
| BGP                    | 否                                |
| Azure VPN 网关类型 | 基于路由的 VPN 网关           |
|                        |                                   |

> [!NOTE]
> 示例配置将 Cisco ASA 设备连接到 Azure 基于路由的 VPN 网关。 连接使用自定义 IPsec/IKE 策略并启用“UsePolicyBasedTrafficSelectors”选项，如[这篇文章](vpn-gateway-connect-multiple-policybased-rm-ps.md)中所述。
>
> 示例要求 ASA 设备中的“IKEv2”策略使用基于访问列表的配置，而不是基于 VTI 的配置。 请查阅 VPN 设备供应商规范，验证本地 VPN 设备是否支持 IKEv2 策略。


## <a name="vpn-device-requirements"></a>VPN 设备要求
Azure VPN 网关使用标准 IPsec/IKE 协议套件建立站点到站点 (S2S) VPN 隧道。 若要了解 Azure VPN 网关的详细 IPsec/IKE 协议参数和默认加密算法，请参阅[关于 VPN 设备](vpn-gateway-about-vpn-devices.md)。

> [!NOTE]
> （可选）可以根据[关于加密要求](vpn-gateway-about-compliance-crypto.md)中所述，为特定的连接准确指定加密算法与密钥长度的组合。 如果准确指定了算法与密钥强度的组合，请确保在 VPN 设备上使用相应的规范。

## <a name="single-vpn-tunnel"></a>单一 VPN 隧道
此配置由 Azure VPN 网关与本地 VPN 设备之间的单个 S2S VPN 隧道构成。 （可选）可以配置 [跨 VPN 隧道的 BGP](#bgp)。

![单一 S2S VPN 隧道](./media/vpn-gateway-3rdparty-device-config-cisco-asa/singletunnel.png)

若要获取有关构建 Azure 配置的详细分步说明，请参阅[单一隧道设置](vpn-gateway-3rdparty-device-config-overview.md#singletunnel)。

### <a name="virtual-network-and-vpn-gateway-information"></a>虚拟网络和 VPN 网关信息
本部分列出示例的参数。

| **Parameter**                | **值**                    |
| ---                          | ---                          |
| 虚拟网络地址前缀        | 10.11.0.0/16<br>10.12.0.0/16 |
| Azure VPN 网关 IP         | Azure_Gateway_Public_IP      |
| 本地地址前缀 | 10.51.0.0/16<br>10.52.0.0/16 |
| 本地 VPN 设备 IP    | OnPrem_Device_Public_IP     |
| * 虚拟网络 BGP ASN                | 65010                        |
| * Azure BGP 对等 IP           | 10.12.255.30                 |
| * 本地 BGP ASN         | 65050                        |
| * 本地 BGP 对等 IP     | 10.52.255.254                |
|                              |                              |

\* 仅适用于 BGP 的可选参数。

### <a name="ipsecike-policy-and-parameters"></a>IPsec/IKE 策略和参数
下表列出本示例中使用的 IPsec/IKE 算法和参数。 请查阅 VPN 设备规范，验证 VPN 设备型号和固件版本支持的算法。

| IPsec/IKEv2  | **值**                            |
| ---              | ---                                  |
| IKEv2 加密 | AES256                               |
| IKEv2 完整性  | SHA384                               |
| DH 组         | DHGroup24                            |
| * IPsec 加密 | AES256                               |
| * IPsec 完整性  | SHA1                                 |
| PFS 组        | PFS24                                |
| QM SA 生存期   | 7,200 秒                         |
| 流量选择器 | UsePolicyBasedTrafficSelectors $True |
| 预共享密钥   | PreSharedKey                         |
|                  |                                      |

\* 在某些设备上，IPsec 加密算法是 AES GCM 时，IPsec 完整性必须为 null 值。

### <a name="asa-device-support"></a>ASA 设备支持

* IKEv2 支持需要 ASA 8.4 和更高版本。

* DH 和超过 5 个组的 PFS 组支持需要 ASA 版本 9.x。

* 需要 ASA 9.x 版本，才能使用 AES GCM 实现 IPsec 加密，并使用 SHA-256、SHA-384 或 SHA-512 实现 IPsec 完整性。 此支持要求适用于较新的 ASA 设备。 在发布时，ASA 型号 5505、5510、5520、5540、5550 和 5580 不支持这些算法。 请查阅 VPN 设备规范，验证 VPN 设备型号和固件版本支持的算法。


### <a name="sample-device-configuration"></a>示例设备配置
此脚本提供基于前几节所述配置和参数的示例。 S2S VPN 隧道配置由以下部分组成：

1. 接口和路由
2. 访问列表
3. IKE 策略和参数（第 1 阶段或主模式）
4. IPsec 策略和参数（第 2 阶段或快速模式）
5. 其他参数（如 TCP MSS 钳位）

> [!IMPORTANT]
> 在使用示例脚本之前，请完成以下步骤。 将脚本中的占位符值替换为你置配的设备设置。

* 指定内部和外部接口的接口配置。
* 标识内部/专用和外部/公共网络的路由。
* 确保所有名称和策略编号在设备上唯一。
* 确保设备支持加密算法。
* 将以下“占位符值”替换为你配置的实际值：
  - 外部接口名称："outside"
  - Azure_Gateway_Public_IP
  - OnPrem_Device_Public_IP
  - IKE：IKE Pre_Shared_Key
  - 虚拟网络和本地网络网关名称："VNetName" 和 "LNGName"
  - 虚拟网络和本地网络地址前缀
  - 正确的网络掩码

#### <a name="sample-script"></a>示例脚本

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

使用以下用于调试的 ASA 命令：

* 显示 IPsec 或 IKE 安全关联 (SA)：
    ```
    show crypto ipsec sa
    show crypto ikev2 sa
    ```

* 进入“调试模式”：
    ```
    debug crypto ikev2 platform <level>
    debug crypto ikev2 protocol <level>
    ```
    `debug` 命令可在控制台上生成大量输出。

* 在设备上显示当前配置：
    ```
    show run
    ```
    使用 `show` 子命令列出设备配置的特定部分，例如：
    ```
    show run crypto
    show run access-list
    show run tunnel-group
    ```

## <a name="next-steps"></a>后续步骤
若要配置主动-主动跨界和 VNet 到 VNet 连接，请参阅[配置主动-主动 VPN 网关](vpn-gateway-activeactive-rm-powershell.md)。
