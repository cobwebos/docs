---
title: 在 Azure VMware 解决方案中创建 IPSec 隧道
description: 了解如何创建虚拟 WAN 集线器，以便在 Azure VMware 解决方案中建立 IPSec 隧道。
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: 74cc31abf432954008cbb20bf64825d199732dab
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951122"
---
# <a name="create-an-ipsec-tunnel-into-azure-vmware-solution"></a>在 Azure VMware 解决方案中创建 IPSec 隧道

在本文中，我们将逐步介绍在 Microsoft Azure 虚拟 WAN 集线器中建立 VPN (IPsec IKEv1 和 IKEv2) 站点到站点隧道的步骤。 我们将创建一个 Azure 虚拟 WAN 集线器和一个附加了公共 IP 地址的 VPN 网关。 接下来，我们将创建一个 Azure ExpressRoute 网关，并建立一个 Azure VMware 解决方案终结点。 我们还将详细介绍如何启用基于策略的 VPN 本地设置。 

## <a name="topology"></a>拓扑

![VPN 站点到站点隧道体系结构。](media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png)

Azure 虚拟中心包含 Azure VMware 解决方案 ExpressRoute 网关和站点到站点 VPN 网关。 它将本地 VPN 设备与 Azure VMware 解决方案终结点连接。

## <a name="before-you-begin"></a>开始之前

若要创建站点到站点 VPN 隧道，需要在本地 VPN 设备上创建一个面向公众的 IP 地址。

## <a name="create-a-virtual-wan-hub"></a>创建虚拟 WAN 中心

1. 在 Azure 门户中，搜索 " **虚拟 wan**"。 选择“+添加”  。 此时会打开 "创建 WAN" 页。  

2. 在 " **创建 WAN** " 页上输入必填字段，然后选择 " **查看 + 创建**"。
   
   | 字段 | 值 |
   | --- | --- |
   | **订阅** | 值已预先填充到属于资源组的订阅。 |
   | **资源组** | 虚拟 WAN 是全局资源，并不局限于特定区域。  |
   | **资源组位置** | 若要创建虚拟 WAN 集线器，需要设置资源组的位置。  |
   | **名称** |   |
   | 类型 | 选择 " **标准**"，它将只允许 VPN 网关流量。  |


    :::image type="content" source="media/create-ipsec-tunnel/create-wan.png" alt-text="创建 WAN。&quot;:::

3. 在 Azure 门户中，选择你在上一步中创建的虚拟 WAN，选择 &quot; **创建虚拟中心**&quot;，输入必填字段，然后选择 " **下一步：站点到站点**"。 

   | 字段 | 值 |
   | --- | --- |
   | **区域** | 从管理的角度来看，需要选择一个区域。  |
   | **名称** |    |
   | **中心专用地址空间** | 使用 `/24` (最小) 输入子网。  |

    :::image type="content" source="media/create-ipsec-tunnel/create-virtual-hub.png" alt-text="创建 WAN。&quot;:::

3. 在 Azure 门户中，选择你在上一步中创建的虚拟 WAN，选择 &quot; **创建虚拟中心**&quot;，输入必填字段，然后选择 " 下拉设置聚合吞吐量来定义站点到站点网关。 

   >[!TIP]
   >一个缩放单位 = 500 Mbps。 缩放单位成对冗余，每个单元支持 500 Mbps。
  
5. 在 " **expressroute** " 选项卡上，创建 expressroute 网关。 

   >[!TIP]
   >缩放单位值为 2 Gbps。 

    创建每个中心大约需要30分钟。 

## <a name="create-a-vpn-site"></a>创建 VPN 站点 

1. 在 Azure 门户的 " **最近使用的资源** " 中，选择在上一部分中创建的虚拟 WAN。

2. 在虚拟中心的**概述**中，选择 "**连接**  >  **VPN (站点到站点") **，然后选择 "**创建新的 VPN 站点**"。


    :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="创建 WAN。&quot;:::

3. 在 Azure 门户中，选择你在上一步中创建的虚拟 WAN，选择 &quot; **创建虚拟中心**&quot;，输入必填字段，然后选择 " **查看 + 创建**"。 通过指定链接和提供程序名称，可以区分可能最终作为中心的一部分创建的任意数量的网关。  (ASN) 的 BGP 和自治系统号在您的组织内必须是唯一的。
 
