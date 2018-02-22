---
title: "连接到 Azure 中使用 ExpressRoute 的 Azure 堆栈"
description: "如何将 Azure 堆栈中的虚拟网络连接到在 Azure 中使用 ExpressRoute 的虚拟网络。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 8ec733c01b19f01951d0b53265daaef32c3afe73
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="connect-azure-stack-to-azure-using-expressroute"></a>连接到 Azure 中使用 ExpressRoute 的 Azure 堆栈

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

有两个受支持的方法，可将 Azure 堆栈中的虚拟网络连接到 Azure 中的虚拟网络：
   * **站点到站点**

     通过 IPsec （IKE v1 和 IKE v2） 的 VPN 连接。 此类型的连接需要 VPN 设备或 RRAS。 有关详细信息，请参阅[到 Azure 中使用 VPN 连接 Azure 堆栈](azure-stack-connect-vpn.md)。
   * **ExpressRoute**

     从 Azure 堆栈部署直接连接到 Azure。 ExpressRoute 是**不**通过公共 Internet 的 VPN 连接。 有关 Azure ExpressRoute 的详细信息，请参阅[ExpressRoute 概述](../expressroute/expressroute-introduction.md)。

这篇文章演示使用 ExpressRoute 连接到 Azure 的 Azure 堆栈的示例。
## <a name="requirements"></a>要求
将 Azure 堆栈和 Azure 中使用 ExpressRoute 连接的特定要求如下：
* 在 Azure 中创建的 ExpressRoute 线路和 Vnet 一个 Azure 订阅。
* 一个设置条 ExpressRoute 线路[连接提供商](../expressroute/expressroute-locations.md)。
* 具有 ExpressRoute 线路连接到其 WAN 端口路由器。
* 路由器的 LAN 端链接到 Azure 堆栈多租户网关。
* 路由器必须支持其 LAN 界面和 Azure 堆栈多租户网关之间的站点到站点 VPN 连接。
* 如果 Azure 堆栈部署中添加多个租户，则路由器必须能够创建多个 Vrf （虚拟路由和转发）。

完成配置后下, 图显示网络的概念视图：

![概念图](media/azure-stack-connect-expressroute/Conceptual.png)

**图 1**

以下体系结构关系图显示如何将多个租户连接从 Azure 堆栈基础结构通过 ExpressRoute 路由器到 Azure 在 Microsoft edge:

![体系结构关系图](media/azure-stack-connect-expressroute/Architecture.png)

**图 2**

这篇文章中所示的示例使用相同的体系结构通过 ExpressRoute 专用对等连接到 Azure。 完毕后到 ExpressRoute 路由器 Azure 堆栈中使用从虚拟网络网关的站点到站点 VPN 连接。 这篇文章中的以下步骤演示如何创建两个 Vnet 来自 Azure 堆栈中的两个不同租户连接到 Azure 中其相应的 Vnet 之间的端到端连接。 你可以选择添加许多租户 Vnet 并为每个租户复制步骤或使用此示例部署只需单个租户 VNet。

## <a name="configure-azure-stack"></a>配置 Azure 堆栈
现在，您创建的资源，你需要为租户你 Azure 堆栈的环境设置。 以下步骤说明了你需要做什么。 这些说明介绍如何创建资源使用 Azure 堆栈门户中，但你也可以使用 PowerShell。

![网络资源的步骤](media/azure-stack-connect-expressroute/image2.png)
### <a name="before-you-begin"></a>开始之前
在开始配置之前，你需要：
* Azure 堆栈部署。

   有关部署 Azure 堆栈开发工具包的信息，请参阅[Azure 堆栈开发工具包部署快速入门](azure-stack-deploy-overview.md)。
* 你的用户可以订阅的 Azure 堆栈上服务关联。

  有关说明，请参阅[让 Azure 堆栈用户能够使用虚拟机](azure-stack-tutorial-tenant-vm.md)。

### <a name="create-network-resources-in-azure-stack"></a>在 Azure 堆栈中创建网络资源

使用以下过程在 Azure 堆栈中为每个租户创建所需的网络资源：

#### <a name="create-the-virtual-network-and-vm-subnet"></a>创建虚拟网络和 VM 子网
1. 在用户门户与用户 （租户） 帐户登录。

2. 在门户中，单击**新建**。

   ![](media/azure-stack-connect-expressroute/MAS-new.png)

3. 从“Marketplace”菜单中选择“网络”。

4. 单击菜单上的“虚拟网络”。

5. 使用下表的相应字段输入的值：

   |字段  |值  |
   |---------|---------|
   |名称     |Tenant1VNet1         |
   |地址空间     |10.1.0.0/16|
   |子网名称     |Tenant1-Sub1|
   |子网地址范围     |10.1.1.0/24|

