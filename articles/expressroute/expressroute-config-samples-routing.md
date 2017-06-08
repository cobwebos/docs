---
title: "ExpressRoute 客户路由器配置示例 | Microsoft Docs"
description: "本页提供 Cisco 和 Juniper 路由器的路由器配置示例。"
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: 
ms.assetid: 564826bc-017a-4683-a385-37c9fa814948
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 032e584dc5abf59e9e3e8d80673b402f1fbf721b
ms.contentlocale: zh-cn
ms.lasthandoff: 05/15/2017


---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>用于设置和管理路由的路由器配置示例
本页提供适用于 Cisco IOS XE 和 Juniper MX 系列路由器的接口与路由配置示例。 这些示例仅供指导，不能按原样使用。 你可以与你的供应商合作，以便为你的网络指定适当的配置。 

> [!IMPORTANT]
> 本页中的示例仅供指导。 你必须与供应商的销售/技术团队和你的网络团队合作，以便指定符合需要的适当配置。 对于本页中所列配置的相关问题，Microsoft 将不提供支持。 有关支持问题，必须与设备供应商联系。
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>路由器接口上的 MTU 和 TCP MSS 设置
* ExpressRoute 接口的 MTU 为 1500，即路由器上以太网接口的典型默认 MTU。 默认情况下，除非路由器具有不同 MTU，否则无需在路由器接口上指定值。
* 与 Azure VPN 网关不同，不需要为 ExpressRoute 线路指定 TCP MSS。

以下路由器配置示例适用于所有对等互连。 有关路由的更多详细信息，请查看 [ExpressRoute 对等互连](expressroute-circuit-peerings.md)和 [ExpressRoute 路由要求](expressroute-routing.md)。


## <a name="cisco-ios-xe-based-routers"></a>基于 Cisco IOS-XE 的路由器
本部分中的示例适用于任何运行 IOS-XE OS 系列的路由器。

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1.配置接口和子接口
在连接到 Microsoft 的每个路由器中，针对每个对等互连都需要有一个子接口。 子接口可使用 VLAN ID 或一对堆叠的 VLAN ID 和 IP 地址来标识。

**Dot1Q 接口定义**

本示例针对包含单个 VLAN ID 的子接口提供子接口定义。 在每个对等互连中，VLAN ID 是唯一的。 IPv4 地址的最后一个八位字节将始终是奇数。

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**QinQ 接口定义**

本示例针对包含两个 VLAN ID 的子接口提供子接口定义。 外部 VLAN ID (s-tag)（如果使用）在所有对等互连中保持不变。 在每个对等互连中，内部 VLAN ID (c-tag) 是唯一的。 IPv4 地址的最后一个八位字节将始终是奇数。

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="2-setting-up-ebgp-sessions"></a>2.设置 eBGP 会话
必须针对每个对等互连设置与 Microsoft 的 BGP 会话。 以下示例可让你设置与 Microsoft 的 BGP 会话。 如果对子接口使用的 IPv4 地址是 a.b.c.d，则 BGP 邻居 (Microsoft) 的 IP 地址将是 a.b.c.d+1。 BGP 邻居的 IPv4 地址的最后一个八位字节将始终是偶数。

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3.设置要通过 BGP 会话播发的前缀
你可以配置路由器，以将所选前缀播发给 Microsoft。 可以使用以下示例来执行此操作。

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="4-route-maps"></a>4.路由映射
可以使用路由映射和前缀列表来筛选已传播到网络中的前缀。 可以使用以下示例来完成此任务。 确保已设置适当的前缀列表。

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


## <a name="juniper-mx-series-routers"></a>Juniper MX 系列路由器
本部分中的示例适用于所有 Juniper MX 系列路由器。

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1.配置接口和子接口

**Dot1Q 接口定义**

本示例针对包含单个 VLAN ID 的子接口提供子接口定义。 在每个对等互连中，VLAN ID 是唯一的。 IPv4 地址的最后一个八位字节将始终是奇数。

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


**QinQ 接口定义**

本示例针对包含两个 VLAN ID 的子接口提供子接口定义。 外部 VLAN ID (s-tag)（如果使用）在所有对等互连中保持不变。 在每个对等互连中，内部 VLAN ID (c-tag) 是唯一的。 IPv4 地址的最后一个八位字节将始终是奇数。

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

### <a name="2-setting-up-ebgp-sessions"></a>2.设置 eBGP 会话
必须针对每个对等互连设置与 Microsoft 的 BGP 会话。 以下示例可让你设置与 Microsoft 的 BGP 会话。 如果对子接口使用的 IPv4 地址是 a.b.c.d，则 BGP 邻居 (Microsoft) 的 IP 地址将是 a.b.c.d+1。 BGP 邻居的 IPv4 地址的最后一个八位字节将始终是偶数。

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

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3.设置要通过 BGP 会话播发的前缀
你可以配置路由器，以将所选前缀播发给 Microsoft。 可以使用以下示例来执行此操作。

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }


### <a name="4-route-maps"></a>4.路由映射
可以使用路由映射和前缀列表来筛选已传播到网络中的前缀。 可以使用以下示例来完成此任务。 确保已设置适当的前缀列表。

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
                export <Policy_Name>
                import <MS_Prefixes_Inbound>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md) 。


