---
title: 配置 ExpressRoute 加密： IPsec over ExpressRoute for Azure 虚拟 WAN
description: 本教程介绍如何使用 Azure 虚拟 WAN 通过 ExpressRoute 专用对等互连创建站点到站点 VPN 连接。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/19/2020
ms.author: cherylmc
ms.openlocfilehash: d3a2c85540a4efa2f934605c4c056b458e2879ca
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84749602"
---
# <a name="expressroute-encryption-ipsec-over-expressroute-for-virtual-wan"></a>ExpressRoute 加密：虚拟 WAN 的基于 ExpressRoute 的 IPsec

本文介绍如何使用 Azure 虚拟 WAN 通过 Azure ExpressRoute 线路的专用对等互连建立从本地网络到 Azure 的 IPsec/IKE VPN 连接。 此方法可以通过 ExpressRoute 在本地网络与 Azure 虚拟网络之间提供加密的传输，而无需通过公共 Internet 进行传输或使用公共 IP 地址。

## <a name="topology-and-routing"></a>拓扑和路由

下图显示了通过 ExpressRoute 专用对等互连建立的 VPN 连接的示例：

![通过 ExpressRoute 建立的 VPN 连接](./media/vpn-over-expressroute/vwan-vpn-over-er.png)

该图显示了通过 ExpressRoute 专用对等互连连接到 Azure 中心 VPN 网关的本地网络中的某个网络。 连接的建立非常直接：

1. 与 ExpressRoute 线路和专用对等互连建立 ExpressRoute 连接。
2. 根据本文中所述建立 VPN 连接。

此配置的一个重要方面是通过 ExpressRoute 和 VPN 路径在本地网络与 Azure 之间进行路由。

### <a name="traffic-from-on-premises-networks-to-azure"></a>从本地网络发往 Azure 的流量

对于从本地网络发往 Azure 的流量，Azure 前缀（包括虚拟中心以及连接到中心的所有辐射虚拟网络）通过 ExpressRoute 专用对等互连 BGP 和 VPN BGP 进行播发。 这会建立从本地网络到 Azure 的两个网络路由（路径）：

- 一个路由是通过受 IPsec 保护的路径建立的
- 一个路由是通过不受 IPsec 保护的 ExpressRoute 直接建立的 

若要将加密应用于通信，必须确保对于图中所示的已连接 VPN 的网络，优先使用通过本地 VPN 网关建立的 Azure 路由，而不是通过直接 ExpressRoute 路径建立的路由。

### <a name="traffic-from-azure-to-on-premises-networks"></a>从 Azure 发往本地网络的流量

相同的要求适用于从 Azure 发往本地网络的流量。 为了确保优先使用 IPsec 路径而不是直接 ExpressRoute 路径（不受 IPsec 保护），可以采用两种做法：

- 在已连接 VPN 的网络的 VPN BGP 会话中播发更具体的前缀。 可以通过 ExpressRoute 专用对等互连播发包含已连接 VPN 的网络的更大范围，然后在 VPN BGP 会话中播发更具体的范围。 例如，通过 ExpressRoute 播发 10.0.0.0/16，通过 VPN 播发 10.0.1.0/24。

- 为 VPN 和 ExpressRoute 播发不相交的前缀。 如果已连接 VPN 的网络范围与已连接 ExpressRoute 的其他网络不相交，则可以分别在 VPN 和 ExpressRoute BGP 会话中播发这些前缀。 例如，通过 ExpressRoute 播发 10.0.0.0/24，通过 VPN 播发 10.0.1.0/24。

在这两个示例中，Azure 将通过 VPN 连接将流量发送到 10.0.1.0/24，而不是直接通过不受 VPN 保护的 ExpressRoute 发送。

> [!WARNING]
> 如果通过 ExpressRoute 和 VPN 连接播发相同的前缀，Azure 将直接使用不受 VPN 保护的 ExpressRoute 路径。
>

## <a name="before-you-begin"></a>准备阶段

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="1-create-a-virtual-wan-and-hub-with-gateways"></a><a name="openvwan"></a>1.创建带有网关的虚拟 WAN 和中心

在继续操作之前，必须准备好以下 Azure 资源和相应的本地配置：

- 一个 Azure 虚拟 WAN
- 一个带有 [ExpressRoute 网关](virtual-wan-expressroute-portal.md)和 [VPN 网关](virtual-wan-site-to-site-portal.md)的虚拟 WAN 中心

