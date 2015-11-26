<properties
   pageTitle="ExpressRoute 客户路由器配置示例 | Microsoft Azure"
   description="本页提供 Cisco 和 Juniper 路由器的路由器配置示例。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor="" />
<tags
   ms.service="expressroute"
   ms.date="10/12/2015"
   wacn.date=""/>

# 用于设置和管理 NAT 的路由器配置示例

本页提供适用于 Cisco ASA 和 Juniper MX 系列路由器的 NAT 配置示例。这些示例仅供指导，不能按原样使用。你可以与你的供应商合作，以便为你的网络指定适当的配置。

>[AZURE.IMPORTANT]本页中的示例仅供指导。你必须与供应商的销售/技术团队和你的网络团队合作，以便指定符合需要的适当配置。对于本页中所列配置的相关问题，Microsoft 将不提供支持。出现问题时，你必须联系设备供应商来获得支持。

以下路由器配置示例适用于 Azure 公共与 Microsoft 对等互连。不得为 Azure 专用对等互连配置 NAT。有关详细信息，请查看 [ExpressRoute 对等互连](/documentation/articles/expressroute-circuit-peerings)和 [ExpressRoute NAT 要求](/documentation/articles/expressroute-nat)。

**注意：**必须使用独立的 NAT IP 池来连接到 Internet 和 ExpressRoute。在 Internet 与 ExpressRoute 中使用相同的 NAT IP 池会导致非对称路由和连接断开。

## Cisco ASA 防火墙

### 从客户网络到 Microsoft 的流量的 PAT 配置

    object network MSFT-PAT
      range <SNAT-START-IP> <SNAT-END-IP>
    
    
    object-group network MSFT-Range
      network-object <IP> <Subnet_Mask>
    
    object-group network on-prem-range-1
      network-object <IP> <Subnet_Mask>
    
    object-group network on-prem-range-2
      network-object <IP> <Subnet_Mask>
    
    object-group network on-prem
      network-object object on-prem-range-1
      network-object object on-prem-range-2
    
    nat (outside,inside) source dynamic on-prem pat-pool MSFT-PAT destination static MSFT-Range MSFT-Range



## Juniper MX 系列路由器 

### 1\.为群集创建冗余的以太网接口

	interfaces {
	    reth0 {
	        description "To Internal Network";
	        vlan-tagging;
	        redundant-ether-options {
	            redundancy-group 1;
	        }
	        unit 100 {
	            vlan-id 100;
	            family inet {
	                address <IP_Address/Subnet_mask>;
	            }
	        }
	    }
	    reth1 {
	        description "To Microsoft via Edge Router";
	        vlan-tagging;
	        redundant-ether-options {
	            redundancy-group 2;
	        }
	        unit 100 {
	            description "To Microsoft via Edge Router";
	            vlan-id 100;
	            family inet {
	                address <IP_Address/Subnet_mask>;
	            }
	        }
	    }
	}


### 2\.创建两个安全区域

 - 内部网络的信任区域和面向外部网络的边缘路由器的非信任区域
 - 将适当的接口分配到区域
 - 在接口上允许服务


	security {   
	 zones {
	        security-zone Trust {
	            host-inbound-traffic {
	                system-services {
	                    ping;
	                }
	                protocols {
	                    bgp;
	                }
	            }
	            interfaces {
	                reth0.100;
	            }
	        }
	        security-zone Untrust {
	            host-inbound-traffic {
	                system-services {
	                    ping;
	                }
	                protocols {
	                    bgp;
	                }
	            }
	            interfaces {
	                reth1.100;
	            }
	        }
	    }
	}


### 3\.在区域之间创建安全策略
 
	security {
	    policies {
	        from-zone Trust to-zone Untrust {
	            policy allow-any {
	                match {
	                    source-address any;
	                    destination-address any;
	                    application any;
	                }
	                then {
	                    permit;
	                }
	            }
	        }
	        from-zone Untrust to-zone Trust {
	            policy allow-any {
	                match {
	                    source-address any;
	                    destination-address any;
	                    application any;
	                }
	                then {
	                    permit;
	                }
	            }
	        }
	    }
	}


