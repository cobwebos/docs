---
title: 使用 ExpressRoute 将 Azure Stack 连接到 Azure
description: 如何使用 ExpressRoute 将 Azure Stack 中的虚拟网络连接到 Azure 中的虚拟网络。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 544fc1bcc9212fd38938d58447f5050df2a08796
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
ms.locfileid: "29844915"
---
# <a name="connect-azure-stack-to-azure-using-expressroute"></a>使用 ExpressRoute 将 Azure Stack 连接到 Azure

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

支持使用两种方法将 Azure Stack 中的虚拟网络连接到 Azure 中的虚拟网络：
   * **站点到站点**

     基于 IPsec（IKE v1 和 IKE v2）的 VPN 连接。 此类型的连接需要 VPN 设备或 RRAS。 有关详细信息，请参阅[使用 VPN 将 Azure Stack 连接到 Azure](azure-stack-connect-vpn.md)。
   * **ExpressRoute**

     从 Azure Stack 部署直接连接到 Azure。 ExpressRoute **不是**通过公共 Internet 的 VPN 连接。 有关 Azure ExpressRoute 的详细信息，请参阅 [ExpressRoute 概述](../expressroute/expressroute-introduction.md)。

本文演示如何使用 ExpressRoute 将 Azure Stack 连接到 Azure。
## <a name="requirements"></a>要求
使用 ExpressRoute 连接 Azure Stack 和 Azure 的具体要求如下：
* 一个用于在 Azure 中创建 ExpressRoute 线路和 VNet 的 Azure 订阅。
* 通过[连接提供商](../expressroute/expressroute-locations.md)预配的 ExpressRoute 线路。
* 一个路由器，ExpressRoute 线路已连接到其 WAN 端口。
* 路由器的 LAN 端已链接到 Azure Stack 多租户网关。
* 路由器在其 LAN 接口与 Azure Stack 多租户共享网关之间，必须支持站点到站点 VPN 连接。
* 如果 Azure Stack 部署中添加了多个租户，则路由器必须能够创建多个 VRF（虚拟路由和转发）。

下图显示了完成配置后的网络概念视图：

![概念图](media/azure-stack-connect-expressroute/Conceptual.png)

**图示 1**

以下体系结构图显示多个租户如何从 Azure Stack 基础结构通过 ExpressRoute 路由器连接到位于 Microsoft 边缘的 Azure：

![体系结构关系图](media/azure-stack-connect-expressroute/Architecture.png)

**图示 2**

本文中演示的示例使用相同的体系结构通过 ExpressRoute 专用对等互连连接到 Azure。 做法是使用站点到站点 VPN 连接从 Azure Stack 中的虚拟网络网关连接到 ExpressRoute 路由器。 本文中的以下步骤说明如何在两个 VNet 之间创建端到端连接：从 Azure Stack 中的不同租户连接到它们在 Azure 中的相应 VNet。 可以选择添加任意数目的租户 VNet，并对每个租户重复相同的步骤，或使用本示例部署单个租户 VNet。

## <a name="configure-azure-stack"></a>配置 Azure Stack
现在创建所需的资源，将 Azure Stack 环境设置为租户。 以下步骤演示了所需的操作。 以下说明介绍如何使用 Azure Stack 门户创建资源，但也可以使用 PowerShell。

![网络资源步骤](media/azure-stack-connect-expressroute/image2.png)
### <a name="before-you-begin"></a>开始之前
在开始配置之前，需要：
* 一个 Azure Stack 部署。

   有关部署 Azure Stack 开发工具包的信息，请参阅 [Azure Stack 开发工具包部署快速入门](azure-stack-deploy-overview.md)。
* Azure Stack 中可供用户订阅的产品。

  有关说明，请参阅[将虚拟机提供给 Azure Stack 用户使用](azure-stack-tutorial-tenant-vm.md)。

### <a name="create-network-resources-in-azure-stack"></a>在 Azure Stack 中创建网络资源

使用以下过程在 Azure Stack 中创建每个租户所需的网络资源：

#### <a name="create-the-virtual-network-and-vm-subnet"></a>创建虚拟网络和 VM 子网
1. 使用用户（租户）帐户登录到用户门户。

2. 在门户中单击“新建”。

   ![](media/azure-stack-connect-expressroute/MAS-new.png)

3. 从“Marketplace”菜单中选择“网络”。

