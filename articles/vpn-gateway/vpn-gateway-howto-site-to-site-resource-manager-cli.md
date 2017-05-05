---
title: "将本地网络连接到 Azure 虚拟网络：站点到站点 VPN：CLI | Microsoft Docs"
description: "通过公共 Internet 创建从本地网络到 Azure 虚拟网络的 IPsec 连接的步骤。 这些步骤将帮助你使用 CLI 创建跨界站点到站点 VPN 网关连接。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: c3563f3a3fa46d40ba02fe97b3b0ebe3c45caddd
ms.lasthandoff: 04/26/2017


---
# <a name="create-a-virtual-network-with-a-site-to-site-vpn-connection-using-cli"></a>使用 CLI 创建具有站点到站点 VPN 连接的虚拟网络

本文介绍如何使用 Azure CLI 创建站点到站点 VPN 网关连接，以便从本地网络连接到 VNet。 本文中的步骤适用于 Resource Manager 部署模型。 也可使用不同的部署工具或部署模型来创建此配置，方法是从以下列表中选择另一选项：

> [!div class="op_single_selector"]
> * [Resource Manager - Azure 门户](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Resource Manager - CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [经典 - Azure 门户](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [经典 - 经典门户](vpn-gateway-site-to-site-create.md)
> 
>


![站点到站点 VPN 网关跨界连接示意图](./media/vpn-gateway-howto-site-to-site-resource-manager-cli/site-to-site-connection-diagram.png)

使用站点到站点 VPN 网关连接，通过 IPsec/IKE（IKEv1 或 IKEv2）VPN 隧道将本地网络连接到 Azure 虚拟网络。 此类型的连接要求位于本地的 VPN 设备分配有一个面向外部的公共 IP 地址。 有关 VPN 网关的详细信息，请参阅[关于 VPN 网关](vpn-gateway-about-vpngateways.md)。

## <a name="before-you-begin"></a>开始之前

在开始配置之前，请验证你是否符合以下条件：

* 验证你是否需要使用 Resource Manager 部署模型。 [!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 
* 一台兼容的 VPN 设备和能够对其进行配置的人员。 有关兼容的 VPN 设备和设备配置的详细信息，请参阅[关于 VPN 设备](vpn-gateway-about-vpn-devices.md)。
* 一个用于 VPN 设备的面向外部的公共 IPv4 地址。 此 IP 地址不得位于 NAT 之后。
* 如果不熟悉本地网络配置中的 IP 地址范围，则需咨询能够为你提供此类详细信息的人员。 创建此配置时，必须指定 IP 地址范围前缀，Azure 会将该前缀路由到本地位置。 本地网络的任何子网都不得与要连接到的虚拟网络子网重叠。 
* 最新版本的 CLI 命令（2.0 或更高版本）。 有关安装 CLI 命令的信息，请参阅 [Install Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)（安装 Azure CLI 2.0）。

### <a name="example-values"></a>示例值

可使用以下值创建测试环境，或参考这些值以更好地理解本文中的示例：

```
#Example values

VnetName                = TestVNet1 
ResourceGroup           = TestRG1 
Location                = eastus 
AddressSpace            = 10.12.0.0/16 
SubnetName              = Subnet1 
Subnet                  = 10.12.0.0/24 
GatewaySubnet           = 10.12.255.0/27 
LocalNetworkGatewayName = Site2 
LNG Public IP           = <VPN device IP address>
LocalAddrPrefix1        = 10.0.0.0/24 
LocalAddrPrefix2        = 20.0.0.0/24   
GatewayName             = VNet1GW 
PublicIP                = VNet1GWIP 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite2
```

## <a name="Login"></a>1.登录 Azure

使用 [az login](/cli/azure/#login) 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。

```azurecli
az login
```

如果有多个 Azure 订阅，请列出该帐户的订阅。

```azurecli
Az account list --all
```

指定要使用的订阅。

```azurecli
Az account set --subscription <replace_with_your_subscription_id>
```

## <a name="2-create-a-resource-group"></a>2.创建资源组

以下示例在“eastus”位置创建名为“TestRG1”的资源组。 如果你在需创建 VNet 的区域中已经有了一个资源组，则可改用该资源组。

```azurecli
az group create -n TestRG1 -l eastus
```

## <a name="VNet"></a>3.创建虚拟网络

如果还没有虚拟网络，请创建。 创建虚拟网络时，请确保你指定的地址空间不与本地网络的任一个地址空间相重叠。 

以下示例创建一个名为“TestVNet1”的虚拟网络和一个名为“Subnet1”的子网。

```azurecli
az network vnet create -n TestVNet1 -g TestRG1 --address-prefix 10.12.0.0/16 -l eastus --subnet-name Subnet1 --subnet-prefix 10.12.0.0/24
```

## 4.<a name="gwsub"></a>创建网关子网

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

对于此配置，你还需要一个网关子网。 虚拟网关使用的网关子网包含 VPN 网关服务使用的 IP 地址。 创建网关子网时，必须将其命名为“GatewaySubnet”。 如果将其命名为其他名称，也将创建子网，但 Azure 不将它视为网关子网。

指定的网关子网的大小取决于要创建的 VPN 网关配置。 尽管创建的网关子网最小可为 /29，但建议选择 /27 或 /28，创建包含更多地址的更大子网。 使用更大的网关子网可以有足够的 IP 地址来应对未来可能会有的配置。


```azurecli
az network vnet subnet create --address-prefix 10.12.255.0/27 -n GatewaySubnet -g TestRG1 --vnet-name TestVNet1
```

## <a name="localnet"></a>5.创建本地网关

本地网络网关通常是指本地位置。 你可以为站点提供一个名称供 Azure 引用，然后指定本地 VPN 设备的 IP 地址，以便创建一个连接来连接到该设备。 此外还可指定 IP 地址前缀，以便通过 VPN 网关将其路由到 VPN 设备。 指定的地址前缀是位于本地网络的前缀。 如果本地网络出现变化，可以轻松更新这些前缀。

使用以下值：

* *--gateway-ip-address* 是本地 VPN 设备的 IP 地址。 VPN 设备不能位于 NAT 之后。
* *--local-address-prefixes* 是本地地址空间。

以下示例演示了如何添加具有多个地址前缀的本地网关：

```azurecli
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

## <a name="PublicIP"></a>6.请求公共 IP 地址

请求一个公共 IP 地址，该地址将分配给虚拟网络 VPN 网关。 这是 IP 地址，你将 VPN 设备配置为连接到该地址。

Resource Manager 部署模型的虚拟网关目前使用动态分配方法，仅支持公共 IP 地址。 但是，这并不意味着 IP 地址会更改。 VPN 网关 IP 地址只在删除或重新创建网关时更改。 虚拟网关公共 IP 地址不会因为重新调整大小、重置或其他 VPN 网关内部维护/升级而更改。 

```azurecli
az network public-ip create -n VNet1GWIP -g TestRG1 --allocation-method Dynamic
```

## <a name="CreateGateway"></a>7.创建 VPN 网关

创建虚拟网络 VPN 网关。 创建 VPN 网关可能需要长达 45 分钟或更长时间才能完成。

使用以下值：

* 站点到站点配置的 *--gateway-type* 为 *Vpn*。 网关类型永远是你要实现的配置的特定类型。 有关详细信息，请参阅[网关类型](vpn-gateway-about-vpn-gateway-settings.md#gwtype)
* *--vpn-type* 可以是 *RouteBased*（在某些文档中称为动态网关）或 *PolicyBased*（在某些文档中称为静态网关）。 具体设置取决于要连接到的设备的要求。 有关 VPN 网关类型的详细信息，请参阅[关于 VPN 网关配置设置](vpn-gateway-about-vpn-gateway-settings.md#vpntype)。
* *--sku* 可以是 Basic、Standard 或 HighPerformance。 某些 SKU 存在配置限制。 有关详细信息，请参阅[网关 SKU](vpn-gateway-about-vpngateways.md#gateway-skus)。

运行此命令后，不会看到任何反馈或输出。 创建网关大约需要 45 分钟时间。

```azurecli
az network vnet-gateway create -n VNet1GW --public-ip-address VNet1GWIP -g TestRG1 --vnet TestVNet1 --gateway-type Vpn --vpn-type RouteBased --sku Standard --no-wait 
```

## <a name="VPNDevice"></a>8.配置 VPN 设备

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]
  若要查找虚拟网关的公共 IP 地址，请使用以下示例，将相关值替换为你自己的值： 为了方便阅读，对输出进行了格式化，以表格式显示一系列公共 IP。

  ```azurecli
  az network public-ip list -g TestRG1 -o table
  ```

## <a name="CreateConnection"></a>9.创建 VPN 连接

在虚拟网关和本地 VPN 设备之间创建站点到站点 VPN 连接。 请特别注意共享密钥值，该值必须与为 VPN 设备配置的共享密钥值相符。

```azurecli
az network vpn-connection create -n VNet1toSite2 -g TestRG1 --vnet-gateway1 VNet1GW -l eastus --shared-key abc123 --local-gateway2 Site2
```

在一小段时间后，将建立该连接。

## <a name="toverify"></a>10.验证 VPN 连接

[!INCLUDE [verify connection](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)] 

若要使用其他方法来验证连接，请参阅[验证 VPN 网关连接](vpn-gateway-verify-connection-resource-manager.md)。

## <a name="common-tasks"></a>常见任务

### <a name="to-view-local-network-gateways"></a>查看本地网关

```azurecli
az network local-gateway list --resource-group TestRG1
```

### <a name="modify"></a>修改本地网关的 IP 地址前缀
如果需要更改局域网网关的前缀，请使用以下说明。 每次进行更改时，必须指定前缀的完整列表，不能仅指定要更改的前缀。

- **如果指定了某个连接**，请使用以下示例。 指定前缀的完整列表，其中包含现有的前缀以及要添加的前缀。 在此示例中，10.0.0.0/24 和 20.0.0.0/24 已存在。 我们添加前缀 30.0.0.0/24 和 40.0.0.0/24。

  ```azurecli
  az network local-gateway update --local-address-prefixes 10.0.0.0/24 20.0.0.0/24 30.0.0.0/24 40.0.0.0/24 -n VNet1toSite2 -g TestRG1
  ```

- **如果未指定连接**，则使用的命令与用来创建本地网关的命令相同。 也可使用该命令来更新 VPN 设备的网关 IP 地址。 仅在尚无连接的情况下，才能使用此命令。 在此示例中，10.0.0.0/24、20.0.0.0/24、30.0.0.0/24 和 40.0.0.0/24 已存在。 我们仅指定需要保留的前缀。 此例中为 10.0.0.0/24 和 20.0.0.0/24。

  ```azurecli
  az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
  ```

### <a name="modifygwipaddress"></a>修改本地网关的 IP 地址

此配置中的 IP 地址是 VPN 设备的 IP 地址，你需要创建到该设备的连接。 如果 VPN 设备 IP 地址更改，可以修改该值。 可以更改 IP 地址，即使存在网关连接。

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 -n Site2 -g TestRG1
```

查看结果时，请验证是否包括了 IP 地址前缀。

  ```azurecli
  "localNetworkAddressSpace": { 
    "addressPrefixes": [ 
      "10.0.0.0/24", 
      "20.0.0.0/24", 
      "30.0.0.0/24" 
    ] 
  }, 
  "location": "eastus", 
  "name": "Site2", 
  "provisioningState": "Succeeded",  
  ```

### <a name="to-view-the-virtual-network-gateway-public-ip-address"></a>查看虚拟网关公共 IP 地址

若要查找虚拟网关的公共 IP 地址，请使用以下示例。 为了方便阅读，对输出进行了格式化，以表格式显示一系列公共 IP。

```azurecli
az network public-ip list -g TestRG1 -o table
```

### <a name="to-verify-the-shared-key-values"></a>验证共享密钥值

验证共享密钥值与用于 VPN 设备配置的值是否相同。 如果不同，请使用设备提供的值再次运行链接，或者使用返回的值更新设备。 值必须匹配。

```azurecli
az network vpn-connection shared-key show --connection-name VNet1toSite2 -g TestRG1
```

## <a name="next-steps"></a>后续步骤

*  连接完成后，即可将虚拟机添加到虚拟网络。 有关详细信息，请参阅[虚拟机](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)。
* 有关 BGP 的信息，请参阅 [BGP 概述](vpn-gateway-bgp-overview.md)和[如何配置 BGP](vpn-gateway-bgp-resource-manager-ps.md)。
* 有关强制隧道的信息，请参阅[配置强制隧道](vpn-gateway-forced-tunneling-rm.md)。
* 有关网络 Azure CLI 命令的列表，请参阅 [Azure CLI](https://docs.microsoft.com/cli/azure/network)。
