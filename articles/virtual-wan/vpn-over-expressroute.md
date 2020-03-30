---
title: 配置快速路由加密：通过 Azure 虚拟 WAN 的快速路由进行 IPsec
description: 在本教程中，了解如何使用 Azure 虚拟 WAN 通过 ExpressRoute 专用对等互连创建站点到站点 VPN 连接。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 03/19/2020
ms.author: cherylmc
ms.openlocfilehash: b1e6305d142530ab19849f61f12a122d0c6434aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059305"
---
# <a name="expressroute-encryption-ipsec-over-expressroute-for-virtual-wan"></a>快速路由加密：通过虚拟 WAN 的快速路由进行 IPsec

本文介绍如何使用 Azure 虚拟 WAN 通过 Azure ExpressRoute 电路的专用对等互连从本地网络建立 IPsec/IKE VPN 连接。 此技术可以通过 ExpressRoute 在本地网络和 Azure 虚拟网络之间提供加密传输，而无需通过公共 Internet 或使用公共 IP 地址。

## <a name="topology-and-routing"></a>拓扑和路由

下图显示了通过 ExpressRoute 专用对等互连的 VPN 连接示例：

![VPN 通过快速路由](./media/vpn-over-expressroute/vwan-vpn-over-er.png)

该图显示了通过 ExpressRoute 专用对等互连连接到 Azure 中心 VPN 网关的本地网络中的网络。 连接建立非常简单：

1. 通过快速路由电路和专用对等互连建立快速路由连接。
2. 建立本文中所述的 VPN 连接。

此配置的一个重要方面是本地网络和 Azure 之间路由到 ExpressRoute 和 VPN 路径。

### <a name="traffic-from-on-premises-networks-to-azure"></a>从本地网络到 Azure 的流量

对于从本地网络到 Azure 的流量，Azure 前缀（包括虚拟集线器和连接到集线器的所有分支虚拟网络）通过 ExpressRoute 专用对等 BGP 和 VPN BGP 进行通告。 这将导致从本地网络向 Azure 路由（路径）两个：

- 一个在 IPsec 保护的路径上
- 直接通过快速路由，*无需*IPsec 保护 

要对通信应用加密，必须确保对于关系图中的 VPN 连接网络，首选通过本地 VPN 网关的 Azure 路由，而不是直接 ExpressRoute 路径。

### <a name="traffic-from-azure-to-on-premises-networks"></a>从 Azure 到本地网络的流量

相同的要求也适用于从 Azure 到本地网络的流量。 为了确保 IPsec 路径优先于直接 ExpressRoute 路径（不含 IPsec），您有两个选项：

- 在 VPN 连接的网络的 VPN BGP 会话上通告更具体的前缀。 您可以通过 ExpressRoute 专用对等互连通告包含 VPN 连接网络的较大范围，然后在 VPN BGP 会话中发布更具体的范围。 例如，通过 ExpressRoute 通告 10.0.0.0/16，通过 VPN 通告 10.0.1.0/24。

- 通告 VPN 和 ExpressRoute 的不相交前缀。 如果 VPN 连接的网络范围与其他 ExpressRoute 连接的网络不相交，则可以分别在 VPN 和 ExpressRoute BGP 会话中通告前缀。 例如，通过 ExpressRoute 通告 10.0.0.0/24，在 VPN 上通告 10.0.1.0/24。

在这两个示例中，Azure 将通过 VPN 连接将流量发送到 10.0.1.0/24，而不是直接通过没有 VPN 保护的 ExpressRoute 发送流量。

> [!WARNING]
> 如果在 ExpressRoute 和 VPN 连接上通告*相同的*前缀，Azure 将直接使用 ExpressRoute 路径，而无需 VPN 保护。
>

## <a name="before-you-begin"></a>开始之前

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="1-create-a-virtual-wan-and-hub-with-gateways"></a><a name="openvwan"></a>1. 创建具有网关的虚拟 WAN 和集线器

在继续操作之前，必须部署以下 Azure 资源和相应的本地配置：

- Azure 虚拟 WAN
- 具有[快速路由网关](virtual-wan-expressroute-portal.md)和[VPN 网关](virtual-wan-site-to-site-portal.md)的虚拟广域网中心

