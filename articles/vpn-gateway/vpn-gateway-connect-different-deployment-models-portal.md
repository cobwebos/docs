---
title: 将经典虚拟网络连接到 Azure 资源管理器 VNet：门户 | Microsoft Docs
description: 使用 VPN 网关和门户将经典 VNet 连接到资源管理器 VNet 的步骤
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 5a90498c-4520-4bd3-a833-ad85924ecaf9
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2018
ms.author: cherylmc
ms.openlocfilehash: ad6b33b5411d3e38f099726df01fcafc8dc90d38
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>通过门户从不同部署模型中连接虚拟网络

本文介绍如何将经典 VNet 连接到 Resource Manager VNet，以使位于单独部署模型中的资源能够相互通信。 本文中的步骤主要使用 Azure 门户完成，但也可通过从此列表中选择文章使用 PowerShell 来创建此配置。

> [!div class="op_single_selector"]
> * [门户](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

将经典 VNet 连接到 Resource Manager VNet 类似于将 VNet 连接到本地站点位置。 这两种连接类型都使用 VPN 网关来提供使用 IPsec/IKE 的安全隧道。 可以在位于不同订阅、不同区域中的 VNet 之间创建连接。 还可以连接已连接到本地网络的 VNet，只要它们配置的网关是动态或基于路由的。 有关 VNet 到 VNet 连接的详细信息，请参阅本文末尾的 [VNet 到 VNet常见问题解答](#faq)。 

如果还没有虚拟网络网关并且不想创建一个，建议你改为考虑使用 VNet 对等互连连接 VNet。 VNet 对等互连不使用 VPN 网关。 有关详细信息，请参阅 [VNet 对等互连](../virtual-network/virtual-network-peering-overview.md)。

### <a name="before"></a>准备工作

* 这些步骤假定已创建了两个 VNet。 如果使用本文进行练习并且还没有 VNet，相关步骤中的链接可以帮助你创建它们。
* 请确认两个 VNet 的地址范围不相互重叠，也不与网关可能连接到的其他连接的任何范围重叠。
* 为 Resource Manager 和服务管理（经典）安装最新的 PowerShell cmdlet。 本文中同时使用 Azure 门户和 PowerShell。 要创建从经典 VNet 到 Resource Manager VNet 的连接，必须使用 PowerShell。 有关详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。 

### <a name="values"></a>示例设置

可使用这些值创建测试环境，或参考这些值以更好地理解本文中的示例。

**经典 VNet**

VNet 名称 = ClassicVNet <br>
地址空间 = 10.0.0.0/24 <br>
子网名称 = Subnet-1 <br>
子网地址范围 = 10.0.0.0/27 <br>
订阅 = 要使用的订阅 <br>
资源组 = ClassicRG <br>
位置 = 美国西部 <br>
GatewaySubnet = 10.0.0.32/28 <br>
本地站点 = RMVNetLocal <br>

**Resource Manager VNet**

VNet 名称 = RMVNet <br>
地址空间 = 192.168.0.0/16 <br>
资源组 = RG1 <br>
位置 = 美国东部 <br>
子网名称 = Subnet-1 <br>
地址范围 = 192.168.1.0/24 <br>
网关子网 = 192.168.0.0/26 <br>
虚拟网络网关名称 = RMGateway <br>
网关类型 = VPN <br>
VPN 类型 = 基于路由 <br>
SKU = VpnGw1 <br>
位置 = 美国东部 <br>
虚拟网络 = RMVNet <br> （将 VPN 网关关联到此 VNet）第一个 IP 配置 = rmgwpip <br> （网关公共 IP 地址）本地网络网关 = ClassicVNetLocal <br>
连接名称 = RMtoClassic

### <a name="connectoverview"></a>连接概述

对于此配置，会在虚拟网络之间创建基于 IPsec/IKE VPN 隧道的 VPN 网关连接。 请确保 VNet 的范围不互相重叠，也不与它们连接到的任何本地网络重叠。

下表显示了有关如何定义示例 VNet 和本地站点的示例：

| 虚拟网络 | 地址空间 | 区域 | 连接到本地网络站点 |
|:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |美国西部 | RMVNetLocal (192.168.0.0/16) |
| RMVNet | (192.168.0.0/16) |美国东部 |ClassicVNetLocal (10.0.0.0/24) |

## <a name="classicvnet"></a>第 1 节 - 配置经典 VNet 设置

在本部分中，会为经典 VNet 创建本地网络（本地站点）和虚拟网络网关。 这些屏幕截图仅供参考。 请务必将值替换成自己的值，或者使用[示例](#values)值。

### 1.<a name="classicvnet"></a>创建经典 VNet

如果还没有经典 VNet 并且运行这些步骤进行练习，则可以使用[此文章](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)以及上文中的[示例](#values)设置值创建 VNet。

如果已有具有 VPN 网关的 VNet，请验证该网关是否为动态的。 如果它是静态，必须首先删除 VPN 网关在执行前[配置本地站点](#local)。

1. 打开 [Azure 门户](https://ms.portal.azure.com)，然后使用 Azure 帐户登录。
2. 单击“+ 创建资源”打开“新建”页。
3. 在“搜索 Marketplace”字段中，键入“虚拟网络”。 如果改为选择“网络”->“虚拟网络”，则不会显示用于创建经典 VNet 的选项。
4. 从返回的列表中找到“虚拟网络”，单击它打开“虚拟网络”页面。 
5. 在“虚拟网络”页面上，选择“经典”以创建经典 VNet。 如果此处采用默认值，最终会创建资源管理器 VNet。

### 2.<a name="local"></a>配置本地站点

1. 导航到“所有资源”并在列表中找到“ClassicVNet”。
2. 在“概述”页面的“VPN 连接”部分中，单击“网关”，创建网关。
  ![配置 VPN 网关](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "配置 VPN 网关")
3. 在“新建 VPN 连接”页面上，对于“连接类型”，选择“站点到站点”。
4. 对于“本地站点”，单击“配置所需设置”。 这会打开“本地站点”页面。
5. 在“本地站点”页面上，创建一个表示资源管理器 VNet 的名称。 例如，RMVNetLocal。
6. 如果 Resource Manager VNet 的 VPN 网关已具有一个公共 IP 地址，则使用“VPN 网关 IP 地址”字段的值。 如果执行这些步骤进行练习，或者 Resource Manager VNet 尚没有虚拟网关，则可以虚构一个占位符 IP 地址。 请确保占位符 IP 地址使用的格式有效。 稍后，将使用 Resource Manager 虚拟网络网关的公共 IP 地址替换占位符 IP 地址。
7. 对于**客户端地址空间**，请使用资源管理器 VNet 的虚拟网络 IP 地址空间的 [值](#connectoverview)。 此设置用于指定要路由到 Resource Manager 虚拟网络的地址空间。 在示例中，我们使用 192.168.0.0/16 作为 RMVNet 的地址范围。
8. 单击“确定”，保存值并返回“新建 VPN 连接”页面。

### <a name="classicgw"></a>3.创建虚拟网络网关

1. 在“新建 VPN 连接”页上，选中“立即创建网关”复选框。
2. 单击“可选网关配置”打开“网关配置”页。

  ![打开网关配置页面](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "打开网关配置页面")
3. 单击“子网 - 配置所需设置”，打开“添加子网”页面。 “名称”已配置有所需值 **GatewaySubnet**。
4. “地址范围”指网关子网的范围。 虽然可以创建具有地址范围 /29（3 个地址）的网关子网，但建议创建包含更多 IP 地址的网关子网。 这可以适应将来可能需要更多可用 IP 地址的配置。 如果可能，请使用 /27 或 /28。 如果使用这些步骤进行练习，可以参考[示例](#values)值。 本示例使用“10.0.0.32/28”。 单击“确定”创建网关子网。
5. “网关配置”页面上的“大小”指的是网关 SKU。 为 VPN 网关选择网关 SKU。
6. 验证“路由类型”是否为“动态”，并单击“确定”，返回“新建 VPN 连接”页面。
7. 在“新建 VPN 连接”页面上，单击“确定”，开始创建 VPN 网关。 创建 VPN 网关可能需要多达 45 分钟才能完成。

### <a name="ip"></a>4.复制虚拟网络网关的公共 IP 地址

创建虚拟网络网关后，可查看网关 IP 地址。 

1. 导航到经典 VNet，并单击“概述”。
2. 单击“VPN 连接”，打开“VPN 连接”页面。 在“VPN 连接”页面上，可查看公共 IP 地址。 这是分配给虚拟网络网关的公共 IP 地址。 记下 IP 地址。 在稍后的步骤中处理 Resource Manager 本地网络网关配置设置时会使用此地址。 
3. 可查看网关连接的状态。 请注意，创建的本地网络站点被列为“连接”。 创建连接后，状态会改变。 查看完状态后，可以关闭此页。

## <a name="rmvnet"></a>第 2 节 - 配置资源管理器 VNet 设置

在本部分中，会为 Resource Manager VNet 创建虚拟网络网关和本地网络网关。 这些屏幕截图仅供参考。 请务必将值替换成自己的值，或者使用[示例](#values)值。

### <a name="1-create-a-virtual-network"></a>1.创建虚拟网络

**示例值：**

* VNet 名称 = RMVNet <br>
* 地址空间 = 192.168.0.0/16 <br>
* 资源组 = RG1 <br>
* 位置 = 美国东部 <br>
* 子网名称 = Subnet-1 <br>
* 地址范围 = 192.168.1.0/24 <br>


如果还没有资源管理器 VNet 并且运行这些步骤进行练习，则可以使用[创建虚拟网络](../virtual-network/quick-create-portal.md)中的步骤以及示例设置值创建虚拟网络。

### <a name="2-create-a-gateway-subnet"></a>2.创建网关子网

**示例值：**GatewaySubnet = 192.168.0.0/26

创建虚拟网络网关前，先要创建网关子网。 创建 CIDR 计数为 /28 或更大（/27、/26 等）的网关子网。 如果正在练习创建此配置，可以使用示例值。

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="creategw"></a>3.创建虚拟网络网关

**示例值：**

* 虚拟网络网关名称 = RMGateway <br>
* 网关类型 = VPN <br>
* VPN 类型 = 基于路由 <br>
* SKU = VpnGw1 <br>
* 位置 = 美国东部 <br>
* 虚拟网络 = RMVNet <br>
* 第一个 IP 配置 = rmgwpip <br>

[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

### <a name="createlng"></a>4.创建本地网络网关

**示例值：**本地网络网关 = ClassicVNetLocal

| 虚拟网络 | 地址空间 | 区域 | 连接到本地网络站点 |网关公共 IP 地址|
|:--- |:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |美国西部 | RMVNetLocal (192.168.0.0/16) |分配给 ClassicVNet 网关的公共 IP 地址|
| RMVNet | (192.168.0.0/16) |美国东部 |ClassicVNetLocal (10.0.0.0/24) |分配给 RMVNet 网关的公共 IP 地址。|

本地网络网关指定与经典 VNet 和其虚拟网络网关关联的地址范围和公共 IP 地址。 如果执行这些步骤进行练习，可以参考示例值。

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="modifylng"></a>第 3 节 - 修改经典 VNet 本地站点设置

在本部分中，会使用 Resource Manager VPN 网关 IP 地址替换在指定本地站点设置时使用的占位符 IP 地址。 本部分使用经典 (SM) PowerShell cmdlet。

1. 在 Azure 门户中，导航到经典虚拟网络。
2. 在虚拟网络页面上，单击“概述”。
3. 在“VPN 连接”部分中，单击图形中本地站点的名称。

  ![VPN 连接](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "VPN 连接")
4. 在“站点到站点 VPN 连接”页面上，单击站点名称。

  ![站点名称](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "本地站点名称")
5. 在本地站点的连接页面上，单击本地站点的名称以打开“本地站点”页面。

  ![打开本地站点](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "打开本地站点")
6. 在“本地站点”页面上，将 VPN 网关的 IP 地址替换为 Resource Manager 网关的 IP 地址。

  ![网关 IP 地址](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "网关 IP 地址")
7. 单击“确定”，更新 IP 地址。

## <a name="RMtoclassic"></a>第 4 节 - 创建从资源管理器虚拟网络到经典虚拟网络的连接

在以下步骤中，将使用 Azure 门户配置从 Resource Manager VNet 到经典 VNet 的连接。

1. 在“所有资源”中，找到本地网络网关。 在我们的示例中，本地网络网关是 **ClassicVNetLocal**。
2. 单击“配置”并验证 IP 地址值是否是经典 VNet 的 VPN 网关。 如果需要，进行更新，并单击“保存”。 关闭页面。
3. 在“所有资源”中，单击本地网络网关。
4. 单击“连接”以打开“连接”页面。
5. 在“连接”页面上，单击 **+** 以添加连接。
6. 在“添加连接”页面上，为连接命名。 例如，RMtoClassic。
7. 在此页面上已选择了“站点到站点”。
8. 选择要与此站点关联的虚拟网络网关。
9. 创建一个**共享密钥**。 在创建的从经典 VNet 到 Resource Manager VNet 的连接中也将使用该密钥。 可以生成该密钥或者虚构一个密钥。 在我们的示例中，我们使用的是“abc123”，但可以（而且应该）使用更复杂的。
10. 单击“确定”以创建连接。

## <a name="classictoRM"></a>第 5 节 - 创建从经典虚拟网络到资源管理器虚拟网络的连接

在以下步骤中，将配置从经典 VNet 到 Resource Manager VNet 的连接。 这些步骤需要 PowerShell。 无法在门户中创建此连接。 请确保已下载并安装了经典 (SM) 和 Resource Manager (RM) PowerShell cmdlet。

### <a name="1-connect-to-your-azure-account"></a>1.连接到 Azure 帐户

使用提升的权限打开 PowerShell 控制台并登录 Azure 帐户。 登录后将下载帐户设置，以便 Azure PowerShell 使用这些设置。 以下 cmdlet 会提示为资源管理器部署模型提供 Azure 帐户的登录凭据：

```powershell
Login-AzureRmAccount
```

获取 Azure 订阅的列表。

```powershell
Get-AzureRmSubscription
```

如果有多个订阅，请指定要使用的订阅。

```powershell
Select-AzureRmSubscription -SubscriptionName "Name of subscription"
```

接下来，登录以使用经典 PowerShell cmdlet（服务管理）。 使用以下命令为经典部署模型添加 Azure 帐户：

```powershell
Add-AzureAccount
```

获取订阅的列表。 添加服务管理 cmdlet 时可能需要此步骤，具体取决于 Azure 模块安装。

```powershell
Get-AzureSubscription
```

如果有多个订阅，请指定要使用的订阅。

```powershell
Select-AzureSubscription -SubscriptionName "Name of subscription"
```

### <a name="2-view-the-network-configuration-file-values"></a>2.查看网络配置文件值

在 Azure 门户中创建 VNet 时，Azure 使用的全名在 Azure 门户中不可见。 例如，在 Azure 门户中命名为“ClassicVNet”的 VNet 在网络配置文件中可能具有更长的名称。 该名称可能如下所示：“Group ClassicRG ClassicVNet”。 在这些步骤中，将下载网络配置文件并查看值。

在计算机上创建一个目录，然后将网络配置文件导出到该目录。 在此示例中，网络配置文件导出到 C:\AzureNet。

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

使用文本编辑器打开文件，并查看经典 VNet 的名称。 运行 PowerShell cmdlet 时，请使用网络配置文件中的名称。

- VNet 名称以 **VirtualNetworkSite name =** 形式列出
- 站点名称以 **LocalNetworkSite name=** 形式列出

### <a name="3-create-the-connection"></a>3.创建连接

设置共享密钥并创建从经典 VNet 到 Resource Manager VNet 的连接。 无法使用门户设置共享密钥。 使用经典版本的 PowerShell cmdlet 登录时，请确保运行这些步骤。 为此，请使用 Add-AzureAccount。 否则无法设置“-AzureVNetGatewayKey”。

- 在此示例中，**-VNetName** 是在网络配置文件中找到的经典 VNet 的名称。 
- -LocalNetworkSiteName 是为本地站点指定的名称，与在网络配置文件中找到的一致。
- **-SharedKey** 是生成并指定的值。 对于此示例，我们使用了 *abc123*，但可以生成更复杂的内容。 重要的是，在此处指定的值必须与创建从 Resource Manager 虚拟网络到经典虚拟网络的连接时指定的值相同。

```powershell
Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
-LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
```

##<a name="verify"></a>第 6 节：验证连接

可使用 Azure 门户或 PowerShell 来验证连接。 验证时，由于正在创建连接，因此可能需要等待一两分钟。 连接成功后，连接状态将从“正在连接”变为“已连接”。

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>验证从经典 VNet 到 Resource Manager VNet 的连接

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

###<a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>验证从 Resource Manager VNet 到经典 VNet 的连接

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>VNet 到 VNet 常见问题解答

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
