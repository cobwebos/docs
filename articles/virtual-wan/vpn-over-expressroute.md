---
title: 配置 ExpressRoute 加密： IPsec over ExpressRoute for Azure 虚拟 WAN
description: 在本教程中，了解如何使用 Azure 虚拟 WAN 通过 ExpressRoute 专用对等互连创建站点到站点 VPN 连接。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 02/18/2020
ms.author: cherylmc
ms.openlocfilehash: c74f703927999bf35dd2d8292b8fa0a6d3c55065
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77459780"
---
# <a name="expressroute-encryption-ipsec-over-expressroute-for-virtual-wan"></a>ExpressRoute 加密：针对虚拟 WAN 的 ExpressRoute over ExpressRoute

本文介绍如何通过 azure ExpressRoute 线路的专用对等互连，使用 Azure 虚拟 WAN 建立从本地网络到 Azure 的 IPsec/IKE VPN 连接。 此方法可以通过 ExpressRoute 在本地网络与 Azure 虚拟网络之间提供加密传输，而无需通过公共 internet 或使用公共 IP 地址。

## <a name="topology-and-routing"></a>拓扑和路由

下图显示了通过 ExpressRoute 专用对等互连的 VPN 连接示例：

![VPN over ExpressRoute](./media/vpn-over-expressroute/vwan-vpn-over-er.png)

此关系图显示了通过 ExpressRoute 专用对等互连连接到 Azure 中心 VPN 网关的本地网络中的网络。 连接建立非常简单：

1. 建立与 ExpressRoute 线路和专用对等互连的 ExpressRoute 连接。
2. 按照本文所述建立 VPN 连接。

此配置的一个重要方面是在本地网络与 Azure 之间通过 ExpressRoute 和 VPN 路径进行路由。

### <a name="traffic-from-on-premises-networks-to-azure"></a>从本地网络到 Azure 的流量

对于从本地网络到 Azure 的流量，Azure 前缀（包括虚拟中心和连接到中心的所有辐射虚拟网络）通过 ExpressRoute 专用对等 BGP 和 VPN BGP 进行播发。 这会从本地网络产生两个通向 Azure 的网络路由（路径）：

- 一个在受 IPsec 保护的路径上
- 直接通过 ExpressRoute，*无需*IPsec 保护 

若要将加密应用于通信，必须确保为关系图中的 VPN 连接的网络提供 Azure 路由，通过直接 ExpressRoute 路径首选 Azure 路由。

### <a name="traffic-from-azure-to-on-premises-networks"></a>从 Azure 到本地网络的流量

同一要求适用于从 Azure 到本地网络的流量。 若要确保 IPsec 路径优于直接 ExpressRoute 路径（没有 IPsec），可以使用两个选项：

- 针对 VPN 连接的网络在 VPN BGP 会话上播发更具体的前缀。 你可以通过 ExpressRoute 专用对等互连播发包含 VPN 连接网络的更大范围，然后在 VPN BGP 会话中公布更具体的范围。 例如，通过 ExpressRoute 播发 10.0.0.0/16 和 VPN 上的 10.0.1.0/24。

- 为 VPN 和 ExpressRoute 播发非连续前缀。 如果 VPN 连接的网络范围与其他 ExpressRoute 连接网络不相交，则可以分别在 VPN 和 ExpressRoute BGP 会话中公布这些前缀。 例如，通过 ExpressRoute 播发 10.0.0.0/24 和 10.0.1.0/24 over VPN。

在这两个示例中，Azure 将通过 VPN 连接将流量发送到 10.0.1.0/24，而不是直接通过无 VPN 保护的 ExpressRoute。

> [!WARNING]
> 如果在 ExpressRoute 和 VPN 连接上公布*相同*的前缀，Azure 将直接使用 expressroute 路径，而无需 VPN 保护。
>

## <a name="before-you-begin"></a>开始之前

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="openvwan"></a>1. 使用网关创建虚拟 WAN 和中心

在继续之前，必须准备好以下 Azure 资源和相应的本地配置：

- Azure 虚拟 WAN
- 具有[ExpressRoute 网关](virtual-wan-expressroute-portal.md)和[VPN 网关](virtual-wan-site-to-site-portal.md)的虚拟 WAN 集线器

有关使用 ExpressRoute 关联创建 Azure 虚拟 WAN 和集线器的步骤，请参阅[使用 Azure 虚拟 WAN 创建 ExpressRoute 关联](virtual-wan-expressroute-portal.md)。 有关在虚拟 WAN 中创建 VPN 网关的步骤，请参阅[使用 Azure 虚拟 Wan 创建站点到站点连接](virtual-wan-site-to-site-portal.md)。

