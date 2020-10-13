---
title: Azure ExpressRoute：路由器配置示例
description: 如要使用 Azure ExpressRoute，请使用适用于 Cisco IOS 的这些接口和路由配置示例。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 03/26/2020
ms.author: duau
ms.openlocfilehash: 3bc850f02884ae0547c2ecf56a46a57a4e66a752
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89397400"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>用于设置和管理路由的路由器配置示例
本页提供处理 Azure ExpressRoute 时适用于 Cisco IOS-XE 和 Juniper MX 系列路由器的接口与路由配置示例。

> [!IMPORTANT]
> 本页中的示例仅供指导。 必须与供应商的销售/技术团队和网络团队合作，以便找到符合需要的适当配置。 对于本页中所列配置的相关问题，Microsoft 将不提供支持。 有关支持问题，请与设备供应商联系。
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>路由器接口上的 MTU 和 TCP MSS 设置
ExpressRoute 接口的最大传输单元 (MTU) 为 1500，即路由器上以太网接口的典型默认 MTU。 默认情况下，除非路由器具有不同 MTU，否则无需在路由器接口上指定值。

与 Azure VPN 网关不同，不需要为 ExpressRoute 线路指定 TCP 最大段大小 (MSS)。

本文中的路由器配置示例适用于所有对等互连。 有关路由的更多详细信息，请查看 [ExpressRoute 对等互连](expressroute-circuit-peerings.md)和 [ExpressRoute 路由要求](expressroute-routing.md)。


## <a name="cisco-ios-xe-based-routers"></a>基于 Cisco IOS-XE 的路由器
本部分中的示例适用于任何运行 IOS-XE OS 系列的路由器。

### <a name="configure-interfaces-and-subinterfaces"></a>配置接口和子接口
在连接到 Microsoft 的每个路由器中，每个对等互连都需要一个子接口。 子接口可使用 VLAN ID 或一对堆叠的 VLAN ID 和 IP 地址来标识。

**Dot1Q 接口定义**

本示例针对包含单个 VLAN ID 的子接口提供子接口定义。 在每个对等互连中，VLAN ID 是唯一的。 IPv4 地址的最后一个八位字节将始终是奇数。

```console
interface GigabitEthernet<Interface_Number>.<Number>
 encapsulation dot1Q <VLAN_ID>
 ip address <IPv4_Address><Subnet_Mask>
```

**QinQ 接口定义**

本示例针对包含两个 VLAN ID 的子接口提供子接口定义。 外部 VLAN ID (s-tag)（如果使用）在所有对等互连中保持不变。 在每个对等互连中，内部 VLAN ID (c-tag) 是唯一的。 IPv4 地址的最后一个八位字节将始终是奇数。

```console
interface GigabitEthernet<Interface_Number>.<Number>
 encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
 ip address <IPv4_Address><Subnet_Mask>
```

### <a name="set-up-ebgp-sessions"></a>设置 eBGP 会话
必须针对每个对等互连设置与 Microsoft 的 BGP 会话。 使用以下示例设置 BGP 会话。 如果对子接口使用的 IPv4 地址是 a.b.c.d，则 BGP 邻居 (Microsoft) 的 IP 地址将是 a.b.c.d+1。 BGP 邻居的 IPv4 地址的最后一个八位字节将始终是偶数。

```console
router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
 neighbor <IP#2_used_by_Azure> activate
 exit-address-family
!
```

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>设置要通过 BGP 会话播发的前缀
使用以下示例，将路由器配置为将所选前缀播发给 Microsoft。

```console
router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
  network <Prefix_to_be_advertised> mask <Subnet_mask>
  neighbor <IP#2_used_by_Azure> activate
 exit-address-family
!
```

### <a name="route-maps"></a>路由映射
使用路由映射和前缀列表来筛选已传播到网络中的前缀。 请参阅下面的示例，并确保已设置适当的前缀列表。

```console
router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
  network <Prefix_to_be_advertised> mask <Subnet_mask>
  neighbor <IP#2_used_by_Azure> activate
  neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
 exit-address-family
!
route-map <MS_Prefixes_Inbound> permit 10
 match ip address prefix-list <MS_Prefixes>
!
```

### <a name="configure-bfd"></a>配置 BFD

将在两个位置配置 BFD：一个在接口级别配置，另一个在 BGP 级别配置。 此处的示例适用于 QinQ 接口。 

```console
interface GigabitEthernet<Interface_Number>.<Number>
 bfd interval 300 min_rx 300 multiplier 3
 encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
 ip address <IPv4_Address><Subnet_Mask>

router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
  neighbor <IP#2_used_by_Azure> activate
  neighbor <IP#2_used_by_Azure> fall-over bfd
 exit-address-family
!
```


## <a name="juniper-mx-series-routers"></a>Juniper MX 系列路由器
本部分中的示例适用于所有 Juniper MX 系列路由器。

### <a name="configure-interfaces-and-subinterfaces"></a>配置接口和子接口

**Dot1Q 接口定义**

本示例针对包含单个 VLAN ID 的子接口提供子接口定义。 在每个对等互连中，VLAN ID 是唯一的。 IPv4 地址的最后一个八位字节将始终是奇数。

```console
    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }
```


**QinQ 接口定义**

本示例针对包含两个 VLAN ID 的子接口提供子接口定义。 外部 VLAN ID (s-tag)（如果使用）在所有对等互连中保持不变。 在每个对等互连中，内部 VLAN ID (c-tag) 是唯一的。 IPv4 地址的最后一个八位字节将始终是奇数。

```console
    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           
```

### <a name="set-up-ebgp-sessions"></a>设置 eBGP 会话
必须针对每个对等互连设置与 Microsoft 的 BGP 会话。 使用以下示例设置 BGP 会话。 如果对子接口使用的 IPv4 地址是 a.b.c.d，则 BGP 邻居 (Microsoft) 的 IP 地址将是 a.b.c.d+1。 BGP 邻居的 IPv4 地址的最后一个八位字节将始终是偶数。

```console
    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }
```

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>设置要通过 BGP 会话播发的前缀
使用以下示例，将路由器配置为将所选前缀播发给 Microsoft。

```console
    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
                route-filter; 
                <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>;
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }
```

### <a name="route-policies"></a>路由策略
可以使用路由映射和前缀列表来筛选已传播到网络中的前缀。 请参阅下面的示例，并确保已设置适当的前缀列表。

```console
    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
                    prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>;
                import <MS_Prefixes_Inbound>;
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }
```

### <a name="configure-bfd"></a>配置 BFD
仅在协议 BGP 部分下配置 BFD。

```console
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
                bfd-liveness-detection {
                       minimum-interval 3000;
                       multiplier 3;
                }
            }                               
        }                                   
    }
```

### <a name="configure-macsec"></a>配置 MACSec
对于 MACSec 配置，连接关联密钥 (CAK) 和连接关联密钥名称 (CKN) 必须通过 PowerShell 命令与已配置的值进行匹配。

```console
    security {
        macsec {
            connectivity-association <Connectivity_Association_Name> {
                cipher-suite gcm-aes-xpn-128;
                security-mode static-cak;
                pre-shared-key {
                    ckn <Connectivity_Association_Key_Name>;
                    cak <Connectivity_Association_Key>; ## SECRET-DATA
                }
            }
            interfaces {
                <Interface_Number> {
                    connectivity-association <Connectivity_Association_Name>;
                }
            }
        }
    }
```

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md) 。