有关创建带有 ExpressRoute 关联的 Azure 虚拟 WAN 和中心的步骤，请参阅[使用 Azure 虚拟 WAN 创建 ExpressRoute 关联](virtual-wan-expressroute-portal.md)。 有关在虚拟 WAN 中创建 VPN 网关的步骤，请参阅[使用 Azure 虚拟 WAN 创建站点到站点连接](virtual-wan-site-to-site-portal.md)。

## <a name="2-create-a-site-for-the-on-premises-network"></a><a name="site"></a>2.为本地网络创建站点

站点资源与虚拟 WAN 的非 ExpressRoute VPN 站点相同。 本地 VPN 设备的 IP 地址现在可以是专用 IP 地址，或者是可通过步骤 1 中创建的 ExpressRoute 专用对等互连访问的本地网络中的公共 IP 地址。

> [!NOTE]
> 本地 VPN 设备的 IP 地址必须是通过 Azure ExpressRoute 专用对等互连播发到虚拟 WAN 中心的地址前缀的一部分。
>

1. 在浏览器中转到 Azure 门户。 
1. 选择创建的 WAN。 在“WAN”页上的“连接”下，选择“VPN 站点”。 
1. 在“VPN 站点”页上，选择“+创建站点”。 
1. 在“创建站点”页上填写以下字段：
   * **订阅**：验证订阅。
   * **资源组**：选择或创建要使用的资源组。
   * **区域**：输入 VPN 站点资源的 Azure 区域。
   * **名称**：输入用于指代你的本地站点的名称。
   * **设备供应商**：输入本地 VPN 设备的供应商。
   * **边界网关协议**：如果本地网络使用 BGP，请选择“启用”。
   * **专用地址空间**：输入位于本地站点上的 IP 地址空间。 发往此地址空间的流量将通过 VPN 网关路由到本地网络。
   * **中心**：选择要连接此 VPN 站点的一个或多个中心。 选定的中心必须已创建了 VPN 网关。
1. 在完成时选择“下一步:链接 >”完成 VPN 链接设置：
   * **链接名称**：用于指代此连接的名称。
   * **提供商名称**：此站点的 Internet 服务提供商的名称。 对于 ExpressRoute 本地网络，该名称是 ExpressRoute 服务提供商的名称。
   * **速度**：Internet 服务链接或 ExpressRoute 线路的速度。
   * **IP 地址**：驻留在本地站点上的 VPN 设备的公共 IP 地址。 对于本地 ExpressRoute，它是通过 ExpressRoute 连接的 VPN 设备的专用 IP 地址。

   如果启用了 BGP，BGP 将应用到在 Azure 中为此站点创建的所有连接。 在虚拟 WAN 上配置 BGP 等同于在 Azure VPN 网关上配置 BGP。 
   
   本地 BGP 对等方地址不能与连接到设备的 VPN 的 IP 地址或 VPN 站点的虚拟网络地址空间相同。 在 VPN 设备上对 BGP 对等节点 IP 使用不同的 IP 地址。 它可以是分配给该设备上环回接口的地址。 但是，该地址不能是 APIPA (169.254.*x*.*x*) 地址。 在代表该位置的相应本地网关中指定此地址。 有关 BGP 先决条件，请参阅[关于 Azure VPN 网关的 BGP](../vpn-gateway/vpn-gateway-bgp-overview.md)。

1. 在完成时选择“下一步:查看 + 创建 >”检查设置值并创建 VPN 站点。 如果选择了要连接的**中心**，则连接将在本地网络与中心 VPN 网关之间建立。

## <a name="3-update-the-vpn-connection-setting-to-use-expressroute"></a><a name="hub"></a>3.将 VPN 连接设置更新为使用 ExpressRoute

创建 VPN 站点并连接到中心后，使用以下步骤将连接配置为使用 ExpressRoute 专用对等互连：

