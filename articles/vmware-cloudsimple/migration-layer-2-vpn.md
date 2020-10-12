---
title: Azure VMware Solution by CloudSimple - 将本地第 2 层网络延伸到私有云
description: 介绍如何在 CloudSimple 私有云上的 NSX 和本地的独立 NSX Edge 客户端之间设置第 2 层 VPN
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a530a6f656f37657a198af85d93d5404ac88d0e1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83651028"
---
# <a name="migrate-workloads-using-layer-2-stretched-networks"></a>使用第 2 层外延式网络迁移工作负荷

本指南将介绍如何使用第 2 层 VPN (L2VPN) 将第 2 层网络从本地环境延伸到 CloudSimple 私有云。 利用此解决方案，可在本地 VMware 环境中运行的工作负载迁移到同一子网地址空间内的 Azure 中的私有云，而无需重新对工作负载设置 IP。

基于 L2VPN 延伸第 2 层网络可在本地 VMware 网络中有或没有基于 NSX 的网络的情况下实现。 如果本地没有用于工作负载的基于 NSX 的网络，则可以使用独立 NSX Edge 服务网关。

> [!NOTE]
> 本指南介绍通过站点到站点 VPN 连接本地和私有云数据中心的方案。

## <a name="deployment-scenario"></a>部署方案

要使用 L2VPN 延伸本地网络，必须配置 L2VPN 服务器（目标 NSX-T Tier0 路由器）和 L2VPN 客户端（源独立客户端）。  

在此部署方案中，私有云通过站点到站点 VPN 隧道连接到本地环境，该隧道允许本地管理和 vMotion 子网与私有云管理和 vMotion 子网通信。 这种安排对于跨 vCenter vMotion (xVC-vMotion) 是必需的。 NSX-T Tier0 路由器作为 L2VPN 服务器部署在私有云中。

独立 NSX Edge 作为 L2VPN 客户端部署在本地环境中，并随后与 L2VPN 服务器配对。 将在各端创建 GRE 隧道终结点，并将其配置为将本地第 2 层网络“延伸”到私有云。 下图演示了这一配置。

![部署方案](media/l2vpn-deployment-scenario.png)

若要详细了解使用 L2 VPN 进行迁移的信息，请参阅 VMware 文档中的[虚拟专用网](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704)。

## <a name="prerequisites-for-deploying-the-solution"></a>部署该解决方案的先决条件

在部署和配置该解决方案前，请确认已满足以下先决条件：

* 本地 vSphere 版本为 6.7U1+ 或 6.5P03+。
* 本地 vSphere 许可证位于 Enterprise Plus 级别（用于 vSphere 分布式交换机）。
* 标识要延伸到私有云的工作负载第 2 层网络。
* 标识本地环境中用于部署 L2VPN 客户端设备的第 2 层网络。
* [已创建私有云](create-private-cloud.md)。
* 独立 NSX-T Edge 设备的版本与私有云环境中使用的 NSX-T Manager 版本 (NSX-T 2.3.0) 兼容。
* 已在本地 vCenter 中创建了已启用伪造传输的 trunk 端口组。
* 已保留一个公共 IP 地址用于 NSX-T 独立客户端上行链路 IP 地址，并且已有 1:1 NAT 可用于在两个地址之间进行转换。
* 已将 az.cloudsimple.io 域的本地 DNS 服务器上的 DNS 转发设置为指向私有云 DNS 服务器。
* RTT 延迟小于或等于 150 ms，需要满足此条件 vMotion 才能跨两个站点工作。

## <a name="limitations-and-considerations"></a>限制和注意事项

下表列出了支持的 vSphere 版本和网络适配器类型。  