## <a name="optional-defining-a-vpn-site-for-policy-based-vpn-site-to-site-tunnels"></a> (可选) 为基于策略的 VPN 站点到站点隧道定义 VPN 站点

本部分仅适用于基于策略的 Vpn。 在大多数情况下，基于策略的 (或静态的基于路由的) VPN 设置由本地 VPN 设备功能驱动。 它们需要指定本地和 Azure VMware 解决方案网络。 对于使用 Azure 虚拟 WAN 集线器的 Azure VMware 解决方案，不能选择 *任何* 网络。 相反，你必须指定所有相关的本地和 Azure VMware 解决方案虚拟 WAN 中心范围。 这些中心范围用于指定策略基础 VPN 隧道本地终结点的加密域。 Azure VMware 解决方案端只需要启用基于策略的流量选择器指示器。 

1. 在 Azure 门户中转到你的虚拟 WAN 中心站点;在 " **连接**" 下，选择 " **VPN (站点到站点) **"。

2. 选择 VPN 站点名称，然后选择最右侧的省略号 ( ... ) ;然后选择 " **编辑 VPN" 部分到此中心**。
 
    :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png" alt-text="创建 WAN。&quot;:::

3. 在 Azure 门户中，选择你在上一步中创建的虚拟 WAN，选择 &quot; **创建虚拟中心**&quot;，输入必填字段，然后选择 " lightbox="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png":::

3. 编辑 VPN 站点和中心之间的连接，然后选择 " **保存**"。
   - Internet 协议安全 (IPSec) ，请选择 " **自定义**"。
   - 使用基于策略的流量选择器，选择 "**启用**"
   - 指定 **Ike 阶段 1** 和 **ike 阶段 2 (ipsec) **的详细信息。 
 
    :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-connection.png" alt-text="创建 WAN。&quot;:::

3. 在 Azure 门户中，选择你在上一步中创建的虚拟 WAN，选择 &quot; **创建虚拟中心**&quot;，输入必填字段，然后选择 "::: 
 
    流量选择器或属于基于策略的加密域的子网应为：
    
    - 虚拟 WAN 集线器/24
    - Azure VMware 解决方案私有云/22
    - 连接的 Azure 虚拟网络 (（如果存在）) 

## <a name="connect-your-vpn-site-to-the-hub"></a>将 VPN 站点连接到中心

1. 选中 "VPN 站点名称" 旁边的框 (参阅前面 **的 Vpn 站点到站点** 屏幕截图) ，然后选择 " **连接 VPN 站点**"。 在 " **预共享密钥** " 字段中，输入以前为本地终结点定义的密钥。 如果没有以前定义的密钥，则可以将此字段留空，并自动生成一个密钥。 
 
    仅当要在中心部署防火墙时才启用 **传播默认路由** ，并且它是通过该隧道连接的下一跃点。

    选择“连接”  。 "连接状态" 屏幕将显示隧道创建的状态。

2. 请参阅虚拟 WAN 概述。 打开 "VPN 站点" 页面并下载 VPN 配置文件以将其应用到本地终结点。  

3. 现在，我们会将 Azure VMware 解决方案集成到虚拟 WAN 中心。  (此步骤需要首先创建私有云。 ) 

    请参阅 Azure VMware 解决方案私有云的 **连接** 部分。 在 " **ExpressRoute** " 选项卡上，选择 " **+ 请求授权密钥**"。 将其命名并选择 " **创建**"。  (创建密钥可能需要大约30秒的时间。 ) 复制 ExpressRoute ID 和授权密钥。 

    :::image type="content" source="media/create-ipsec-tunnel/express-route-connectivity.png" alt-text="创建 WAN。&quot;:::

3. 在 Azure 门户中，选择你在上一步中创建的虚拟 WAN，选择 &quot; **创建虚拟中心**&quot;，输入必填字段，然后选择 " **+ 兑换授权密钥**"。

    :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="创建 WAN。&quot;:::

3. 在 Azure 门户中，选择你在上一步中创建的虚拟 WAN，选择 &quot; **创建虚拟中心**&quot;，输入必填字段，然后选择 " 以建立链接。 

5. 若要测试连接，请 [创建一个 NSX-T 段](./tutorial-nsx-t-network-segment.md) ，并在网络上预配 VM。 通过对本地和 Azure VMware 解决方案终结点进行 ping 测试。