4. 单击菜单上的“虚拟网络”。

5. 根据下表在相应的字段中键入值：

   |字段  |值  |
   |---------|---------|
   |名称     |Tenant1VNet1         |
   |地址空间     |10.1.0.0/16|
   |子网名称     |Tenant1-Sub1|
   |子网地址范围     |10.1.1.0/24|

6. 此时会看到此前创建的订阅填充到“订阅”字段中。

    a. 对于“资源组”，可以创建资源组，也可以选择“使用现有资源组”（如果已经有了一个）。

    b. 验证默认位置。

    c. 单击“固定到仪表板”。

    d.单击“下一步”。 单击“创建”。



#### <a name="create-the-gateway-subnet"></a>创建网关子网
1. 从仪表板打开刚创建的虚拟网络资源 (Tenant1VNet1)。
2. 在“设置”部分中选择“子网”。
3. 单击“网关子网”，将网关子网添加到虚拟网络。
   
    ![](media/azure-stack-connect-expressroute/gatewaysubnet.png)
4. 默认情况下，子网的名称设置为 **GatewaySubnet**。
   网关子网很特殊，必须使用该特定名称才能正常运行。
5. 在“地址范围”字段中，检查地址是否为 **10.1.0.0/24**。
6. 单击“确定”创建网关子网。

#### <a name="create-the-virtual-network-gateway"></a>创建虚拟网络网关
1. 在 Azure Stack 用户门户中，单击“新建”。
   
2. 从“Marketplace”菜单中选择“网络”。
3. 从网络资源列表中选择“虚拟网关”。
4. 在“名称”字段中，键入 **GW1**。
5. 单击“虚拟网络”项，选择一个虚拟网络。
   从列表中选择“Tenant1VNet1”。
6. 单击“公共 IP 地址”菜单项。 当“选择公共 IP 地址”部分打开时，单击“新建”。
7. 在“名称”字段中键入 **GW1-PiP**，并单击“确定”。
8. 默认情况下，应已选择“基于路由”作为“VPN 类型”。
    保留该设置。
9. 验证“订阅”和“位置”是否正确。 可以根据需要将资源固定到仪表板。 单击“创建”。

#### <a name="create-the-local-network-gateway"></a>创建本地网关

本地网络网关资源的用途是指示 VPN 连接另一端的远程网关。 在本示例中，远程端是 ExpressRoute 路由器的 LAN 子接口。 对于本示例中的租户 1，远程地址是 10.60.3.255，如图 2 所示。

1. 登录到 Azure Stack 物理机。
2. 使用用户帐户登录到用户门户，然后单击“新建”。
3. 从“Marketplace”菜单中选择“网络”。
4. 从资源列表中选择“本地网关”。
5. 在“名称”字段中，键入 **ER-Router-GW**。
6. 对于“IP 地址”字段，请参阅图示 2。 对于租户 1，ExpressRoute 路由器的 LAN 子接口 IP 地址是 10.60.3.255。 根据自己的环境，键入路由器对应接口的 IP 地址。
7. 在“地址空间”字段中，键入要连接到 Azure 中的 VNet 的地址空间。 对于本示例，请参阅图示 2。 对于租户 1，请注意，所需的子网是 **192.168.2.0/24**（这是 Azure 中的中心 VNet）和 **10.100.0.0/16**（这是 Azure 中的分支 VNet）。 根据自己的环境，键入相应的子网。
   > [!IMPORTANT]
   > 对于 Azure Stack 网关和 ExpressRoute 路由器之间的站点到站点 VPN 连接，本示例假设使用静态路由。

8. 检查“订阅”、“资源组”和“位置”是否均正确，单击“创建”。

#### <a name="create-the-connection"></a>创建连接
1. 在 Azure Stack 用户门户中，单击“新建”。
2. 从“Marketplace”菜单中选择“网络”。
3. 从资源列表中选择“连接”。
4. 在“基本设置”部分，选择“站点到站点(IPSec)”作为“连接类型”。
5. 选择“订阅”、“资源组”和“位置”，并单击“确定”。
6. 在“设置”部分，依次单击“虚拟网络网关”、“GW1”。
7. 依次单击“本地网络网关”、“ER 路由器 GW”。
8. 在“连接名称”字段中，键入 **ConnectToAzure**。
9. 在“共享密钥(PSK)”字段中，键入 **abc123**，然后单击“确定”。
10. 在“摘要”部分，单击“确定”。

    创建连接后，可以看到虚拟网络网关使用的公共 IP 地址。 若要在 Azure Stack 门户中查找地址，请浏览到虚拟网络网关。 在“概述”中，找到“公共 IP 地址”。 请记下此地址；下一部分要将它用作“内部 IP 地址”（如果适用于部署）。

    ![](media/azure-stack-connect-expressroute/GWPublicIP.png)