有关创建 Azure 虚拟 WAN 和具有快速路由关联的集线器的步骤，请参阅[使用 Azure 虚拟 WAN 创建快速路由关联](virtual-wan-expressroute-portal.md)。 有关在虚拟 WAN 中创建 VPN 网关的步骤，请参阅[使用 Azure 虚拟 WAN 创建站点到站点的连接](virtual-wan-site-to-site-portal.md)。

## <a name="2-create-a-site-for-the-on-premises-network"></a><a name="site"></a>2. 为本地网络创建站点

站点资源与虚拟 WAN 的非 ExpressRoute VPN 站点相同。 本地 VPN 设备的 IP 地址现在可以是专用 IP 地址，也可以是本地网络中的公共 IP 地址，可通过步骤 1 中创建的 ExpressRoute 专用对等互连进行。

> [!NOTE]
> 本地 VPN 设备的 IP 地址*必须是*通过 Azure ExpressRoute 专用对等互连通告给虚拟 WAN 中心的地址前缀的一部分。
>

1. 转到浏览器中的 Azure 门户。 
1. 选择您创建的 WAN。 在 WAN 页面上，在 **"连接**"下，选择**VPN 站点**。
1. 在**VPN 站点**页面上，选择 **"创建网站**"。
1. 在“创建站点”页上填写以下字段：****
   * **订阅**：验证订阅。
   * **资源组**：选择或创建要使用的资源组。
   * **区域**：输入 VPN 站点资源的 Azure 区域。
   * **名称**：输入要引用本地站点的名称。
   * **设备供应商**：输入本地 VPN 设备的供应商。
   * **边界网关协议**：如果您的本地网络使用 BGP，请选择"启用"。
   * **专用地址空间**：输入位于本地站点上的 IP 地址空间。 发送到此地址空间的流量通过 VPN 网关路由到本地网络。
   * **集线器**：选择一个或多个集线器以连接此 VPN 站点。 所选中心必须已创建 VPN 网关。
1. 选择 **"下一步"：VPN 链接设置的链接>：**
   * **链接名称**：要引用此连接的名称。
   * **提供商名称**：此站点的互联网服务提供商的名称。 对于 ExpressRoute 本地网络，它是 ExpressRoute 服务提供商的名称。
   * **速度**：互联网服务链路或快速路由电路的速度。
   * **IP 地址**：驻留在本地站点的 VPN 设备的公共 IP 地址。 或者，对于本地的 ExpressRoute，它是通过 ExpressRoute 的 VPN 设备的专用 IP 地址。

   如果启用了 BGP，它将应用于 Azure 中为此站点创建的所有连接。 在虚拟 WAN 上配置 BGP 等效于在 Azure VPN 网关上配置 BGP。 
   
   本地 BGP 对等地址*不得*与 VPN 到设备的 IP 地址或 VPN 站点的虚拟网络地址空间相同。 在 VPN 设备上对 BGP 对等节点 IP 使用不同的 IP 地址。 它可以是分配给该设备上环回接口的地址。 但是 *，它不可能是*APIPA （169.254）。*x*. .*x*） 地址。 在表示位置的相应本地网络网关中指定此地址。 有关 BGP 先决条件，请参阅[关于 Azure VPN 网关的 BGP](../vpn-gateway/vpn-gateway-bgp-overview.md)。

1. 选择 **"下一步"：查看 + 创建>** 以检查设置值并创建 VPN 站点。 如果选择要连接**的集线器**，则将在本地网络和中心 VPN 网关之间建立连接。

## <a name="3-update-the-vpn-connection-setting-to-use-expressroute"></a><a name="hub"></a>3. 更新 VPN 连接设置以使用 ExpressRoute

创建 VPN 站点并连接到集线器后，请使用以下步骤将连接配置为使用 ExpressRoute 专用对等互连：

1. 返回虚拟 WAN 资源页，然后选择中心资源。 或者从 VPN 站点导航到连接的集线器。
1. 在**连接**下，选择**VPN（站点到站点）。**
1. 通过 ExpressRoute 在 VPN 站点上选择省略号 （**...），** 然后选择 **"编辑 VPN 连接到此中心**"。
1. 对于**使用 Azure 专用 IP 地址**，请选择"**是**"。 该设置将集线器 VPN 网关配置为使用此连接网关上的集线器地址范围内的专用 IP 地址，而不是公共 IP 地址。 这将确保来自本地网络的流量遍历 ExpressRoute 专用对等路径，而不是使用此 VPN 连接使用公共 Internet。 以下屏幕截图显示了设置。

   ![用于为 VPN 连接使用专用 IP 地址的设置](./media/vpn-over-expressroute/vpn-link-configuration.png)
   
