---
title: 在 Azure 虚拟 WAN 中通过 ExpressRoute 专用对等互连创建站点到站点 VPN 连接 |Microsoft Docs
description: 在本教程中，了解如何使用 Azure 虚拟 WAN 通过 ExpressRoute 专用对等互连创建站点到站点 VPN 连接。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 10/11/2019
ms.author: cherylmc
Customer intent: I want to connect my on-premises networks to my VNets using S2S VPN connection over my ExpressRoute private peering using Azure Virtual WAN.
ms.openlocfilehash: 6272d6fe6f8c35c06a8121e10be2dd5a2e5512a8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515026"
---
# <a name="create-a-site-to-site-vpn-connection-over-expressroute-private-peering-using-azure-virtual-wan"></a>使用 Azure 虚拟 WAN 通过 ExpressRoute 专用对等互连创建站点到站点 VPN 连接

本文介绍如何使用虚拟 WAN 通过 ExpressRoute 线路的专用对等互连，建立从本地网络到 Azure 的 IPsec/IKE VPN 连接。 这可以通过 ExpressRoute 在本地网络与 Azure 虚拟网络之间提供加密传输，而无需通过公共 Internet 或使用公共 IP 地址。

## <a name="topology-and-routing"></a>拓扑和路由

下图显示了基于 ExpressRoute 的专用对等互连连接的示例：

![VPN over ExpressRoute](./media/vpn-over-expressroute/vwan-vpn-over-er.png)

此关系图显示了通过 ExpressRoute 专用对等互连连接到 Azure 中心 VPN 网关的本地网络中的网络。 连接建立非常简单：

1. 建立与 ExpressRoute 线路和专用对等互连的 ExpressRoute 连接
2. 按照本文档中所述建立 VPN 连接

此配置的一个重要方面是在本地网络与 Azure 之间通过 ExpressRoute 和 VPN 路径进行路由。

### <a name="traffic-from-on-premises-networks-to-azure"></a>从本地网络到 Azure 的流量

对于从本地网络到 Azure 的流量，Azure 前缀（包括虚拟中心和连接到中心的所有辐射虚拟网络）将通过 ExpressRoute 专用对等 BGP 和 VPN BGP 播发。 这将导致从本地网络到 Azure 的两个网络路由（路径）;一个在 IPsec 受保护的路径上，另一个直接通过 ExpressRoute**而不**受 ipsec 保护。 若要确保将加密应用于通信，必须确保在关系图中，对于连接了 VPN 的网络，通过直接 ExpressRoute 路径首选 Azure 路由。

### <a name="traffic-from-azure-to-on-premises-networks"></a>从 Azure 到本地网络的流量

同一要求适用于从 Azure 到本地网络的流量。 若要确保 IPsec 路径优于直接 ExpressRoute 路径（没有 IPsec），可以使用两个选项：

- 针对 VPN 连接的网络在 VPN BGP 会话上播发更具体的前缀。 你可以通过 ExpressRoute 专用对等互连在更大范围内播发包含 "VPN 连接的网络"，然后在 VPN BGP 会话中公布更具体的范围。 例如，通过 ExpressRoute 播发 10.0.0.0/16 和 VPN 上的 10.0.1.0/24。

- 为 VPN 和 ExpressRoute 播发非连续前缀。 如果 VPN 连接的网络范围与其他 ExpressRoute 连接网络断开连接，则可以分别在 VPN 和 ExpressRoute BGP 会话中公布前缀。 例如，通过 ExpressRoute 播发 10.0.0.0/24 和 10.0.1.0/24 over VPN。

在这两个示例中，Azure 将通过 VPN 连接将流量发送到 10.0.1.0/24，而不是直接通过无 VPN 保护的 ExpressRoute。

> [!WARNING]
> 如果在 ExpressRoute 和 VPN 连接上公布**相同**的前缀，Azure 将**直接使用 expressroute 路径，而无需 VPN 保护**。
>

## <a name="before-you-begin"></a>开始之前

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="openvwan"></a>1. 使用网关创建虚拟 WAN 和中心

在继续之前，必须准备好以下 Azure 资源和相应的本地配置：

1. Azure 虚拟 WAN
2. 具有[ExpressRoute 网关](virtual-wan-expressroute-portal.md)和[VPN 网关](virtual-wan-site-to-site-portal.md)的虚拟 WAN 集线器