6. 此时会看到此前创建的订阅填充到“订阅”字段中。

    a. 对于资源组，你可以创建资源组或如果已经拥有一个帐户，选择**使用现有**。

    b. 验证默认位置。

    c. 单击“固定到仪表板”。

    d.单击“下一步”。 单击“创建”。



#### <a name="create-the-gateway-subnet"></a>创建网关子网
1. 打开从仪表板创建 (Tenant1VNet1) 的虚拟网络资源。
2. 在设置部分中，选择**子网**。
3. 单击“网关子网”，将网关子网添加到虚拟网络。
   
    ![](media/azure-stack-connect-expressroute/gatewaysubnet.png)
4. 默认情况下，子网的名称设置为 **GatewaySubnet**。
   网关子网很特殊，必须使用该特定名称才能正常运行。
5. 在**地址范围**字段中，验证地址是否**10.1.0.0/24**。
6. 单击“确定”创建网关子网。

#### <a name="create-the-virtual-network-gateway"></a>创建虚拟网络网关
1. 在 Azure 堆栈用户门户中，单击**新建**。
   
2. 从“Marketplace”菜单中选择“网络”。
3. 从网络资源列表中选择“虚拟网关”。
4. 在“名称”字段中，键入 **GW1**。
5. 单击“虚拟网络”项，选择一个虚拟网络。
   选择**Tenant1VNet1**从列表中。
6. 单击“公共 IP 地址”菜单项。 当**选择公共 IP 地址**部分打开单击**新建**。
7. 在“名称”字段中键入 **GW1-PiP**，并单击“确定”。
8. 默认情况下，应已选择“基于路由”作为“VPN 类型”。
    保留该设置。
9. 验证“订阅”和“位置”是否正确。 如果你想，你可以固定到仪表板的资源。 单击“创建”。

#### <a name="create-the-local-network-gateway"></a>创建本地网关

本地网络网关资源的目的是以指示在另一端的 VPN 连接的远程网关。 对于此示例中，远程端是 ExpressRoute 路由器 LAN 个子。 对于在此示例中的租户 1，远程地址是 10.60.3.255，如图 2 中所示。

1. 登录到 Azure 堆栈物理计算机。
2. 登录到用户门户与你的用户帐户并单击**新建**。
3. 从“Marketplace”菜单中选择“网络”。
4. 从资源列表中选择“本地网关”。
5. 在**名称**字段中键入**ER 路由器 GW**。
6. 有关**IP 地址**字段，请参阅图 2。 租户 1 ExpressRoute 路由器的 LAN 子接口的 IP 地址是 10.60.3.255。 对于你自己的环境中，键入路由器的相应接口的 IP 地址。
7. 在**地址空间**字段中，键入你想要连接到 Azure 中的 Vnet 的地址空间。 此示例中，请参阅图 2。 对于租户 1，请注意，所需的子网是**192.168.2.0/24** （这是 Azure 中的中心 Vnet） 和**10.100.0.0/16** （这是 Azure 中的分支 VNet）。 键入你自己的环境的相应子网。
   > [!IMPORTANT]
   > 此示例假定你在 Azure 堆栈网关和 ExpressRoute 路由器之间的站点到站点 VPN 连接使用静态路由。

8. 验证你**订阅**，**资源组**，和**位置**是否都正确，然后单击**创建**。

#### <a name="create-the-connection"></a>创建连接
1. 在 Azure 堆栈用户门户中，单击**新建**。
2. 从“Marketplace”菜单中选择“网络”。
3. 从资源列表中选择“连接”。
4. 在**基础知识**设置部分中，选择**站点到站点 (IPSec)**作为**连接类型**。
5. 选择**订阅**，**资源组**，和**位置**单击**确定**。
6. 在**设置**部分中，单击**虚拟网络网关**单击**GW1**。
7. 单击**本地网络网关**，然后单击**ER 路由器 GW**。
8. 在**连接名称**字段中，键入**ConnectToAzure**。
9. 在**共享密钥 (PSK)**字段中，键入**abc123**单击**确定**。
10. 上**摘要**部分中，单击**确定**。

    创建连接后，你可以看到虚拟网络网关所用的公共 IP 地址。 若要查找的地址在 Azure 堆栈门户中，浏览到虚拟网络网关。 在**概述**，查找**公共 IP 地址**。 请注意此地址;你将使用其作为*内部 IP 地址*下一步的部分 （如果适用于你的部署） 中。

    ![](media/azure-stack-connect-expressroute/GWPublicIP.png)

#### <a name="create-a-virtual-machine"></a>创建虚拟机
若要验证通过 VPN 连接的数据，您需要发送和接收数据 Azure 堆栈 Vnet 中的虚拟机。 现在创建虚拟机并将其置于你虚拟网络中的 VM 子网。