### 4\.配置 NAT 策略
 - 创建两个 NAT 池一个用于通过 NAT 转换出站到 Microsoft 的流量，另一个用于从 Microsoft 发往客户的流量。
 - 创建规则以通过 NAT 转换相应的流量

		security {
		    nat {
		        source {
		            pool SNAT_To_ExpressRoute {
		                routing-instance {
		                    External_ExpressRoute;
		                }
		                address {
		                    <NAT_IP_address/Subnet_mask>;
		                }
		            }
		            pool SNAT_From_ExpressRoute {
		                routing-instance {
		                    Internal;
		                }
		                address {
		                    <NAT_IP_address/Subnet_mask>;
		                }
		            }
		            rule-set Outbound_NAT {
		                from routing-instance Internal;
		                to routing-instance External_ExpressRoute;
		                rule SNAT_Out {
		                    match {
		                        source-address 0.0.0.0/0;
		                    }
		                    then {
		                        source-nat {
		                            pool {
		                                SNAT_To_ExpressRoute;
		                            }
		                        }
		                    }
		                }
		            }
		            rule-set Inbound_NAT {
		                from routing-instance External_ExpressRoute;
		                to routing-instance Internal;
		                rule SNAT_In {
		                    match {
		                        source-address 0.0.0.0/0;
		                    }
		                    then {
		                        source-nat {
		                            pool {
		                                SNAT_From_ExpressRoute;
		                            }
		                        }
		                    }
		                }
		            }
		        }
		    }
		}


### 5\.配置 BGP 以朝每个方向播发选择的前缀

请参阅[路由配置示例](/documentation/articles/expressroute-config-samples-routing)页中的示例。

### 6\.创建策略

	routing-options {
	    	      autonomous-system <Customer_ASN>;
	}
	policy-options {
	    prefix-list Microsoft_Prefixes {
	        <IP_Address/Subnet_Mask;
	        <IP_Address/Subnet_Mask;
	    }
	    prefix-list private-ranges {
	        10.0.0.0/8;
	        172.16.0.0/12;
	        192.168.0.0/16;
	        100.64.0.0/10;
	    }
	    policy-statement Advertise_NAT_Pools {
	        from {
	            protocol static;
	            route-filter <NAT_Pool_Address/Subnet_mask> prefix-length-range /32-/32;
	        }
	        then accept;
	    }
	    policy-statement Accept_from_Microsoft {
	        term 1 {
	            from {
	                instance External_ExpressRoute;
	                prefix-list-filter Microsoft_Prefixes orlonger;
	            }
	            then accept;
	        }
	        term deny {
	            then reject;
	        }
	    }
	    policy-statement Accept_from_Internal {
	        term no-private {
	            from {
	                instance Internal;
	                prefix-list-filter private-ranges orlonger;
	            }
	            then reject;
	        }
	        term bgp {
	            from {
	                instance Internal;
	                protocol bgp;
	            }
	            then accept;
	        }
	        term deny {
	            then reject;
	        }
	    }
	}
	routing-instances {
	    Internal {
	        instance-type virtual-router;
	        interface reth0.100;
	        routing-options {
	            static {
	                route <NAT_Pool_IP_Address/Subnet_mask> discard;
	            }
	            instance-import Accept_from_Microsoft;
	        }
	        protocols {
	            bgp {
	                group customer {
	                    export <Advertise_NAT_Pools>;
	                    peer-as <Customer_ASN_1>;
	                    neighbor <BGP_Neighbor_IP_Address>;
	                }
	            }
	        }
	    }
	    External_ExpressRoute {
	        instance-type virtual-router;
	        interface reth1.100;
	        routing-options {
	            static {
	                route <NAT_Pool_IP_Address/Subnet_mask> discard;
	            }
	            instance-import Accept_from_Internal;
	        }
	        protocols {
	            bgp {
	                group edge_router {
	                    export <Advertise_NAT_Pools>;
	                    peer-as <Customer_Public_ASN>;
	                    neighbor <BGP_Neighbor_IP_Address>;
	                }
	            }
	        }
	    }
	}

## 后续步骤

有关详细信息，请参阅 [ExpressRoute 常见问题](/documentation/articles/expressroute-faqs)。

<!---HONumber=82-->