## <a name="site"></a>2. 为本地网络创建站点

站点资源与虚拟 WAN 的非 ExpressRoute VPN 站点相同。 本地 VPN 设备的 IP 地址现在可以是专用 IP 地址，也可以是在步骤1中创建的 ExpressRoute 专用对等互连中可访问的本地网络中的公共 IP 地址。

> [!NOTE]
> 本地 VPN 设备的 IP 地址*必须*是通过 Azure ExpressRoute 专用对等互连播发到虚拟 WAN 集线器的地址前缀的一部分。
>

1. 在浏览器中转到 Azure 门户。 
1. 选择所创建的 WAN。 在 "WAN" 页的 "**连接**" 下，选择 " **VPN 站点**"。
1. 在 " **VPN 站点**" 页上，选择 " **+ 创建网站**"。
1. 在“创建站点”页上填写以下字段：
   * **订阅**：验证订阅。
   * **资源组**：选择或创建要使用的资源组。
   * **区域**：输入 VPN 站点资源的 Azure 区域。
   * **名称**：输入要用于引用本地站点的名称。
   * **设备供应商**：输入本地 VPN 设备的供应商。
   * **边界网关协议**：如果你的本地网络使用 BGP，请选择 "启用"。
   * **专用地址空间**：输入位于本地站点上的 IP 地址空间。 目标为此地址空间的流量通过 VPN 网关路由到本地网络。
   * **集线器**：选择一个或多个中心以连接此 VPN 站点。 所选中心必须已创建 VPN 网关。
1. 选择 "**下一步"：** VPN 链接设置的链接 >：
   * **链接名称**：要用于引用此连接的名称。
   * **提供程序名称**：此站点的 internet 服务提供商的名称。 对于 ExpressRoute 本地网络，它是 ExpressRoute 服务提供商的名称。
   * **速度**： internet 服务链接或 ExpressRoute 线路的速度。
   * **IP 地址**：位于本地站点的 VPN 设备的公共 IP 地址。 或者，对于本地 ExpressRoute，它是 VPN 设备通过 ExpressRoute 的专用 IP 地址。

   如果启用了 BGP，它将应用于在 Azure 中为此站点创建的所有连接。 在虚拟 WAN 上配置 BGP 等效于在 Azure VPN 网关上配置 BGP。 
   
   本地 BGP 对等机地址*不得*与 vpn 站点的设备或虚拟网络地址空间的 VPN 的 IP 地址相同。 在 VPN 设备上对 BGP 对等节点 IP 使用不同的 IP 地址。 它可以是分配给该设备上环回接口的地址。 但是，它*不能*是 APIPA （169.254）。*x*。*x*）地址。 在表示该位置的相应本地网关中指定此地址。 有关 BGP 先决条件，请参阅[关于 Azure VPN 网关的 BGP](../vpn-gateway/vpn-gateway-bgp-overview.md)。

1. 选择 "**下一步"：查看和创建 >** 以检查设置值并创建 VPN 站点。 如果选择了 "要连接的**集线器**"，则将在本地网络和中心 VPN 网关之间建立连接。

## <a name="hub"></a>3. 更新 VPN 连接设置以使用 ExpressRoute

创建 VPN 站点并连接到中心后，请使用以下步骤将连接配置为使用 ExpressRoute 专用对等互连：

1. 返回到 "虚拟 WAN 资源" 页，然后选择 "中心资源"。 或从 VPN 站点导航到连接的中心。
1. 在 "**连接**" 下，选择 " **VPN （站点到站点）** "。
1. 选择 "通过 ExpressRoute 的 VPN 站点上的省略号（ **...** ）"，然后选择 "**编辑此集线器的 vpn 连接**"。
1. 对于 "**使用 Azure 专用 IP 地址**"，请选择 **"是"** 。 此设置将集线器 VPN 网关配置为使用此连接的网关上的中心地址范围内的专用 IP 地址，而不是使用公共 IP 地址。 这将确保来自本地网络的流量会遍历 ExpressRoute 专用对等互连路径，而不是使用此 VPN 连接的公共 internet。 以下屏幕截图显示了该设置。

   ![用于将专用 IP 地址用于 VPN 连接的设置](./media/vpn-over-expressroute/vpn-link-configuration.png)
   
1. 选择“保存”。

保存更改后，集线器 VPN 网关将使用 VPN 网关上的专用 IP 地址，通过 ExpressRoute 与本地 VPN 设备建立 IPsec/IKE 连接。