有关创建 Azure 虚拟 WAN 和具有 ExpressRoute 关联的集线器的步骤，请参阅使用[Azure 虚拟 Wan 创建 ExpressRoute 关联](virtual-wan-expressroute-portal.md)，并[使用 Azure 虚拟广域网创建站点到站点连接](virtual-wan-site-to-site-portal.md)，以便创建 VPN虚拟 WAN 中的网关。

## <a name="site"></a>2. 为本地网络创建站点

站点资源与虚拟 WAN 的非 ExpressRoute VPN 站点相同。 需要注意的一点是，本地 VPN 设备的 IP 地址现在可以是专用 IP 地址，也可以是本地网络中的公共 IP 地址，可通过在步骤1中创建的 ExpressRoute 专用对等互连来访问。

> [!NOTE]
> 本地 VPN 设备 IP 地址必须是通过 Azure ExpressRoute 专用对等互连播发到虚拟 WAN 集线器的地址前缀的一部分。
>

1. 在浏览器中导航到 Azure 门户。 单击已创建的 WAN。 在 "WAN" 页的 "**连接**" 下，单击 " **vpn 站点**" 以打开 "vpn 站点" 页。

2. 在“VPN 站点”页上，单击“+创建站点”。

3. 在“创建站点”页上填写以下字段：

   * **订阅** - 验证订阅。
   * **资源组**-选择或创建要使用的资源组。
   * **区域**-VPN 站点资源的 Azure 区域。
   * **名称** - 本地站点的名称。
   * **设备供应商**-本地 VPN 设备的供应商。
   * **边界网关协议**-如果本地网络使用 BGP，请选择 "启用"。
   * **专用地址空间** - 位于本地站点的 IP 地址空间。 目标为此地址空间的流量通过 VPN 网关路由到本地网络。
   * **中心**-选择一个或多个中心以连接此 VPN 站点。 所选中心必须已创建 VPN 网关。

4. 单击 "**下一步"：** VPN 链接设置的链接 >：

   * **链接名称**-要用于引用此连接的名称。
   * **提供程序名称**-此站点的 Internet 服务提供商的名称。 对于 ExpressRoute 本地网络，ExpressRoute 服务提供商的名称。
   * **速度**-Internet 服务链接或 ExpressRoute 线路的速度。
   * **IP 地址**-位于本地站点的 VPN 设备的公共 IP 地址。 或者，对于 ExpressRoute 本地，通过 ExpressRoute 的 VPN 设备的专用 IP 地址。

   如果启用了 BGP，它将应用于在 Azure 中为此站点创建的所有连接。 在虚拟 WAN 上配置 BGP 就等同于在 Azure VPN 网关上配置 BGP。 本地 BGP 对等机地址*不*能与 vpn 到设备的 IP 地址或 vpn 站点的 VNet 地址空间相同。 在 VPN 设备上对 BGP 对等节点 IP 使用不同的 IP 地址。 它可以是分配给该设备上环回接口的地址。 但是，该地址不能是 APIPA (169.254.*x*.*x*) 地址。 在表示该位置的相应本地网关中指定此地址。 有关 BGP 先决条件，请参阅[关于 Azure VPN 网关的 BGP](../vpn-gateway/vpn-gateway-bgp-overview.md)。

5. 单击 "**下一步"：查看和创建 >** 以检查设置值并创建 VPN 站点。 如果选择了 "要连接的**集线器**"，则将在本地网络和中心 VPN 网关之间建立连接。

## <a name="hub"></a>3. 更新 VPN 连接设置以使用 ExpressRoute

创建 VPN 站点并连接到中心后，请使用以下步骤将连接配置为使用 ExpressRoute 专用对等互连：

1. 返回到 "虚拟 WAN 资源" 页，然后单击 "中心" 资源。 或从 VPN 站点导航到连接的中心。

2. 在 "**连接**" 下，单击 " **VPN （站点到站点）** "

3. 单击 "..."在基于 ExpressRoute 的 VPN 站点上，选择 "**编辑到此集线器的 VPN 连接**"

