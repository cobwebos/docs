---
title: 使用点到站点和 RADIUS 身份验证将计算机连接到虚拟网络：PowerShell | Microsoft Docs
description: 使用 P2S 和 RADIUS 身份验证将 Windows 和 Mac OS X 客户端安全地连接到虚拟网络。
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2018
ms.author: anzaman
ms.openlocfilehash: df7afe9324831ffb8e79d7320f2c716ed18a7b4f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38719679"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-radius-authentication-powershell"></a>使用 RADIUS 身份验证配置 VNet 的点到站点连接：PowerShell

本文介绍如何创建一个 VNet，其中具有使用 RADIUS 身份验证的点到站点连接。 此配置仅适用于资源管理器部署模型。

点到站点 (P2S) VPN 网关用于创建从单个客户端计算机到虚拟网络的安全连接。 要从远程位置连接到 VNet，例如从家里或会议室进行远程通信，则可使用点到站点 VPN。 如果只有一些客户端需要连接到 VNet，则可使用 P2S VPN 这种解决方案来代替站点到站点 VPN。

P2S VPN 连接是从 Windows 和 Mac 设备启动的。 连接方客户端可以使用以下身份验证方法：

* RADIUS 服务器
* VPN 网关本机证书身份验证

借助本文可以配置一个使用 RADIUS 服务器进行身份验证的 P2S 配置。 如果想要改用生成的证书和 VPN 网关本机证书身份验证进行身份验证，请参阅[使用 VPN 网关本机证书身份验证配置 VNet 的点到站点连接](vpn-gateway-howto-point-to-site-rm-ps.md)。

![连接关系图 - RADIUS](./media/point-to-site-how-to-radius-ps/p2sradius.png)

点到站点连接不需要 VPN 设备或面向公众的 IP 地址。 P2S 基于 SSTP（安全套接字隧道协议）或 IKEv2 创建 VPN 连接。

* SSTP 是基于 SSL 的 VPN 隧道，仅在 Windows 客户端平台上受支持。 它可以穿透防火墙，这使得它成为一个可用来从任何位置连接到 Azure 的理想选项。 在服务器端，我们支持 SSTP 1.0、1.1 和 1.2 版。 客户端决定要使用的版本。 对于 Windows 8.1 及更高版本，SSTP 默认使用 1.2。

* IKEv2 VPN，这是一种基于标准的 IPsec VPN 解决方案。 IKEv2 VPN 可用于从 Mac 设备进行连接（OSX 10.11 和更高版本）。

P2S 连接有以下要求：

* RouteBased VPN 网关。 
* 用于处理用户身份验证的 RADIUS 服务器。 可将 RADIUS 服务器部署在本地或 Azure VNet 中。
* 要连接到 VNet 的 Windows 设备的 VPN 客户端配置包。 VPN 客户端配置包提供 VPN 客户端通过 P2S 进行连接所需的设置。

## <a name="aboutad"></a>关于 P2S VPN 的 Active Directory (AD) 域身份验证

AD 域身份验证可让用户使用其组织域凭据登录到 Azure。 它需要一台与 AD 服务器集成的 RADIUS 服务器。 组织也可以利用其现有的 RADIUS 部署。
 
RADIUS 服务器可以驻留在本地或 Azure VNet 中。 在身份验证期间，VPN 网关充当传递设备，在 RADIUS 服务器与连接方设备之间来回转发身份验证消息。 VPN 网关必须能够访问 RADIUS 服务器。 如果 RADIUS 服务器位于本地，需要建立从 Azure 到本地站点的 VPN 站点到站点连接。

除了 Active Directory 以外，RADIUS 服务器还能与其他外部标识系统集成。 这样就为点到站点 VPN 提供了大量的身份验证选项，包括 MFA 选项。 请查看 RADIUS 服务器供应商文档，获取该服务器可集成的标识系统列表。

![连接关系图 - RADIUS](./media/point-to-site-how-to-radius-ps/radiusimage.png)

> [!IMPORTANT]
>只能使用 VPN 站点到站点连接来与本地 RADIUS 服务器建立连接， 而不能使用 ExpressRoute 连接。
>
>

## <a name="before"></a>开始之前