#### <a name="create-a-virtual-machine"></a>创建虚拟机
若要验证通过 VPN 连接传输的数据，需要虚拟机在 Azure Stack VNet 中发送和接收数据。 现在请创建虚拟机，并将其放在虚拟网络中的 VM 子网上。

1. 在 Azure Stack 用户门户中，单击“新建”。
2. 从“Marketplace”菜单中选择“虚拟机”。
3. 在虚拟机映像列表中，选择“Windows Server 2016 Datacenter Eval”映像，然后单击“创建”。
4. 在“基本”部分的“名称”字段中，键入 **VM01**。
5. 键入有效的用户名和密码。 在创建 VM 后，将使用此帐户登录到该 VM。
6. 提供“订阅”、“资源组”和“位置”，并单击“确定”。
7. 在“大小”部分，单击此实例的虚拟机大小，然后单击“选择”。
8. 在“设置”部分，可以接受默认值。 但请确保选择的虚拟网络是 **Tenant1VNet1**，且子网已设置为 **10.1.1.0/24**。 单击“确定”。
9. 检查“摘要”部分中的设置，单击“确定”。

针对要连接的每个租户 VNet，重复执行前面的**创建虚拟网络和 VM 子网**到**创建虚拟机**部分中所述的步骤。

### <a name="configure-the-nat-virtual-machine-for-gateway-traversal"></a>配置用于网关遍历的 NAT 虚拟机
> [!IMPORTANT]
> 本部分仅适用于 Azure Stack 开发工具包部署。 多节点部署无需 NAT。

Azure Stack 开发工具包是自主性的，与部署物理主机的网络相隔离。 因此，网关所连接的“外部”VIP 网络不是在外部，而是隐藏在执行网络地址转换 (NAT) 的路由器后面。
 
该路由器是 Windows Server 虚拟机 (**AzS-BGPNAT01**)，在 Azure Stack 开发工具包基础结构中充当路由和远程访问服务 (RRAS) 角色。 必须对 AzS-BGPNAT01 虚拟机上的 NAT 进行配置，允许站点到站点 VPN 连接在两端进行连接。

#### <a name="configure-the-nat"></a>配置 NAT

1. 使用管理员帐户登录到 Azure Stack 物理机。
2. 复制并编辑以下 PowerShell 脚本，然后在权限提升的 Windows PowerShell ISE 中运行。 替换管理员密码 返回的地址是外部 BGPNAT 地址。

   ```
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "<your administrator password>" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "azs-bgpnat01" `
    -Password $Password
   ```
4. 若要配置 NAT，请复制并编辑以下 PowerShell 脚本，然后在权限提升的 Windows PowerShell ISE 中运行。 编辑脚本，替换外部 BGPNAT 地址和内部 IP 地址（前面在**创建连接**部分中记下的值）。

   在示例图示中，外部 BGPNAT 地址是 10.10.0.62，内部 IP 地址是 192.168.102.1。

   ```
   $ExtBgpNat = '<External BGPNAT address>'
   $IntBgpNat = '<Internal IP address>'

   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 4499 `
      -PortEnd 4501}
   # create a static NAT mapping to map the external address to the Gateway
   # Public IP Address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 500 `
      -InternalPort 500}
   # Finally, configure NAT traversal which uses port 4500 to
   # successfully establish the complete IPSEC tunnel over NAT devices
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

## <a name="configure-azure"></a>配置 Azure
完成 Azure Stack 配置后，可以部署一些 Azure 资源。 下图显示了 Azure 中的示例租户虚拟网络。 对于 Azure 中的 VNet，可以使用任何名称和地址方案。 但是，在 Azure 和 Azure Stack 中，VNet 的地址范围必须唯一，而不能重叠。

![Azure VNet](media/azure-stack-connect-expressroute/AzureArchitecture.png)

**图示 3**

