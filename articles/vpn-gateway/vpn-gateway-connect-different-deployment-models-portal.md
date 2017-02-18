---
title: "将经典虚拟网络连接到 Azure Resource Manager VNet：门户 | Microsoft Docs"
description: "了解如何使用 VPN 网关和门户在经典 VNet 和 Resource Manager VNet 之间创建 VPN 连接"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 5a90498c-4520-4bd3-a833-ad85924ecaf9
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/17/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 10985eaebd9148ef4fbd22eac0ba50076ca71ef5
ms.openlocfilehash: c20d72f25571e582310ae65ad1d887dbdb7b011c


---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>通过门户从不同部署模型中连接虚拟网络
> [!div class="op_single_selector"]
> * [门户](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Azure 当前具有两个管理模型：经典模型和 Resource Manager (RM) 模型。 如果 Azure 已经使用了一段时间，则您的 Azure VM 和实例角色可能是在经典 VNet 上运行。 而较新的 VM 和角色实例可能是在 Resource Manager 中创建的 VNet 上运行。 本文将指导您如何连接经典 VNet 和 Resource Manager Vnet，从而可以通过网关连接使不同部署模型中的资源能够相互通信。 

可以在位于不同订阅、不同区域中的 VNet 之间创建连接。 您还可以连接已连接到本地网络的 VNet，只要它们配置的网关是动态或基于路由的。 有关 VNet 到 VNet 连接的详细信息，请参阅本文末尾的 [VNet 到 VNet 注意事项](#faq)部分。

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>VNet 到 VNet 连接的部署模型和方法
[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

当我们发布有关此配置的新文章和其他可用工具时，将会更新以下表格。 当有文章可用时，我们将从表中直接链接到该文章。<br><br>

**VNet 到 VNet**

[!INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

**VNet 对等互连**

[!INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="a-namebeforeabefore-beginning"></a><a name="before"></a>准备阶段
以下步骤将指导您完成为每个 VNet 配置动态或基于路由的网关以及在网关之间创建 VPN 连接所需的设置。 此配置不支持静态或基于策略的网关。 

本文将使用 Azure 门户和 PowerShell。 在虚拟网络间创建连接需要 PowerShell。 不能使用门户来创建此配置的连接。

### <a name="prerequisites"></a>先决条件
* 已创建了两个 VNet。
* 两个 VNet 的地址范围不相互重叠，也不与网关可能连接到的其他连接的任何范围重叠。
* 已安装最新 PowerShell cmdlet。 有关详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)。 请确保安装服务管理 (SM) 和 Resource Manager (RM) cmdlet。 

### <a name="a-namevaluesaexample-settings"></a><a name="values"></a>示例设置
可以使用示例设置作为参考。

**经典 VNet 设置**

VNet 名称 = ClassicVNet <br>
地址空间 = 10.0.0.0/24 <br>
子网 1 = 10.0.0.0/27 <br>
资源组 = ClassicRG <br>
位置 = 美国西部 <br>
GatewaySubnet = 10.0.0.32/28 <br>
本地站点 = RMVNetLocal <br>

**Resource Manager VNet 设置**

VNet 名称 = RMVNet <br>
地址空间 = 192.168.0.0/16 <br>
子网 1 = 192.168.1.0/24 <br>
网关子网 = 192.168.0.0/26 <br>
资源组 = RG1 <br>
位置 = 美国东部 <br>
虚拟网络网关名称 = RMGateway <br>
网关类型 = VPN <br>
VPN 类型 = 基于路由 <br>
网关公共 IP 名称 = gwpip <br>
本地网络网关 = ClassicVNetLocal <br>

## <a name="a-namecreatesmgwasection-1-configure-classic-vnet-settings"></a><a name="createsmgw"></a>第 1 部分：配置经典 VNet 设置
本部分将为经典 VNet 创建本地网络（本地站点）和虚拟网络网关。 本部分中的说明使用 Azure 门户。 这里的步骤假设经典 VNet 尚未创建 VPN 网关。 如果已有网关，请确认其为动态网关。 如果为静态，则必须先删除 VPN 网关，然后再继续以下步骤。

### <a name="part-1---configure-the-local-site"></a>第 1 部分 - 配置本地站点

打开 [Azure 门户](https://ms.portal.azure.com)，然后使用 Azure 帐户登录。

1. 导航到“所有资源”并找到经典虚拟网络。
2. 在“概述”边栏选项卡的“VPN 连接”部分中，单击“网关”图形，创建网关。

    ![配置 VPN 网关](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "配置 VPN 网关")
3. 在“新建 VPN 连接”边栏选项卡上，就“连接类型”选择“站点到站点”。
4. 对于“本地站点”，单击“配置所需设置”。 这将打开“本地站点”边栏选项卡。
5. 在“本地站点”边栏选项卡上，创建用于表示 Resource Manager VNet 的名称。 例如，RMVNetLocal。
6. 如果 Resource Manager VNet 的 VPN 网关已具有一个公共 IP 地址，则使用 **VPN 网关 IP 地址**字段的值。 如果尚未创建 Resource Manager VNet 的虚拟网络网关，则可使用占位符 IP 地址。 请确保占位符 IP 地址使用的格式有效。 稍后，必须使用 Resource Manager 虚拟网络网关的公共 IP 地址替换占位符 IP 地址。
7. 对于**客户端地址空间**，请使用 Resource Manager VNet 的虚拟网络 IP 地址空间的值。 此设置用于指定要路由到 RM VNet 的地址空间。
8. 单击“确定”，保存值并返回“新建 VPN 连接”边栏选项卡。

### <a name="part-2---create-the-virtual-network-gateway"></a>第 2 部分 - 创建虚拟网络网关
1. 在“新建 VPN 连接”边栏选项卡上，选择“立即创建网关”复选框，然后单击“可选网关配置”，打开“网关配置”边栏选项卡。 

    ![打开网关配置边栏选项卡](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "打开网关配置边栏选项卡")
2. 单击“子网 - 配置所需设置”，打开“添加子网”边栏选项卡。 在此边栏选项卡上，你将看到已为“名称”配置了所需值 **GatewaySubnet**。
3. “地址范围”指网关子网的范围。 虽然可创建具有 /29 地址范围（3 个地址）的网关子网，但建议你创建包含更多可用 IP 地址的网关子网，以容纳将来可能出现的需要更多可用 IP 地址的配置。 如果可能，请使用 /27 或 /28。 单击“确定”创建网关子网。
4. “网关配置”边栏选项卡上的“大小”指网关 SKU。 为 VPN 网关选择网关 SKU。
5. 验证“路由类型”是否为“动态”，然后单击“确定”，返回“新建 VPN 连接”边栏选项卡。
6. 在“新建 VPN 连接”边栏选项卡，单击“确定”，开始创建 VPN 网关。 此步骤可能最多需要 45 分钟才能完成。


### <a name="a-nameipapart-3---copy-the-virtual-network-gateway-public-ip-address"></a><a name="ip"></a>第 3 部分 - 复制虚拟网络网关的公共 IP 地址
创建虚拟网络网关后，可查看网关 IP 地址。 

1. 导航到经典 VNet，然后单击“概述”。
2. 单击“VPN 连接”，打开“VPN 连接”边栏选项卡。 在“VPN 连接”边栏选项卡，可查看公共 IP 地址。 这是分配给虚拟网络网关的公共 IP 地址。 记下或复制此 IP 地址。 在稍后处理 Resource Manager 本地网络网关配置设置的步骤中会使用该步骤。 还可查看网关连接的状态。 请注意，创建的本地网络站点被列为“连接”。 创建连接后，状态会改变。 复制网关 IP 地址后关闭边栏选项卡。

## <a name="a-namecreatermgwasection-2-configure-resource-manager-vnet-settings"></a><a name="creatermgw"></a>第 2 部分：配置 Resource Manager VNet 设置
本部分将为 Resource Manager VNet 创建虚拟网络网关和本地网络。 这些屏幕截图仅供参考。 请务必替换为你自己的值。 如果你是在练习创建此配置，请参考这些[值](#values)。

### <a name="part-1---create-a-gateway-subnet"></a>第 1 节 - 创建网关子网
创建虚拟网络网关前，先要创建网关子网。 创建 CIDR 计数为 /28 或更大的网关子网。 （/27，/26 等）

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

从浏览器导航到 [Azure 门户](http://portal.azure.com)并使用 Azure 帐户登录。

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="part-2---create-a-virtual-network-gateway"></a>第 2 节 - 创建虚拟网络网关
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

创建虚拟网络网关最多需要 45 分钟。 可等待 Resource Manager 虚拟网络网关创建完成，也可继续进行[第 3 部分 - 创建本地网络网关](#createlng)。 创建网关后，会向其分配公共 IP 地址。 在后续步骤中，会使用此 IP 地址替换第 1 部分中创建的经典 VNet 本地站点设置的占位符 IP 地址信息。 

### <a name="a-namecreatelngapart-3---create-a-local-network-gateway"></a><a name="createlng"></a>第 3 部分 - 创建本地网络网关

本地网络网关指定与经典 VNet 和其虚拟网络网关关联的地址范围和公共 IP 地址。

- 请使用[上一部分](#ip)中分配给经典 VNet 网关的公共 IP 地址。 
- 为本地网络网关命名，Azure 可通过该名称引用该网关。 例如，“ClassicVNetLocal”。
- 使用分配给经典 VNet（不仅仅是子网）的地址空间。

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]


## <a name="section-3-modify-the-classic-vnet-local-site-settings"></a>第 3 节：修改经典 VNet 本地站点设置

在此节中，将使用经典 VNet。 指定本地站点设置时，需替换使用的占位符 IP 地址。 本部分使用经典 (SM) PowerShell cmdlet。 如果尚未进行此操作，请查看 Resource Manager VNe 的虚拟网络网关，并复制公共 IP 地址。 将使用此 IP 地址替换占位符 IP 地址。

验证是否按照[准备阶段](#before)部分的说明下载了最新版 cmdlet。

1. 在 Azure 门户中，导航到经典虚拟网络。
2. 在虚拟网络边栏选项卡上，单击“概述”。
3. 在“VPN 连接”部分中，单击图形中本地站点的名称。

    ![VPN 连接](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "VPN 连接")
4. 在“站点到站点 VPN 连接”边栏选项卡上，单击站点名称。

    ![站点名称](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "本地站点名称")
5. 在本地站点的连接边栏选项卡上，单击要打开“本地站点”边栏选项卡的本地站点的名称。

    ![打开本地站点](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "打开本地站点")
6. 在“本地站点”边栏选项卡上，将 VPN 网关的 IP 地址替换为 Resource Manager 网关的 IP 地址。

    ![网关 IP 地址](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "网关 IP 地址")
7. 单击“确定”，更新 IP 地址。


## <a name="a-nameconnectasection-4-create-the-connection"></a><a name="connect"></a>第 4 部分：创建连接
本部分将创建 VNet 之间的连接。 这些步骤需要 PowerShell。 无法在任一门户中创建此连接。 请确保您已下载并安装了经典 (SM) 和 Resource Manager (RM) PowerShell cmdlet。

### <a name="part-1---log-in-to-your-azure-account"></a>第 1 部分 - 登录 Azure 帐户

1. 使用提升的权限打开 PowerShell 控制台并登录 Azure 帐户。 以下 cmdlet 将提示您提供 Azure 帐户的登录凭据。 登录后将下载您的帐户设置，以便 Azure PowerShell 使用这些设置。
   
        Login-AzureRmAccount 
   
2. 如果您有多个订阅，则获取您的 Azure 订阅的列表。
   
        Get-AzureRmSubscription
   
3. 指定要使用的订阅。 
   
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"

4. 添加要使用经典 PowerShell cmdlet 的 Azure 帐户。 若要执行此操作，请使用以下命令：
   
        Add-AzureAccount

### <a name="part-2---download-your-network-configuration-file"></a>第 2 部分 -下载网络配置文件

在 Azure 门户中创建经典 VNet 设置时，由于部署模型的不同，有时经典 Vnet 和本地网络站点的名称在网络配置文件中会发生变化。 例如，使用 Azure 门户将经典 VNet 命名为“经典 VNet”，并在一个名为“ClassicRG”的资源组中进行创建。 网络配置文件中包含的名称转换为“Group ClassicRG Classic VNet”。 指定包含空格的 VNet 的名称时，请使用引号将值引起来。

1. 通过运行以下命令，导出 Azure 网络配置文件。 如有必要，可以将文件的导出位置更改为其他位置。
   
        Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
3. 使用文本编辑器（如记事本）打开.xml 文件并查看内容。 验证经典虚拟网络和本地网络站点的名称的值。

### <a name="part-3---set-the-shared-key"></a>第 3 部分 - 设置共享密钥

针对从经典 VNet 到 Resource Manager VNet 的连接设置共享密钥。 使用这些 cmdlet 时，请务必使用网络配置文件检查 `-VNetName` 和 `-LocalNetworkSiteName` 的值。 指定包含空格的名称时，请将值用引号引起来。 

- 在此示例中，`-VNetName` 是经典 VNet 的名称。 
- `-LocalNetworkSiteName` 是为本地站点指定的名称。
- `-SharedKey` 是用户生成并指定的值。 对于此示例，我们使用了 *abc123*，但你可以生成更复杂的内容。 重要的是，此处指定的值必须与下一步中创建连接时指定的值相同。

在 PowerShell 控制台中，运行下方示例设置共享的密钥，请务必将值替换为你自己的值。 本示例的返回结果应显示“状态: 成功”。
   
        Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
        -LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
### <a name="part-4---create-the-vpn-connection-by-running-the-following-commands"></a>第 4 部分 - 通过运行以下命令创建 VPN 连接：
   
1. 设置变量。
   
        $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
        $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   
2. 创建连接。 在此示例中，`-Name` 是要赋予连接的名称，并非已创建的内容。 以下示例创建名为“RM-Classic”的连接。 请注意，`-ConnectionType` 是“IPsec”，不是“Vnet2Vnet”，而 `-SharedKey` 与之前设置的密钥相匹配。
   
        New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
        -Location "East US" -VirtualNetworkGateway1 `
        $vnet02gateway -LocalNetworkGateway2 `
        $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

## <a name="section-5-verify-your-connections"></a>第 5 节：验证连接
可使用 Azure 门户或 PowerShell 来验证连接。 验证时，由于正在创建连接，因此可能需要等待一两分钟。 连接成功后，连接状态将从“正在连接”变为“已连接”。

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>验证从经典 VNet 到 Resource Manager VNet 的连接

####<a name="verify-your-connection-using-powershell"></a>使用 PowerShell 验证连接


[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

####<a name="verify-your-connection-using-the-azure-portal"></a>使用 Azure 门户验证连接

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


###<a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>验证从 Resource Manager VNet 到经典 VNet 的连接

####<a name="verify-your-connection-using-powershell"></a>使用 PowerShell 验证连接

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

####<a name="verify-your-connection-using-the-azure-portal"></a>使用 Azure 门户验证连接

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="a-namefaqavnet-to-vnet-considerations"></a><a name="faq"></a>VNet 到 VNet 注意事项

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]




<!--HONumber=Jan17_HO4-->