1. 在 Azure 堆栈用户门户中，单击**新建**。
2. 从“Marketplace”菜单中选择“虚拟机”。
3. 在虚拟机映像的列表中，选择**Windows Server 2016 数据中心 Eval**映像并单击**创建**。
4. 上**基础知识**部分中，在**名称**字段中键入**VM01**。
5. 键入有效的用户名和密码。 在创建 VM 后，将使用此帐户登录到该 VM。
6. 提供**订阅**，**资源组**，和**位置**，然后单击**确定**。
7. 上**大小**部分中单击此实例的虚拟机大小，然后单击**选择**。
8. 上**设置**部分中，你可以接受默认值。 但确保选中了虚拟网络都具有**Tenant1VNet1**和子网设置为**10.1.1.0/24**。 单击“确定”。
9. 上查看设置**摘要**部分并单击**确定**。

对于每个租户你想要连接的 VNet，请重复前面的步骤从**创建虚拟网络和 VM 子网**通过**创建虚拟机**部分。

### <a name="configure-the-nat-virtual-machine-for-gateway-traversal"></a>配置网关遍历 NAT 虚拟机
> [!IMPORTANT]
> 本部分适用于仅限 Azure 堆栈开发工具包部署。 NAT 不需要为多节点部署。

Azure 堆栈开发工具包是自包含且独立于在其部署的物理主机的网络。 因此，"External"VIP 网络网关连接到不是外部的但改为隐藏执行网络地址转换 (NAT) 的路由器后面。
 
路由器是 Windows Server 虚拟机 (**AzS BGPNAT01**) Azure 堆栈开发工具包基础结构中运行路由和远程访问服务 (RRAS) 角色。 你必须以允许站点到站点 VPN 连接来连接两端 AzS BGPNAT01 虚拟机上配置 NAT。

#### <a name="configure-the-nat"></a>配置 NAT

1. 登录到你的管理员帐户的 Azure 堆栈物理机。
2. 复制并编辑以下 PowerShell 脚本，然后在提升的 Windows PowerShell ISE 中运行。 将您的管理员密码。 返回地址是你*外部 BGPNAT 地址*。

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
4. 若要配置 NAT，复制并编辑以下 PowerShell 脚本，然后在提升的 Windows PowerShell ISE 中运行。 编辑脚本，以将*外部 BGPNAT 地址*和*内部 IP 地址*(其中中记下以前**创建连接**部分)。

   在示例图中，*外部 BGPNAT 地址*是 10.10.0.62 和*内部 IP 地址*是 192.168.102.1。

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
现在，你已完成 Azure 堆栈配置，你可以部署某些 Azure 资源。 下图显示示例租户在 Azure 中的虚拟网络。 在 Azure vnet，你可以使用任何名称和寻址方案。 但是，在 Azure 和 Azure 堆栈中的 Vnet 的地址范围必须唯一，且不重叠。

![Azure Vnet](media/azure-stack-connect-expressroute/AzureArchitecture.png)

**图 3**

在 Azure 中部署的资源都类似于你在 Azure 堆栈中部署的资源。 同样，你将部署：
* 虚拟网络和子网
* 网关子网
* 虚拟网络网关
* 连接
* 一条 ExpressRoute 线路

示例 Azure 网络基础结构配置方式如下：
* 使用标准中心 (192.168.2.0/24) 和分支 (10.100.0.0./16) VNet 模型。
* 工作负荷部署在分支 Vnet 和 ExpressRoute 线路连接到集线器 VNet。
* 两个 Vnet 已链接使用 VNet 对等功能。

### <a name="configure-vnets"></a>配置 Vnet
1. 登录到 Azure 门户与你的 Azure 凭据。
2. 创建中心 VNet 使用 192.168.2.0/24 地址空间。 创建子网使用 192.168.2.0/25 地址范围，并添加网关子网使用 192.168.2.128/27 地址范围。
3. 创建分支 VNet 和子网使用 10.100.0.0/16 地址范围。


有关在 Azure 中创建虚拟网络的详细信息，请参阅[创建具有多个子网的虚拟网络](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)。

### <a name="configure-an-expressroute-circuit"></a>配置 ExpressRoute 线路

1. 查看中的 ExpressRoute 先决条件[ExpressRoute 先决条件和清单](../expressroute/expressroute-prerequisites.md)。
2. 按照中的步骤[创建和修改 ExpressRoute 线路](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)创建使用你的 Azure 订阅的 ExpressRoute 线路。
3. 与你托管商/提供程序来设置 ExpressRoute 线路在其内部共享服务密钥从前面的步骤。
4. 按照中的步骤[创建和修改 ExpressRoute 线路的对等互连](../expressroute/expressroute-howto-routing-portal-resource-manager.md)配置专用对等互连上 ExpressRoute 线路。