在 Azure 中部署的资源类似于在 Azure Stack 中部署的资源。 同样，需要部署：
* 虚拟网络和子网
* 网关子网
* 虚拟网络网关
* 连接
* ExpressRoute 线路

示例 Azure 网络基础结构的配置方式如下：
* 使用标准的中心 (192.168.2.0/24) 和分支 (10.100.0.0./16) VNet 模型。
* 工作负荷部署在分支 VNet 中，ExpressRoute 线路连接到中心 VNet。
* 通过 VNet 对等互连功能来链接这两个 VNet。

### <a name="configure-vnets"></a>设置 VNet
1. 使用 Azure 凭据登录到 Azure 门户。
2. 使用 192.168.2.0/24 地址空间创建中心 VNet。 使用 192.168.2.0/25 地址范围创建子网，使用 192.168.2.128/27 地址范围添加网关子网。
3. 使用 10.100.0.0/16 地址范围创建分支 VNet 和子网。


有关在 Azure 中创建虚拟网络的详细信息，请参阅[创建虚拟网络](../virtual-network/manage-virtual-network.md#create-a-virtual-network)。

### <a name="configure-an-expressroute-circuit"></a>配置 ExpressRoute 线路

1. 查看 [ExpressRoute 先决条件和查检表](../expressroute/expressroute-prerequisites.md)中的 ExpressRoute 先决条件。
2. 根据[创建和修改 ExpressRoute 线路](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)中的步骤，使用 Azure 订阅创建 ExpressRoute 线路。
3. 将上一步骤中的服务密钥提供给主机服务提供商，以便在他们一端预配 ExpressRoute 线路。
4. 根据[创建和修改 ExpressRoute 线路的对等互连](../expressroute/expressroute-howto-routing-portal-resource-manager.md)中的步骤，在 ExpressRoute 线路上配置专用对等互连。

### <a name="create-the-virtual-network-gateway"></a>创建虚拟网络网关

* 根据[使用 PowerShell 配置 ExpressRoute 的虚拟网络网关](../expressroute/expressroute-howto-add-gateway-resource-manager.md)中的步骤，在中心 VNet 中为 ExpressRoute 创建虚拟网络网关。

### <a name="create-the-connection"></a>创建连接

* 若要将 ExpressRoute 线路链接到中心 VNet，请遵循[将虚拟网络连接到 ExpressRoute 线路](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)中的步骤。

### <a name="peer-the-vnets"></a>在 VNet 之间建立对等互连

* 根据[使用 Azure 门户创建虚拟网络对等互连](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md)中的步骤，在中心和分支 VNet 之间建立对等互连。 配置 VNet 对等互连时，请务必选择以下选项：
   * 从中心到分支：**允许网关传输**
   * 从分支到中心：**使用远程网关**

### <a name="create-a-virtual-machine"></a>创建虚拟机

* 将工作负荷虚拟机部署到分支 VNet。

针对 Azure 中要通过相应 ExpressRoute 线路连接的其他任何租户 VNet，重复这些步骤。

## <a name="configure-the-router"></a>配置路由器

可以参考以下端到端基础结构图示配置 ExpressRoute 路由器。 此图显示了两个租户（租户 1 和租户 2）及其各自的 Express Route 线路。 每条线路链接到各自在 ExpressRoute 路由器的 LAN 和 WAN 端的 VRF（虚拟路由和转发），确保两个租户之间保持端到端隔离。 在学习示例配置的过程中，请记下路由器接口中使用的 IP 地址。

![端到端图示](media/azure-stack-connect-expressroute/EndToEnd.png)

**图示 4**

可以使用任何支持 IKEv2 VPN 和 BGP 的路由器，来终止 Azure Stack 的站点到站点 VPN 连接。 同一路由器用于通过 ExpressRoute 线路连接到 Azure。 

下面是 Cisco ASR 1000 的示例配置，它支持图示 4 中所示的网络基础结构：

```
ip vrf Tenant 1
 description Routing Domain for PRIVATE peering to Azure for Tenant 1
 rd 1:1
!
ip vrf Tenant 2
 description Routing Domain for PRIVATE peering to Azure for Tenant 2
 rd 1:5
!
crypto ikev2 proposal V2-PROPOSAL2 
description IKEv2 proposal for Tenant 1 
encryption aes-cbc-256
 integrity sha256
 group 2
crypto ikev2 proposal V4-PROPOSAL2 
description IKEv2 proposal for Tenant 2 
encryption aes-cbc-256
 integrity sha256
 group 2
!
crypto ikev2 policy V2-POLICY2 
description IKEv2 Policy for Tenant 1 
match fvrf Tenant 1
 match address local 10.60.3.255
 proposal V2-PROPOSAL2
description IKEv2 Policy for Tenant 2
crypto ikev2 policy V4-POLICY2 
 match fvrf Tenant 2
 match address local 10.60.3.251
 proposal V4-PROPOSAL2
!
crypto ikev2 profile V2-PROFILE
description IKEv2 profile for Tenant 1 
match fvrf Tenant 1
 match address local 10.60.3.255
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 1
!
crypto ikev2 profile V4-PROFILE
description IKEv2 profile for Tenant 2
 match fvrf Tenant 2
 match address local 10.60.3.251
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 2
!
crypto ipsec transform-set V2-TRANSFORM2 esp-gcm 256 
 mode tunnel
crypto ipsec transform-set V4-TRANSFORM2 esp-gcm 256 
 mode tunnel
!
crypto ipsec profile V2-PROFILE
 set transform-set V2-TRANSFORM2 
 set ikev2-profile V2-PROFILE
!
crypto ipsec profile V4-PROFILE
 set transform-set V4-TRANSFORM2 
 set ikev2-profile V4-PROFILE
!
interface Tunnel10
description S2S VPN Tunnel for Tenant 1
 ip vrf forwarding Tenant 1
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.211
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf Tenant 1
 tunnel protection ipsec profile V2-PROFILE
!
interface Tunnel20
description S2S VPN Tunnel for Tenant 2
 ip vrf forwarding Tenant 2
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.213
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf VNET3
 tunnel protection ipsec profile V4-PROFILE
!
interface GigabitEthernet0/0/1
 description PRIMARY Express Route Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/1.100
description Primary WAN interface of Tenant 1
 description PRIMARY ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.1 255.255.255.252
!
interface GigabitEthernet0/0/1.102
description Primary WAN interface of Tenant 2
 description PRIMARY ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.17 255.255.255.252
!
interface GigabitEthernet0/0/2
 description BACKUP Express Route Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/2.100
description Secondary WAN interface of Tenant 1
 description BACKUP ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.5 255.255.255.252
!
interface GigabitEthernet0/0/2.102
description Secondary WAN interface of Tenant 2 
description BACKUP ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.21 255.255.255.252
!
interface TenGigabitEthernet0/1/0
 description Downlink to ---Port 1/47
 no ip address
!
interface TenGigabitEthernet0/1/0.211
 description LAN interface of Tenant 1
description Downlink to --- Port 1/47.211
 encapsulation dot1Q 211
 ip vrf forwarding Tenant 1
 ip address 10.60.3.255 255.255.255.254
!
interface TenGigabitEthernet0/1/0.213
description LAN interface of Tenant 2
 description Downlink to --- Port 1/47.213
 encapsulation dot1Q 213
 ip vrf forwarding Tenant 2
 ip address 10.60.3.251 255.255.255.254
!
router bgp 65530
 bgp router-id <removed>
 bgp log-neighbor-changes
 description BGP neighbor config and route advertisement for Tenant 1 VRF 
 address-family ipv4 vrf Tenant 1
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.254 mask 255.255.255.254
  network 192.168.1.0 mask 255.255.255.252
  network 192.168.1.4 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65100
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 1
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.254 remote-as 4232570301
  neighbor 10.60.3.254 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.254 activate
  neighbor 10.60.3.254 route-map BLOCK-ALL out
  neighbor 192.168.1.2 remote-as 12076
  neighbor 192.168.1.2 description PRIMARY ER peer for Tenant 1 to Azure
  neighbor 192.168.1.2 ebgp-multihop 5
  neighbor 192.168.1.2 activate
  neighbor 192.168.1.2 soft-reconfiguration inbound
  neighbor 192.168.1.2 route-map Tenant 1-ONLY out
  neighbor 192.168.1.6 remote-as 12076
  neighbor 192.168.1.6 description BACKUP ER peer for Tenant 1 to Azure
  neighbor 192.168.1.6 ebgp-multihop 5
  neighbor 192.168.1.6 activate
  neighbor 192.168.1.6 soft-reconfiguration inbound
  neighbor 192.168.1.6 route-map Tenant 1-ONLY out
  maximum-paths 8
 exit-address-family
 !
description BGP neighbor config and route advertisement for Tenant 2 VRF 
address-family ipv4 vrf Tenant 2
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.250 mask 255.255.255.254
  network 192.168.1.16 mask 255.255.255.252
  network 192.168.1.20 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65300
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 2
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.250 remote-as 4232570301
  neighbor 10.60.3.250 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.250 activate
  neighbor 10.60.3.250 route-map BLOCK-ALL out
  neighbor 192.168.1.18 remote-as 12076
  neighbor 192.168.1.18 description PRIMARY ER peer for Tenant 2 to Azure
  neighbor 192.168.1.18 ebgp-multihop 5
  neighbor 192.168.1.18 activate
  neighbor 192.168.1.18 soft-reconfiguration inbound
  neighbor 192.168.1.18 route-map VNET-ONLY out
  neighbor 192.168.1.22 remote-as 12076
  neighbor 192.168.1.22 description BACKUP ER peer for Tenant 2 to Azure
  neighbor 192.168.1.22 ebgp-multihop 5
  neighbor 192.168.1.22 activate
  neighbor 192.168.1.22 soft-reconfiguration inbound
  neighbor 192.168.1.22 route-map VNET-ONLY out
  maximum-paths 8
 exit-address-family
!
ip forward-protocol nd
!
ip as-path access-list 1 permit ^$
ip route vrf Tenant 1 10.1.0.0 255.255.0.0 Tunnel10
ip route vrf Tenant 2 10.1.0.0 255.255.0.0 Tunnel20
!
ip prefix-list BLOCK-ALL seq 5 deny 0.0.0.0/0 le 32
!
route-map BLOCK-ALL permit 10
 match ip address prefix-list BLOCK-ALL
!
route-map VNET-ONLY permit 10
 match as-path 1
!
```

## <a name="test-the-connection"></a>测试连接

建立站点到站点连接和 ExpressRoute 线路之后，请测试连接。 此任务十分简单。  登录到在 Azure VNet 中创建的虚拟机之一，然后针对 Azure Stack 环境中创建的虚拟机执行 ping，反之亦然。 

为了确保通过站点到站点和 ExpressRoute 连接发送流量，必须在两端 ping 虚拟机的专用 IP (DIP) 地址，而不是虚拟机的 VIP 地址。 因此，必须找到并记下连接另一端的地址。

### <a name="allow-icmp-in-through-the-firewall"></a>允许 ICMP 通过防火墙
默认情况下，Windows Server 2016 不允许 ICMP 数据包通过防火墙。 因此，对于测试中使用的每个虚拟机，请在权限提升的 PowerShell 窗口中运行以下 cmdlet：


   ```
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="ping-the-azure-stack-virtual-machine"></a>Ping Azure Stack 虚拟机

1. 使用租户帐户登录到 Azure Stack 用户门户。
2. 单击左侧导航栏中的“虚拟机”。
3. 找到并单击前面创建的虚拟机。
4. 在虚拟机部分，单击“连接”。
5. 打开权限提高的 PowerShell 窗口，键入 **ipconfig /all**。
6. 在输出中找到并记下 IPv4 地址。 从 Azure VNet 中的虚拟机 Ping 此地址。 在示例环境中，此地址来自 10.1.1.x/24 子网。 在环境中，此地址可能不同。 但是，该地址应在为租户 VNet 子网创建的子网内。


### <a name="view-data-transfer-statistics"></a>查看数据传输统计信息

若要了解有多少流量通过连接，可以在 Azure Stack 用户门户的“连接”部分找到此信息。 此信息也很适合用于验证刚刚发送的 ping 是否确实通过了 VPN 和 ExpressRoute 连接。

1. 登录到 Microsoft Azure 堆栈用户门户使用你的租户帐户。
2. 导航到创建 VPN 网关的资源组，然后选择“连接”对象类型。
3. 在列表中单击“ConnectToAzure”连接。
4. 在“连接”部分，可以看到“传入数据”和“传出数据”的统计信息。应该会看到其中有一些非零值。

   ![传入数据/传出数据](media/azure-stack-connect-expressroute/DataInDataOut.png)

## <a name="next-steps"></a>后续步骤
[将应用部署到 Azure 和 Azure Stack](azure-stack-solution-pipeline.md)