## <a name="associate"></a>4. 获取中心 VPN 网关的专用 IP 地址

下载 VPN 设备配置以获取中心 VPN 网关的专用 IP 地址。 需要这些地址来配置本地 VPN 设备。

1. 在中心的页面上，选择 "**连接**" 下的 " **VPN （站点到站点）** "。
1. 在 "**概述**" 页的顶部，选择 "**下载 VPN 配置**"。 

   Azure 会在资源组 "microsoft 网络-[location]" 中创建一个存储帐户，其中*location*是 WAN 的位置。 将配置应用到 VPN 设备后，可以删除此存储帐户。
1. 创建文件后，选择要下载的链接。
1. 将配置应用到 VPN 设备。

### <a name="vpn-device-configuration-file"></a>VPN 设备配置文件

设备配置文件包含在配置本地 VPN 设备时要使用的设置。 查看此文件时，请留意以下信息：

* **vpnSiteConfiguration**：此部分表示设备详细信息设置为连接到虚拟 WAN 的站点。 它包括分支设备的名称和公共 IP 地址。
* **vpnSiteConnections**：此部分提供有关下列设置的信息：

    * 虚拟中心虚拟网络的地址空间。<br/>示例：
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * 连接到中心的虚拟网络的地址空间。<br>示例：
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * 虚拟中心的 VPN 网关的 IP 地址。 因为 VPN 网关的每个连接都包含在主动-主动配置中的两个隧道，你会看到此文件中列出了两个 IP 地址。 在此示例中，你将看到每个站点 `Instance0` 和 `Instance1`，并且它们是专用 IP 地址而不是公共 IP 地址。<br>示例：
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * VPN 网关连接的配置详细信息，例如 BGP 和预共享密钥。 系统会自动为你生成预共享密钥。 对于自定义预共享密钥，你始终可以在 "**概述**" 页上编辑该连接。
  
### <a name="example-device-configuration-file"></a>示例设备配置文件

```
[{
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"5b096293-edc3-42f1-8f73-68c14a7c4db3"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-ER-site",
        "IPAddress":"172.24.127.211",
        "LinkName":"VPN-over-ER"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"10.51.230.4",
            "Instance1":"10.51.230.5"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
    },
    {
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"fbdb34ea-45f8-425b-9bc2-4751c2c4fee0"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-INet-site",
        "IPAddress":"13.75.195.234",
        "LinkName":"VPN-over-INet"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"51.143.63.104",
            "Instance1":"52.137.90.89"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
}]
```

### <a name="configuring-your-vpn-device"></a>配置 VPN 设备

如需有关如何配置设备的说明，可以使用 [VPN 设备配置脚本页](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts)中的说明，并注意以下事项：

* VPN 设备页上的说明不是为虚拟 WAN 编写的。 但你可以使用配置文件中的虚拟 WAN 值手动配置 VPN 设备。 
* VPN 网关的可下载设备配置脚本不适用于虚拟 WAN，因为配置不同。
* 新的虚拟 WAN 可以同时支持 IKEv1 和 IKEv2。
* 虚拟 WAN 只能使用基于路由的 VPN 设备和设备指令。

## <a name="viewwan"></a>5. 查看虚拟 WAN

1. 中转到虚拟 WAN。
1. 在“概述”页上，地图中的每个点表示一个中心。 将鼠标悬停在任意点上可查看中心的运行状况摘要。
1. 在 "**中心和连接**" 部分中，可以查看中心、站点、区域和 VPN 连接状态。 您还可以查看传入和传出的字节数。

## <a name="viewhealth"></a>6. 查看资源运行状况

1. 中转到你的 WAN。
1. 在 "**支持 + 故障排除**" 部分中，选择 "**运行状况**" 并查看资源。

## <a name="connectmon"></a>7. 监视连接

创建连接来监视 Azure 虚拟机（VM）和远程站点之间的通信。 有关如何设置连接监视器的信息，请参阅[监视网络通信](~/articles/network-watcher/connection-monitor.md)。 源字段是 Azure 中的 VM IP，目标 IP 是站点 IP。

## <a name="cleanup"></a>8. 清理资源

当不再需要这些资源时，可以使用[AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)删除资源组及其包含的所有资源。 运行以下 PowerShell 命令，并将 `myResourceGroup` 替换为资源组的名称：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤

本文介绍如何使用虚拟 WAN 通过 ExpressRoute 专用对等互连创建 VPN 连接。 若要了解有关虚拟 WAN 和相关功能的详细信息，请参阅[虚拟 wan 概述](virtual-wan-about.md)。