4. 选择 "**使用 Azure 专用 IP 地址**" 上的 "是"。 此设置将集线器 VPN 网关配置为使用此连接的网关上的中心地址范围内的专用 IP 地址，而不是使用公共 IP 地址。 这将确保来自本地网络的流量遍历 ExpressRoute 专用对等互连路径，而不是使用公共 Internet 进行此 VPN 连接。 以下屏幕截图显示了 "设置" 窗口。

   ![VPN 连接设置](./media/vpn-over-expressroute/vpn-link-configuration.png)
   
5. 单击“保存”。

保存后，集线器 VPN 网关将使用 VPN 网关上的专用 IP 地址，通过 ExpressRoute 与本地 VPN 设备建立 IPsec/IKE 连接。

## <a name="associate"></a>4. 获取中心 VPN 网关专用 IP 地址

下载 VPN 设备配置以获取中心 VPN 网关的专用 IP 地址。 需要配置本地 VPN 设备。

1. 在中心的页面上，单击 "**连接**" 下的 " **VPN （站点到站点）** "

2. 在 "概述" 页的顶部，单击 "**下载 VPN 配置**"。Azure 会在资源组 "microsoft 网络-[location]" 中创建一个存储帐户，其中 location 是 WAN 的位置。 将配置应用到 VPN 设备后，可以删除此存储帐户。

3. 完成创建文件后，可以单击相应的链接下载该文件。

4. 将配置应用到 VPN 设备。

### <a name="understanding-the-vpn-device-configuration-file"></a>了解 VPN 设备配置文件

设备配置文件包含配置本地 VPN 设备时要使用的设置。 查看此文件时，请留意以下信息：

* **vpnSiteConfiguration** - 此部分表示当站点连接到虚拟 WAN 时设置的设备详细信息。 它包含分支设备的名称和公共 IP 地址。
* **vpnSiteConnections** - 此部分提供以下设置的信息：

    * 虚拟中心 VNet 的**地址空间**<br/>示例：
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * 已连接到中心的 VNet 的**地址空间**<br>示例：
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * 虚拟中心 vpngateway 的 IP 地址。 由于 vpngateway 的每个连接由采用主动 - 主动配置的 2 个隧道构成，因此，此文件中列出了这两个 IP 地址。 在此示例中，你将看到每个站点的 "Instance0" 和 "Instance1"，它们是专用 IP 地址而不是公共 IP 地址。<br>示例：
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * **Vpngateway 连接配置详细信息**，例如 BGP、预共享密钥等等。PSK 是自动为您生成的预共享密钥。 始终可以在“概述”页中为自定义 PSK 编辑连接。
  
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

* VPN 设备页上的说明不是针对虚拟 WAN 编写的，但你可以使用配置文件中的虚拟 WAN 值来手动配置 VPN 设备。 
* 适用于 VPN 网关的可下载设备配置脚本并不适用于虚拟 WAN，因为配置不同。
* 新的虚拟 WAN 可以同时支持 IKEv1 和 IKEv2。
* 虚拟 WAN 只能使用基于路由的 VPN 设备和设备说明。

## <a name="viewwan"></a>5. 查看虚拟 WAN

1. 导航到虚拟 WAN。

2. 在“概述”页上，地图中的每个点表示一个中心。 将鼠标悬停在任一点上可以查看中心运行状况的摘要。

3. 在“中心和连接”部分，可以查看中心状态、站点、区域、VPN 连接状态和传入与传出字节数。

## <a name="viewhealth"></a>6. 查看资源运行状况

1. 导航到 WAN。

2. 在“WAN”页上的“支持 + 故障排除”部分，单击“运行状况”并查看资源。

## <a name="connectmon"></a>7. 监视连接

创建一个连接，用于监视 Azure VM 与远程站点之间的通信。 有关如何设置连接监视器的信息，请参阅[监视网络通信](~/articles/network-watcher/connection-monitor.md)。 源字段是 Azure 中的 VM IP，目标 IP 是站点 IP。

## <a name="cleanup"></a>8. 清理资源

不再需要这些资源时，可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 删除资源组及其包含的所有资源。 将“myResourceGroup”替换为资源组的名称，并运行以下 PowerShell 命令：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤

本文介绍如何使用虚拟 WAN 通过 ExpressRoute 专用对等互连创建 VPN 连接。 若要了解有关虚拟 WAN 和其他相关功能的详细信息，请参阅[虚拟 Wan 概述](virtual-wan-about.md)页。