| vSphere 版本 | 源 vSwitch 类型 | 虚拟 NIC 驱动程序 | 目标 vSwitch 类型 | 支持？ |
------------ | ------------- | ------------ | ------------- | ------------- 
| All | DVS | All | DVS | 是 |
| vSphere 6.7UI 或更高版本、6.5P03 或更高版本 | DVS | VMXNET3 | N-VDS | 是 |
| vSphere 6.7UI 或更高版本、6.5P03 或更高版本 | DVS | E1000 | N-VDS | [不受特定 VWware 的支持](https://kb.vmware.com/s/article/56991) |
| vSphere 6.7UI 或 6.5P03、NSX-V 或低于 NSX-T2.2 的版本、6.5P03 或更高版本 | All | All | N-VDS | [不受特定 VWware 的支持](https://kb.vmware.com/s/article/56991) |

从 VMware NSX-T 2.3 版本起：

* 不能同时路由私有云端上通过 L2VPN 延伸到本地的逻辑交换机。 延伸的逻辑交换机不能连接到逻辑路由器。
* 仅可使用 API 调用配置 L2VPN 和基于路由的 IPSEC VPN。

有关详细信息，请参阅 VMware 文档中的[虚拟专用网](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704)。

### <a name="sample-l2-vpn-deployment-addressing"></a>L2 VPN 部署寻址示例

### <a name="on-premises-network-where-the-standalone-esg-l2-vpn-client-is-deployed"></a>部署独立 ESG（L2 VPN 客户端）的本地网络

| **项** | **值** |
|------------|-----------------|
| 网络名称 | MGMT_NET_VLAN469 |
| VLAN | 469 |
| CIDR| 10.250.0.0/24 |
| 独立 Edge 设备 IP 地址 | 10.250.0.111 |
| 独立 Edge 设备 NAT IP 地址 | 192.227.85.167 |

### <a name="on-premises-network-to-be-stretched"></a>要延伸的本地网络

| **项** | **值** |
|------------|-----------------|
| VLAN | 472 |
| CIDR| 10.250.3.0/24 |

### <a name="private-cloud-ip-schema-for-nsx-t-tier0-router-l2-vpn-serve"></a>用于 NSX-T Tier0 路由器的私有云 IP 架构（L2 VPN 服务器）

| **项** | **值** |
|------------|-----------------|
| 环回接口 | 192.168.254.254/32 |
| 隧道接口 | 5.5.5.1/29 |
| 逻辑交换机（已延伸） | Stretch_LS |
| 环回接口（NAT IP 地址） | 104.40.21.81 |

### <a name="private-cloud-network-to-be-mapped-to-the-stretched-network"></a>要映射到延伸网络的私有云网络

| **项** | **值** |
|------------|-----------------|
| VLAN | 712 |
| CIDR| 10.200.15.0/24 |

## <a name="fetch-the-logical-router-id-needed-for-l2vpn"></a>获取 L2VPN 所需的逻辑路由器 ID

以下步骤演示了如何获取 IPsec 和 L2VPN 服务的 Tier0 DR 逻辑路由器实例的逻辑路由器 ID。 稍后在实现 L2VPN 时需要该逻辑路由器 ID。

1. 登录 NSX-T Manager `https://*nsx-t-manager-ip-address*`，选择“网络” > “路由器” > “Provider-LR” > “概述”   。 对于“高可用性模式”，请选择“主动-待机” 。 此操作将打开弹出窗口，其中显示 Tier0 路由器当前处于活跃状态的 Edge VM。

    ![选择“主动-待机”](media/l2vpn-fetch01.png)

2. 选择“Fabric” > “节点” > “Edge”  。 记下在上一步中标识的活跃 Edge VM (Edge VM1) 的管理 IP 地址。

    ![记下管理 IP](media/l2vpn-fetch02.png)

3. 打开与 Edge VM 的管理 IP 地址的 SSH 会话。 以用户名“admin”和密码“CloudSimple 123!”运行 ```get logical-router``` 命令 。

    ![获取逻辑路由器输出](media/l2vpn-fetch03.png)

4. 如果未看到“DR-Provider-LR”条目，请完成以下步骤。

5. 创建两个支持覆盖的逻辑交换机。 一个逻辑交换机延伸到迁移的工作负载所在的本地位置。 另一个逻辑交换机为虚拟交换机。 有关说明，请参阅 VMware 文档中的[创建逻辑交换机](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-23194F9A-416A-40EA-B9F7-346B391C3EF8.html)。

    ![创建逻辑交换机](media/l2vpn-fetch04.png)

6. 使用链接本地 IP 地址或本地或私有云中的任何非重叠子网将虚拟交换机附加到 Tier1 路由器。 请参阅 VMware 文档中的[在第 1 层逻辑路由器上添加下行链路端口](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-E7EA867C-604C-4224-B61D-2A8EF41CB7A6.html)。

    ![附加虚拟交换机](media/l2vpn-fetch05.png)

7. 再次对 Edge VM 的 SSH 会话运行 `get logical-router` 命令。 “DR-Provider-LR”逻辑路由器的 UUID 将随之显示。 记下 UUID，在配置 L2VPN 时需要使用。

    ![获取逻辑路由器输出](media/l2vpn-fetch06.png)

## <a name="fetch-the-logical-switch-id-needed-for-l2vpn"></a>获取 L2VPN 所需的逻辑交换机 ID

1. 登录 NSX-T Manager(`https://nsx-t-manager-ip-address`)。
2. 选择“网络” > “切换” > “交换机” > “<\Logical switch”\> > “概述”    。
3. 记下延伸逻辑交换机的 UUID，在配置 L2VPN 时需要使用。

    ![获取逻辑路由器输出](media/l2vpn-fetch-switch01.png)

## <a name="routing-and-security-considerations-for-l2vpn"></a>L2VPN 的路由和安全注意事项

若要在 NSX-T Tier0 路由器与独立 NSX Edge 客户端之间建立基于 IPsec 路由的 VPN，则 NSX-T Tier0 路由器的环回接口必须能够通过 UDP 500/4500 与本地 NSX 独立客户端的公共 IP 地址进行通信。

### <a name="allow-udp-5004500-for-ipsec"></a>允许将 UDP 500/4500 用于 IPsec

1. 在 CloudSimple 门户中为 NSX-T Tier0 环回接口[创建公共 IP 地址](public-ips.md)。

2. [创建防火墙表](firewall.md)，其中包含允许 UDP 500/4500 入站流量的有状态规则，并将防火墙表附加到 NSX-T HostTransport 子网。

### <a name="advertise-the-loopback-interface-ip-to-the-underlay-network"></a>将环回接口 IP 播发到基础网络

1. 为环回接口网络创建 NULL 路由。 登录 NSX-T Manager，选择“网络” > “路由” > “路由器” > “Provider-LR” > “路由” > “静态路由”     。 单击“添加”。 对于“网络”，请输入环回接口 IP 地址。 对于“下一个跃点”，单击“添加”，指定“NULL”作为下一个跃点，并将“管理距离”保留为默认值 1 。

    ![添加静态路由](media/l2vpn-routing-security01.png)

2. 创建 IP 前缀列表。 登录 NSX-T Manager，选择“网络” > “路由” > “路由器” > “Provider-LR” > “路由” > “IP 前缀列表”     。 单击“添加”。 输入名称以标识列表。 对于“前缀”，请单击“添加”两次 。 在第一行，对“网络”输入“0.0.0.0/0”，对“操作”输入“拒绝” 。 在第二行，对“网络”选择“任何”，对“操作”选择“允许”   。
3. 将 IP 前缀列表附加到两个 BGP 邻居 (TOR)。 将 IP 前缀列表附加到 BGP 邻居可防止将 BGP 中的默认路由播发到 TOR 交换机。 但是，包含 NULL 路由的任何其他路由都将向 TOR 交换机播发环回接口 IP 地址。

    ![创建 IP 前缀列表](media/l2vpn-routing-security02.png)

4. 登录 NSX-T Manager，选择“网络” > “路由” > “路由器” > “Provider-LR” > “路由” > “BGP” > “邻居”      。 选择第一个邻居。 单击“编辑” > “地址系列” 。 对于 IPv4 系列，请编辑“Out Filter”列，然后选择所创建的 IP 前缀列表。 单击“ **保存**”。 对第二个邻居重复此步骤。

    ![附加 IP 前缀列表 1](media/l2vpn-routing-security03.png) ![附加 IP 前缀列表2](media/l2vpn-routing-security04.png)

5. 将 NULL 静态路由重新分发到 BGP。 若要将环回接口路由播发到基础网络，必须将 NULL 静态路由重新分发到 BGP。 登录 NSX-T Manager，选择“网络” > “路由” > “路由器” > “Provider-LR” > “路由” > “路由重新分发” > “邻居”      。 选择“Provider-LR-Route_Redistribution”，然后单击“编辑” 。 选中“静态”复选框，然后单击“保存” 。

    ![将 NULL 静态路由重新分发到 BGP](media/l2vpn-routing-security05.png)

## <a name="configure-a-route-based-vpn-on-the-nsx-t-tier0-router"></a>在 NSX-T Tier0 路由器上配置基于路由的 VPN

使用以下模板填写在 NSX-T Tier0 路由器上配置基于路由的 VPN 的所有详细信息。 后续的 POST 调用中需要每个 POST 调用中的 UUID。 L2VPN 的环回和隧道接口的 IP 地址必须唯一，且不能与本地或私有云网络重叠。

为用于 L2VPN 的环回和隧道接口选择的 IP 地址必须唯一，且不能与本地或私有云网络重叠。 环回接口网络必须始终为 /32。

```
Loopback interface ip : 192.168.254.254/32
Tunnel interface subnet : 5.5.5.0/29
Logical-router ID : UUID of Tier0 DR logical router obtained in section "Steps to fetch Logical-Router ID needed for L2VPN"
Logical-switch ID(Stretch) : UUID of Stretch Logical Switch obtained earlier
IPSec Service ID :
IKE profile ID :
DPD profile ID :
Tunnel Profile ID :
Local-endpoint ID :
Peer end-point ID :
IPSec VPN session ID (route-based) :
L2VPN service ID :
L2VPN session ID :
Logical-Port ID :
Peer Code :
```

对于以下所有 API 调用，请将 IP 地址替换为你的 NSX-T Manager IP 地址。 可以从 POSTMAN 客户端或使用 `curl` 命令来运行所有这些 API 调用。

### <a name="enable-the-ipsec-vpn-service-on-the-logical-router"></a>在逻辑路由器上启用 IPSec VPN 服务

```
POST   https://192.168.110.201/api/v1/vpn/ipsec/services/
{
"resource_type": "IPSecVPNService",
"description": "Manage VPN service",
"display_name": "IPSec VPN service",
"logical_router_id": "Logical-router ID",
"ike_log_level": "INFO",
"enabled": true
}
```

### <a name="create-profiles-ike"></a>创建配置文件：IKE

```
POST https://192.168.110.201/api/v1/vpn/ipsec/ike-profiles
 
{
"resource_type": "IPSecVPNIKEProfile",
"description": "IKEProfile for siteA",
"display_name": "IKEProfile siteA",
"encryption_algorithms": ["AES_128"],
"ike_version": "IKE_V2",
"digest_algorithms": ["SHA2_256"],
"sa_life_time":21600,
"dh_groups": ["GROUP14"]
}
```

### <a name="create-profiles-dpd"></a>创建配置文件：DPD

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/dpd-profiles  

{
"resource_type": "IPSecVPNDPDProfile",
"display_name": "nsx-default-dpd-profile",
"enabled": true
}
```

### <a name="create-profiles-tunnel"></a>创建配置文件：隧道

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/tunnel-profiles
 
{
"resource_type": "IPSecVPNTunnelProfile",
"display_name": "nsx-default-tunnel-profile",
"enable_perfect_forward_secrecy": true,
"encryption_algorithms": ["AES_GCM_128"],
"digest_algorithms": [],
"sa_life_time":3600,
"dh_groups": ["GROUP14"],
"encapsulation_mode": "TUNNEL_MODE",
"transform_protocol": "ESP",
"df_policy": "COPY"
}
```

### <a name="create-a-local-endpoint"></a>创建本地终结点

``` 
POST https://192.168.110.201/api/v1/vpn/ipsec/local-endpoints
 
{
"resource_type": "IPSecVPNLocalEndpoint",
"description": "Local endpoint",
"display_name": "Local endpoint",
"local_id": "<Public IP of Loopback interface>",
"ipsec_vpn_service_id": {
"target_id": "IPSec VPN service ID"},
"local_address": "<IP of Loopback interface>",
"trust_ca_ids": [],
"trust_crl_ids": []
}
```

### <a name="create-a-peer-endpoint"></a>创建对等终结点

```
POST https://192.168.110.201/api/v1/vpn/ipsec/peer-endpoints

{
"resource_type": "IPSecVPNPeerEndpoint",
"description": "Peer endpoint for site B",
"display_name": "Peer endpoint for site B",
"connection_initiation_mode": "INITIATOR",
"authentication_mode": "PSK",
"ipsec_tunnel_profile_id": "IPSec Tunnel profile ID",
"dpd_profile_id": "DPD profile ID",
"psk":"nsx",
"ike_profile_id": "IKE profile ID",
"peer_address": "<Public IP of Standalone client",
"peer_id": "<Public IP of Standalone client>"
}
```

### <a name="create-a-route-based-vpn-session"></a>创建基于路由的 VPN 会话

```
POST :  https://192.168.110.201/api/v1/vpn/ipsec/sessions
 
{
"resource_type": "RouteBasedIPSecVPNSession",
"peer_endpoint_id": "Peer Endpoint ID",
"ipsec_vpn_service_id": "IPSec VPN service ID",
"local_endpoint_id": "Local Endpoint ID",
"enabled": true,
"tunnel_ports": [
{
"ip_subnets": [
{
"ip_addresses": [
 "5.5.5.1"
],
"prefix_length": 24
}
  ]
}
]
}
```

## <a name="configure-l2vpn-on-nsx-t-tier0-router"></a>在 NSX-T Tier0 路由器上配置 L2VPN

在每次 POST 调用后填充以下信息。 后续的 POST 调用中需要这些 ID。

```
L2VPN Service ID:
L2VPN Session ID:
Logical Port ID:
```

### <a name="create-the-l2vpn-service"></a>创建 L2VPN 服务

以下 GET 命令的输出将为空，因为配置尚未完成。

```
GET : https://192.168.110.201/api/v1/vpn/l2vpn/services
```

对于以下 POST 命令，逻辑路由器 ID 是之前获得的 Tier0 DR 逻辑路由器的 UUID。

```
POST : https://192.168.110.201/api/v1/vpn/l2vpn/services

{
"logical_router_id": "Logical Router ID",
"enable_full_mesh" : true
}
```

### <a name="create-the-l2vpn-session"></a>创建 L2VPN 会话

对于以下 POST 命令，L2VPN 服务 ID 是刚刚获得的 ID，IPsec VPN 会话 ID 是在上一节中获得的 ID。

```    
POST: https://192.168.110.201/api/v1/vpn/l2vpn/sessions

{
"l2vpn_service_id" : "L2VPN service ID",
"transport_tunnels" : [
    {
    "target_id" : "IPSec VPN session ID"
    }]
}
```

这些调用会创建一个 GRE 隧道终结点。 要查看状态，请运行下面的命令。

```
edge-2> get tunnel-port
Tunnel      : 44648dae-8566-5bc9-a065-b1c4e5c3e03f
IFUID       : 328
LOCAL       : 169.254.64.1
REMOTE      : 169.254.64.2
ENCAP       : GRE

Tunnel      : cf950ca1-5cf8-5438-9b1a-d2c8c8e7229b
IFUID       : 318
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.152
ENCAP       : GENEVE

Tunnel      : 63639321-87c5-529e-8a61-92c1939799b2
IFUID       : 304
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.156
ENCAP       : GENEVE
```

### <a name="create-logical-port-with-the-tunnel-id-specified"></a>创建已指定隧道 ID 的逻辑端口

```
    POST https://192.168.110.201/api/v1/logical-ports/

{
"resource_type": "LogicalPort",
"display_name": "Extend logicalSwitch, port for service",
"logical_switch_id": "Logical switch ID",
"admin_state" : "UP",
"attachment": {
"attachment_type":"L2VPN_SESSION",
"id":"L2VPN session ID",
"context" : {
"resource_type" : "L2VpnAttachmentContext",
    "tunnel_id" : 10
}
    }
        }
```

## <a name="obtain-the-peer-code-for-l2vpn-on-the-nsx-t-side"></a>获取 NSX 端的 L2VPN 的对等代码

获取 NSX-T 终结点的对等代码。 配置远程终结点时需要该对等代码。 可以从上一节中获取 L2VPN <session-id>。 有关详细信息，请参阅 [NSX-T 2.3 API 指南](https://www.vmware.com/support/nsxt/doc/nsxt_23_api.html)。

```
GET https://192.168.110.201/api/v1/vpn/l2vpn/sessions/<session-id>/peer-codes
```

## <a name="deploy-the-nsx-t-standalone-client-on-premises"></a>部署 NSX-T 独立客户端（本地）

部署前，请确认本地防火墙规则允许来自/传入 CloudSimple 公共 IP 地址（之前已为 NSX-T T0 路由器环回接口保留该地址）的入站和出站 UDP 500/4500 流量。 

1. [下载独立 NSX Edge 客户端](https://my.vmware.com/group/vmware/details?productId=673&rPId=33945&downloadGroup=NSX-T-230) OVF，并将下载的捆绑包中的文件提取到文件夹中。

    ![下载独立 NSX Edge 客户端](media/l2vpn-deploy-client01.png)

2. 前往包含所有提取文件的文件夹。 选择所有 vmdk（对大型设备选择 NSX-l2t-client-large.mf 和 NSX-l2t-client-large.ovf；对超大型设备选择 NSX-l2t-client-Xlarge.mf 和 NSX-l2t-client-Xlarge.ovf）。 单击“下一步”。

    ![选择模板](media/l2vpn-deploy-client02.png) ![选择模板](media/l2vpn-deploy-client03.png)

3. 输入 NSX-T 独立客户端的名称，然后单击“下一步”。

    ![输入模板名称](media/l2vpn-deploy-client04.png)

4. 单击“下一步”以访问数据存储设置。 选择适用于 NSX-T 独立客户端的数据存储，然后单击“下一步”。

    ![选择数据存储](media/l2vpn-deploy-client06.png)

5. 为 NSX-T 独立客户端的 Trunk 接口 (Trunk PG)、公共接口（上行链路 PG）和 HA 接口（上行链路 PG）选择正确的端口组。 单击“下一步”。

    ![选择端口组](media/l2vpn-deploy-client07.png)

6. 在“自定义模板”屏幕中填写以下详细信息，然后单击“下一步” ：

    展开 L2T：

    * 对等地址。 输入 Azure CloudSimple 门户上为 NSX-T Tier0 环回接口保留的 IP 地址。
    * 对等代码。 粘贴从 L2VPN 服务器部署的最后一步中获取的对等代码。
    * 子接口 VLAN（隧道 ID）。 输入要延伸的 VLAN ID。 在括号 () 中，输入之前配置的隧道 ID。

    展开上行链路接口：

    * DNS IP 地址。 输入本地 DNS IP 地址。
    * 默认网关。  输入 VLAN 的默认网关，该网关将充当此客户端的默认网关。
    * IP 地址。 输入独立客户端的上行链路 IP 地址。
    * 前缀长度。 输入上行链路 VLAN/子网的前缀长度。
    * CLI 管理/启用/根用户密码。 为管理/启用/根帐户设置密码。

      ![自定义模板](media/l2vpn-deploy-client08.png)
      ![自定义模板 - 更多信息](media/l2vpn-deploy-client09.png)

7. 检查设置，并单击“完成”。

    ![完成配置](media/l2vpn-deploy-client10.png)

## <a name="configure-an-on-premises-sink-port"></a>配置本地接收器端口

如果某个 VPN 站点未部署 NSX，则可以通过在该站点上部署独立 NSX Edge 来配置 L2 VPN。 将使用不由 NSX 管理的主机上的 OVF 文件部署独立 NSX Edge。 这会部署一个 NSX Edge 服务网关设备，用作 L2 VPN 客户端。

如果独立 Edge Trunk vNIC 连接到 vSphere 分布式交换机，则需要使用混杂模式或使用接收器端口才能使 L2 VPN 正常工作。 使用混杂模式可能会导致重复的 ping 和重复的响应。 出于此原因，请在 L2 VPN 独立 NSX Edge 配置中使用接收器端口模式。 请参阅 VMware 文档中的[配置接收器端口](https://docs.vmware.com/en/VMware-NSX-Data-Center-for-vSphere/6.4/com.vmware.nsx.admin.doc/GUID-3CDA4346-E692-4592-8796-ACBEEC87C161.html)。

## <a name="ipsec-vpn-and-l2vpn-verification"></a>IPsec VPN 和 L2VPN 验证

使用以下命令从独立 NSX-T Edge 验证 IPsec 和 L2VPN 会话。

```
nsx-l2t-edge> show service ipsec
-----------------------------------------------------------------------
vShield Edge IPSec Service Status:
IPSec Server is running.
AESNI is enabled.
Total Sites: 1, 1 UP, 0 Down
Total Tunnels: 1, 1 UP, 0 Down
----------------------------------
Site:  10.250.0.111_0.0.0.0/0-104.40.21.81_0.0.0.0/0
Channel: PeerIp: 104.40.21.81    LocalIP: 10.250.0.111  Version: IKEv2  Status: UP
Tunnel: PeerSubnet: 0.0.0.0/0    LocalSubnet: 0.0.0.0/0   Status: UP
----------------------------------
```

```
nsx-l2t-edge> show service l2vpn
L2 VPN is running
----------------------------------------
L2 VPN type: Client/Spoke

SITENAME                       IPSECSTATUS          VTI                  GRE
1ecb00fb-a538-4740-b788-c9049e8cb6c6 UP                   vti-100              l2t-1
```

使用以下命令从 NSX-T Tier0 路由器验证 IPsec 和 L2VPN 会话。

```
edge-2> get ipsecvpn session
Total Number of Sessions: 1

IKE Session ID : 3
UUID           : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Type           : Route

Local IP       : 192.168.254.254      Peer IP        : 192.227.85.167
Local ID       : 104.40.21.81         Peer ID        : 192.227.85.167
Session Status : Up

Policy Rules
    VTI UUID       : 4bf96e3b-e50b-49cc-a16e-43a6390e3d53
    ToRule ID      : 560874406            FromRule ID    : 2708358054
    Local Subnet   : 0.0.0.0/0            Peer Subnet    : 0.0.0.0/0
    Tunnel Status  : Up
```

```
edge-2> get l2vpn session
Session       : f7147137-5dd0-47fe-9e53-fdc2b687b160
Tunnel        : b026095b-98c8-5932-96ff-dda922ffe316
IPSEC Session : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Status        : UP
```

在本地环境中，使用以下命令验证 NSX-T 独立客户端 VM 所在的 ESXi 主机上的接收器端口。

```
 [root@esxi02:~] esxcfg-vswitch -l |grep NSX
  53                  1           NSXT-client-large.eth2
  225                1           NSXT-client-large.eth1
  52                  1           NSXT-client-large.eth0
```

```
[root@esxi02:~] net-dvs -l | grep "port\ [0-9]\|SINK\|com.vmware.common.alias"
                com.vmware.common.alias = csmlab2DS ,   propType = CONFIG
        port 24:
        port 25:
        port 26:
        port 27:
        port 13:
        port 19:
        port 169:
        port 54:
        port 110:
        port 6:
        port 107:
        port 4:
        port 199:
        port 168:
        port 201:
        port 0:
        port 49:
        port 53:
        port 225:
                com.vmware.etherswitch.port.extraEthFRP =   SINK
        port 52:
```