1. 返回到虚拟 WAN 资源页，选择中心资源。 或者从 VPN 站点导航到已连接的中心。
1. 在“连接”下，选择“VPN (站点到站点)”。 
1. 选择通过 ExpressRoute 连接的 VPN 站点对应的省略号 ( **...** )，然后选择“编辑指向此中心的 VPN 连接”。
1. 对于“使用 Azure 专用 IP 地址”，请选择“是”。  此设置将中心 VPN 网关配置为对此连接使用网关上的中心地址范围内的专用 IP 地址，而不是使用公共 IP 地址。 这将确保来自本地网络的流量通过 ExpressRoute 专用对等互连路径，而不是对此 VPN 连接使用公共 Internet。 以下屏幕截图显示了该设置。

   ![将专用 IP 地址用于 VPN 连接的设置](./media/vpn-over-expressroute/vpn-link-configuration.png)
   
1. 选择“保存” 。

保存更改后，中心 VPN 网关将使用 VPN 网关上的专用 IP 地址，通过 ExpressRoute 来与本地 VPN 设备建立 IPsec/IKE 连接。

## <a name="4-get-the-private-ip-addresses-for-the-hub-vpn-gateway"></a><a name="associate"></a>4.获取中心 VPN 网关的专用 IP 地址

下载 VPN 设备配置，以获取中心 VPN 网关的专用 IP 地址。 需要使用这些地址来配置本地 VPN 设备。

1. 在中心的页面上，选择“连接”下的“VPN (站点到站点)”。 
1. 在“概述”页的顶部，选择“下载 VPN 配置”。  

   Azure 将在资源组“microsoft-network-[location]”中创建一个存储帐户，其中，*location* 是 WAN 的位置。 将配置应用到 VPN 设备后，可以删除此存储帐户。
1. 创建文件后，选择相应的链接下载该文件。
1. 将配置应用到 VPN 设备。

### <a name="vpn-device-configuration-file"></a>VPN 设备配置文件

设备配置文件包含配置本地 VPN 设备时要使用的设置。 查看此文件时，请留意以下信息：

* **vpnSiteConfiguration**：此节表示设置为要连接到虚拟 WAN 的站点的设备详细信息。 其中包括分支设备的名称和公共 IP 地址。
* **vpnSiteConnections**：此节提供有关以下设置的信息：

    * 虚拟中心的虚拟网络的地址空间。<br/>示例：
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * 已连接到中心的虚拟网络的地址空间。<br>示例：
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * 虚拟中心 VPN 网关的 IP 地址。 由于 VPN 网关的每个连接由采用“主动 - 主动”配置的 2 个隧道构成，因此，此文件中列出了这两个 IP 地址。 在此示例中，你会看到每个站点的 `Instance0` 和 `Instance1`，它们是专用 IP 地址而不是公共 IP 地址。<br>示例：
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * VPN 网关连接的配置详细信息，例如 BGP 和预共享密钥。 预共享密钥是系统自动生成的。 始终可以在自定义预共享密钥的“概述”页上编辑连接。
  
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

* VPN 设备页上的说明不是针对虚拟 WAN 编写的。 但你可以使用配置文件中的虚拟 WAN 值来手动配置 VPN 设备。 
* 适用于 VPN 网关的可下载设备配置脚本并不适用于虚拟 WAN，因为配置不同。
* 新的虚拟 WAN 可以支持 IKEv1 和 IKEv2。
* 虚拟 WAN 只能使用基于路由的 VPN 设备和设备说明。

## <a name="5-view-your-virtual-wan"></a><a name="viewwan"></a>5.查看虚拟 WAN

1. 转到虚拟 WAN。
1. 在“概述”页上，地图中的每个点表示一个中心。
1. 在“中心和连接”部分，可以查看中心、站点、区域和 VPN 连接状态。 还可以查看传入与传出的字节数。

## <a name="7-monitor-a-connection"></a><a name="connectmon"></a>7.监视连接

创建一个连接，用于监视 Azure 虚拟机 (VM) 与远程站点之间的通信。 有关如何设置连接监视器的信息，请参阅[监视网络通信](~/articles/network-watcher/connection-monitor.md)。 源字段是 Azure 中的 VM IP，目标 IP 是站点 IP。

## <a name="8-clean-up-resources"></a><a name="cleanup"></a>8.清理资源

不再需要这些资源时，可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 删除资源组及其包含的所有资源。 运行以下 PowerShell 命令（请将 `myResourceGroup` 替换为资源组的名称）：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤

本文帮助你使用虚拟 WAN 通过 ExpressRoute 专用对等互连创建 VPN 连接。 若要详细了解虚拟 WAN 和相关功能，请参阅[虚拟 WAN 概述](virtual-wan-about.md)。
