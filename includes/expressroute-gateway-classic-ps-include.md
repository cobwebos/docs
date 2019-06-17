---
title: include 文件
description: include 文件
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 12/13/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 70ac106995324c758bde942d12191a01e3457e6e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66121304"
---
> [!NOTE]
> 这些示例不适用于 S2S/ExpressRoute 共存配置。
> 有关使用共存配置中的网关的详细信息，请参阅[配置共存连接](../articles/expressroute/expressroute-howto-coexist-classic.md#gw)。

## <a name="add-a-gateway"></a>添加网关

向使用经典资源模型的虚拟网络添加网关时，你在创建网关之前直接修改网络配置文件。 该文件中必须存在以下示例中的值才能创建网关。 如果虚拟网络之前有与之关联的网关，则其中某些值已经存在。 请修改文件来反映下面的值。

### <a name="download-the-network-configuration-file"></a>下载网络配置文件

1. 使用[网络配置文件](../articles/virtual-network/virtual-networks-using-network-configuration-file.md)一文中的步骤下载网络配置文件。 使用文本编辑器打开该文件。
2. 向该文件中添加一个本地网络站点。 可以使用任何有效的地址前缀。 可以为 VPN 网关添加任何有效的 IP 地址。 此部分中的地址值不用于 ExpressRoute 操作，但是在进行文件验证时需要使用它们。 在示例中，“branch1”是站点的名称。 你可以使用其他名称，但一定要在该文件的 Gateway 节中使用同一值。

   ```
   <VirtualNetworkConfiguration>
    <Dns />
    <LocalNetworkSites>
      <LocalNetworkSite name="branch1">
        <AddressSpace>
          <AddressPrefix>165.3.1.0/27</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>3.2.1.4</VPNGatewayAddress>
    </LocalNetworkSite>
   ```
3. 导航到 VirtualNetworkSites 并修改字段。

   * 验证你的虚拟网络是否存在网关子网。 如果不存在，此时可以添加一个。 该名称必须是“GatewaySubnet”。
   * 验证文件的 Gateway 节是否存在。 如果不存在，请添加该节。 这是将虚拟网络与本地网络站点（它表示你要连接到的网络）进行关联所必需的。
   * 验证 connection type = Dedicated。 对于 ExpressRoute 连接，这是必需的。

   ```
   </LocalNetworkSites>
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myAzureVNET" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="default">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.1.0/27</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="branch1">
              <Connection type="Dedicated" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
   </VirtualNetworkConfiguration>
   </NetworkConfiguration>
   ```
4. 保存文件并将其上传到 Azure。

### <a name="create-the-gateway"></a>创建网关

使用以下命令可创建网关。 将所有值替换成你自己的值。

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>验证是否已创建网关

使用以下命令来验证是否已创建网关。 此命令还将检索执行其他操作所需的网关 ID。

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>重设网关大小

有许多[网关 SKU](../articles/expressroute/expressroute-about-virtual-network-gateways.md)。 可以使用以下命令随时更改网关 SKU。

> [!IMPORTANT]
> 此命令对 UltraPerformance 网关不起作用。 要将网关更改为 UltraPerformance 网关，首先要删除现有的 ExpressRoute 网关，然后创建新的 UltraPerformance 网关。 要将网关从 UltraPerformance 网关降级，首先要删除 UltraPerformance 网关，然后创建新网关。
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>删除网关

使用以下命令可删除网关

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```