* 确保拥有 Azure 订阅。 如果还没有 Azure 订阅，可以激活 [MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或注册获取[免费帐户](https://azure.microsoft.com/pricing/free-trial)。

* 安装最新版本的 Azure 资源管理器 PowerShell cmdlet。 有关安装 PowerShell cmdlet 的详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。

### <a name="log-in"></a>登录

[!INCLUDE [Log in](../../includes/vpn-gateway-ps-login-include.md)]

### <a name="example"></a>示例值

可使用示例值创建测试环境，或参考这些值以更好地理解本文中的示例。 可以将这些步骤用作演练并使用这些值而不更改它们，也可以更改这些值以反映自己的环境。

* **名称：VNet1**
* **地址空间：192.168.0.0/16** 和 **10.254.0.0/16**<br>本示例中使用了多个地址空间，说明此配置可与多个地址空间一起使用。 但是，对于此配置，多个地址空间并不必要。
* **子网名称：FrontEnd**
  * **子网地址范围：192.168.1.0/24**
* **子网名称：BackEnd**
  * **子网地址范围：10.254.1.0/24**
* **子网名称：GatewaySubnet**<br>要使 VPN 网关正常工作，必须使用子网名称 GatewaySubnet。
  * **GatewaySubnet 地址范围：192.168.200.0/24** 
* **VPN 客户端地址池：172.16.201.0/24**<br>使用此点到站点连接连接到 VNet 的 VPN 客户端接收来自 VPN 客户端地址池的 IP 地址。
* **订阅：** 如果有多个订阅，请验证是否正在使用正确的订阅。
* **资源组：TestRG**
* **位置：美国东部**
* **DNS 服务器**：用于 VNet 名称解析的 DNS 服务器的 IP 地址。 （可选）
* **GW 名称：Vnet1GW**
* **公共 IP 名称：VNet1GWPIP**
* **VpnType：RouteBased** 

## 1.<a name="vnet"></a>创建资源组、VNet 和公共 IP 地址

以下步骤在包含三个子网的资源组中创建资源组和虚拟网络。 替换值时，请务必始终将网关子网特意命名为“GatewaySubnet”。 如果命名为其他名称，网关创建会失败；

1. 创建资源组。

  ```powershell
  New-AzureRmResourceGroup -Name "TestRG" -Location "East US"
  ```
2. 为虚拟网络创建子网配置，并将其命名为 FrontEnd、BackEnd 和 GatewaySubnet。 这些前缀必须是已声明的 VNet 地址空间的一部分。

  ```powershell
  $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name "FrontEnd" -AddressPrefix "192.168.1.0/24"  
  $besub = New-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.254.1.0/24"  
  $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "192.168.200.0/24"
  ```
3. 创建虚拟网络。

  在本示例中，-DnsServer 服务器参数是可选的。 指定一个值不会创建新的 DNS 服务器。 指定的 DNS 服务器 IP 地址应该是可以解析从 VNet 所连接到的资源名称的 DNS 服务器。 对于此示例，我们使用了专用 IP 地址，但这可能不是你 DNS 服务器的 IP 地址。 请务必使用自己的值。 指定的值将由部署到 VNet 的资源使用，而不是由 P2S 连接使用。

  ```powershell
  New-AzureRmVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG" -Location "East US" -AddressPrefix "192.168.0.0/16","10.254.0.0/16" -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
  ```
4. VPN 网关必须具有公共 IP 地址。 请先请求 IP 地址资源，然后在创建虚拟网关时参阅该资源。 创建 VPN 网关时，IP 地址是动态分配给资源的。 VPN 网关当前仅支持动态公共 IP 地址分配。 不能请求静态公共 IP 地址分配。 但这并不意味着 IP 地址在分配到 VPN 网关后会更改。 公共 IP 地址只在删除或重新创建网关时更改。 该地址不会因为 VPN 网关大小调整、重置或其他内部维护/升级而更改。

  指定用于请求动态分配的公共 IP 地址的变量。

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG"  
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet 
  $pip = New-AzureRmPublicIpAddress -Name "VNet1GWPIP" -ResourceGroupName "TestRG" -Location "East US" -AllocationMethod Dynamic 
  $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwipconf" -Subnet $subnet -PublicIpAddress $pip
  ```

## 2.<a name="radius"></a>设置 RADIUS 服务器

在创建和配置虚拟网络网关之前，应该正确配置 RADIUS 服务器，以便能够进行身份验证。

1. 如果未部署 RADIUS 服务器，请部署一个。 有关部署步骤，请参阅 RADIUS 供应商提供的设置指南。  
2. 将 VPN 网关配置为 RADIUS 上的 RADIUS 客户端。 添加此 RADIUS 客户端时，请指定创建的虚拟网络 GatewaySubnet。 
3. 设置 RADIUS 服务器后，获取 RADIUS 服务器的 IP 地址和共享机密，RADIUS 客户端将使用这些信息来与 RADIUS 服务器通信。 如果 RADIUS 服务器在 Azure VNet 中，请使用 RADIUS 服务器 VM 的 CA IP。

[网络策略服务器 (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) 一文提供了有关为 AD 域身份验证配置 Windows RADIUS 服务器 (NPS) 的指导。

## 3.<a name="creategw"></a>创建 VPN 网关

为 VNet 配置和创建 VPN 网关。

* -GatewayType 必须是“Vpn”，-VpnType 必须是“RouteBased”。
* VPN 网关可能需要长达 45 分钟的时间才能完成，具体取决于所选的 [网关 SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku) 。

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1
```

## 4.<a name="addradius"></a>添加 RADIUS 服务器和客户端地址池
 
* 可按名称或 IP 地址指定 -RadiusServer。 如果指定名称并且服务器驻留在本地，VPN 网关可能无法解析名称。 如果出现这种情况，最好是指定服务器的 IP 地址。 
* -RadiusSecret 应该与 RADIUS 服务器上配置的值匹配。
* -VpnCientAddressPool 是连接方 VPN 客户端在连接时要从中接收 IP 地址的范围。 使用专用 IP 地址范围时，该范围不得与要通过其进行连接的本地位置重叠，也不得与要连接到其中的 VNet 重叠。 请确保配置足够大的地址池。  

1. 为 RADIUS 机密创建安全字符串。

  ```powershell
  $Secure_Secret=Read-Host -AsSecureString -Prompt "RadiusSecret"
  ```

2. 系统会提示输入 RADIUS 机密。 输入的字符不会显示，而是被“*”字符取代。

  ```powershell
  RadiusSecret:***
  ```
3. 添加 VPN 客户端地址池和 RADIUS 服务器信息。

  对于 SSTP 配置：

    ```powershell
    $Gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "SSTP" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

  对于 IKEv2 配置：

    ```powershell
    $Gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "IKEv2" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

  对于 SSTP + IKEv2

    ```powershell
    $Gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol @( "SSTP", "IkeV2" ) `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

## 5.<a name="vpnclient"></a>下载 VPN 客户端配置包并设置 VPN 客户端

VPN 客户端配置可让设备通过 P2S 连接来与 VNet 建立连接。 若要生成 VPN 客户端配置包并设置 VPN 客户端，请参阅[为 RADIUS 身份验证创建 VPN 客户端配置](point-to-site-vpn-client-configuration-radius.md)。

## <a name="connect"></a>6.连接到 Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>从 Windows VPN 客户端进行连接

1. 若要连接到 VNet，请在客户端计算机上导航到 VPN 连接，找到创建的 VPN 连接。 其名称与虚拟网络的名称相同。 输入域凭据，单击“连接”。 此时会显示一条弹出消息，请求以提升的权限操作。 请接受请求并输入凭据。

  ![VPN 客户端连接到 Azure](./media/point-to-site-how-to-radius-ps/client.png)
2. 连接已建立。

  ![已建立连接](./media/point-to-site-how-to-radius-ps/connected.png)

### <a name="connect-from-a-mac-vpn-client"></a>从 Mac VPN 客户端进行连接

在“网络”对话框中，找到要使用的客户端配置文件，单击“连接”。

  ![Mac 连接](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="verify"></a>验证连接

1. 要验证 VPN 连接是否处于活动状态，请打开提升的命令提示符，然后运行 *ipconfig/all*。
2. 查看结果。 请注意，收到的 IP 地址是在配置中指定的点到站点 VPN 客户端地址池中的地址之一。 结果与以下示例类似：

  ```
  PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
  ```

若要对 P2S 连接进行故障排除，请参阅[排查 Azure 点到站点连接问题](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)。

## <a name="connectVM"></a>连接到虚拟机

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="faq"></a>常见问题解答

本“常见问题解答”文章适用于使用 RADIUS 身份验证的 P2S

[!INCLUDE [Point-to-Site RADIUS FAQ](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>后续步骤

连接完成后，即可将虚拟机添加到虚拟网络。 有关详细信息，请参阅[虚拟机](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)。 若要详细了解网络和虚拟机，请参阅 [Azure 和 Linux VM 网络概述](../virtual-machines/linux/azure-vm-network-overview.md)。