### <a name="create-the-virtual-network-gateway"></a>创建虚拟网络网关

* 按照中的步骤[将虚拟网络网关配置为使用 PowerShell 的 ExpressRoute](../expressroute/expressroute-howto-add-gateway-resource-manager.md)在中心 VNet 中为 ExpressRoute 创建虚拟网络网关。

### <a name="create-the-connection"></a>创建连接

* 若要将 ExpressRoute 线路链接到中心 VNet，请按照中的步骤[的虚拟网络连接到 ExpressRoute 线路](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)。

### <a name="peer-the-vnets"></a>对等 Vnet

* 对等的中心和分支 Vnet 使用中的步骤[创建使用 Azure 门户的虚拟网络对等互连](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md)。 在配置 VNet 对等时，确保选择以下选项：
   * 从中心辐射到：**允许网关传输**
   * 从到中心辐射：**使用远程网关**

### <a name="create-a-virtual-machine"></a>创建虚拟机

* 将你工作负荷的虚拟机部署到分支 VNet。

任何其他租户你想要在 Azure 中通过其各自的 ExpressRoute 线路的连接的 Vnet 重复这些步骤。

## <a name="configure-the-router"></a>配置路由器

下面的端到端基础结构关系图可用于指导 ExpressRoute 路由器的配置。 下图显示其各自的 Express Route 线路两个租户 （租户 1 和租户 2）。 每个将 LAN 和 WAN 方 ExpressRoute 路由器，从而确保端到端隔离两个租户之间的链接到其自己的 VRF （虚拟路由和转发）。 请注意在执行时的示例配置路由器接口中使用的 IP 地址。

![端到端关系图](media/azure-stack-connect-expressroute/EndToEnd.png)

**图 4**

你可以使用支持 IKEv2 VPN 和 BGP 可以终止从 Azure 堆栈的站点到站点 VPN 连接的任何路由器。 同一个路由器用于连接到 Azure 中使用一条 ExpressRoute 线路。 

下面是配置从支持网络基础结构关系图 4 中所示 Cisco ASR 1000 的示例：

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

测试在建立站点到站点连接和 ExpressRoute 线路后的连接。 此任务是简单。  登录到你在 Azure VNet 中创建的虚拟机之一并创建在 Azure 堆栈环境中，反之亦然的虚拟机执行 ping 操作。 

若要确保你要发送的流量通过站点到站点和 ExpressRoute 连接，必须 ping 同时结束时的虚拟机的专用的 IP (DIP) 地址而不是虚拟机的 VIP 地址。 因此，你必须查找和记下连接的另一端上的地址。

### <a name="allow-icmp-in-through-the-firewall"></a>允许通过防火墙中的 ICMP
默认情况下，Windows Server 2016 不允许在通过防火墙的 ICMP 数据包。 因此，对于在测试中使用每个虚拟机，在提升的 PowerShell 窗口中运行以下 cmdlet:


   ```
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="ping-the-azure-stack-virtual-machine"></a>Ping Azure 堆栈的虚拟机

1. 登录到 Azure 堆栈用户门户使用的租户帐户。
2. 单击左侧导航栏中的“虚拟机”。
3. 找到以前创建的虚拟机，然后单击它。
4. 在为虚拟机部分中，单击**连接**。
5. 打开提升的 PowerShell 窗口，然后键入**ipconfig /all**。
6. 在输出中查找的 IPv4 地址，并记下它。 从 Azure VNet 中的虚拟机此地址执行 ping 操作。 在示例环境中，该地址是从 10.1.1.x/24 子网。 在环境中，地址可能不同。 但是，它应位于你创建的租户 VNet 子网的子网内。


### <a name="view-data-transfer-statistics"></a>查看数据传输统计信息

如果你想要知道多少流量通过你的连接，你可以在 Azure 堆栈用户门户中查找连接部分上的此信息。 此信息也是验证刚刚发送 ping 实际经历 VPN 和 ExpressRoute 连接的另一个好办法。

1. 登录到 Microsoft Azure 堆栈用户门户使用你的租户帐户。
2. 导航到在其中创建 VPN 网关的资源组，然后选择**连接**对象类型。
3. 单击**ConnectToAzure**列表中的连接。
4. 上**连接**部分中，你可以看到的统计信息**中的数据**和**输出数据量**。应该会看到其中有一些非零值。

   ![输出的数据中的数据](media/azure-stack-connect-expressroute/DataInDataOut.png)

## <a name="next-steps"></a>后续步骤
[将应用部署到 Azure 和 Azure 堆栈](azure-stack-solution-pipeline.md)