1. 选择“保存”。****

保存更改后，集线器 VPN 网关将使用 VPN 网关上的专用 IP 地址通过 ExpressRoute 与本地 VPN 设备建立 IPsec/IKE 连接。

## <a name="4-get-the-private-ip-addresses-for-the-hub-vpn-gateway"></a><a name="associate"></a>4. 获取集线器 VPN 网关的专用 IP 地址

下载 VPN 设备配置以获取集线器 VPN 网关的专用 IP 地址。 您需要这些地址来配置本地 VPN 设备。

1. 在集线器的页面上，选择**连接**下的**VPN（站点到站点）。**
1. 在 **"概述"** 页的顶部，选择 **"下载 VPN 配置**"。 

   Azure 在资源组中创建一个存储帐户"Microsoft-网络-位置"，*其中位置*是 WAN 的位置。 将配置应用于 VPN 设备后，可以删除此存储帐户。
1. 创建文件后，选择要下载该文件的链接。
1. 将配置应用到 VPN 设备。

### <a name="vpn-device-configuration-file"></a>VPN设备配置文件

设备配置文件包含配置本地 VPN 设备时要使用的设置。 查看此文件时，请留意以下信息：

* **vpnSite配置**：此部分表示作为连接到虚拟 WAN 的站点设置的设备详细信息。 它包括分支设备的名称和公共 IP 地址。
* **vpnSite连接**：本节提供有关以下设置的信息：

    * 虚拟中心虚拟网络的地址空间。<br/>示例：
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * 地址连接到集线器的虚拟网络的空间。<br>示例：
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * 虚拟中心 VPN 网关的 IP 地址。 由于 VPN 网关的每个连接由两个处于主动-主动配置中的隧道组成，因此您将看到此文件中列出的两个 IP 地址。 在此示例中，您可以看到`Instance0`每个站点，`Instance1`它们是专用 IP 地址，而不是公共 IP 地址。<br>示例：
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * VPN 网关连接的配置详细信息，如 BGP 和预共享密钥。 自动为您生成预共享密钥。 您可以随时在 **"概述"** 页上编辑自定义预共享密钥的连接。
  
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

* VPN 设备页面上的说明不是为虚拟 WAN 编写的。 但是，您可以使用配置文件中的虚拟 WAN 值手动配置 VPN 设备。 
* 用于 VPN 网关的可下载设备配置脚本不适合虚拟 WAN，因为配置不同。
* 新的虚拟广域网可以同时支持 IKEv1 和 IKEv2。
* 虚拟 WAN 只能使用基于路由的 VPN 设备和设备指令。

## <a name="5-view-your-virtual-wan"></a><a name="viewwan"></a>5. 查看虚拟广域网

1. 转到虚拟广域网。
1. 在“概述”**** 页上，地图中的每个点表示一个中心。
1. 在 **"集线器和连接**"部分中，您可以查看中心、站点、区域和 VPN 连接状态。 您还可以查看字节进出。

## <a name="7-monitor-a-connection"></a><a name="connectmon"></a>7. 监控连接

创建连接以监视 Azure 虚拟机 （VM） 和远程站点之间的通信。 有关如何设置连接监视器的信息，请参阅[监视网络通信](~/articles/network-watcher/connection-monitor.md)。 源字段是 Azure 中的 VM IP，目标 IP 是站点 IP。

## <a name="8-clean-up-resources"></a><a name="cleanup"></a>8. 清理资源

当您不再需要这些资源时，可以使用[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)删除资源组及其包含的所有资源。 运行以下 PowerShell 命令，然后`myResourceGroup`替换为资源组的名称：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤

本文可帮助您使用虚拟 WAN 通过 ExpressRoute 专用对等互连创建 VPN 连接。 要了解有关虚拟 WAN 和相关功能的详细信息，请参阅[虚拟 WAN 概述](virtual-wan-about.md)。
