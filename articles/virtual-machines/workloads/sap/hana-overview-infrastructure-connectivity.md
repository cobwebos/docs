---
title: Azure 上的 SAP HANA（大型实例）的基础结构和连接 | Microsoft 文档
description: 配置所需的连接基础结构，以使用 Azure 上的 SAP HANA（大型实例）。
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 43debeb710e5ab5112f9f0a85a76761cde3051a7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="sap-hana-large-instances-infrastructure-and-connectivity-on-azure"></a>Azure 上的 SAP HANA（大型实例）的基础结构和连接 

阅读本指南前，请先了解一些定义。 在 [Azure 上的 SAP HANA（大型实例）概述和体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)中，我们引入了两类不同的 HANA 大型实例单元，其中包括：

- 称为“I 类”SKU 的S72、S72m、S144、S144m、S192 和 S192m。
- 称为“II 类”SKU 的 S384、S384m、S384xm、S576、S768 和 S960。

将在整个 HANA 大型实例文档中使用类说明符，最终用于根据 HANA 大型实例 SKU 指代不同的功能和要求。

我们还经常用到其他定义，如下所述：
- **大型实例模具：**已通过 SAP HANA TDI 认证的硬件基础结构堆栈，专门用于在 Azure 中运行 SAP HANA 实例。
- **Azure 上的 SAP HANA（大型实例）：**用于在通过 SAP HANA TDI 认证的、部署在不同 Azure 区域中的大型实例模具中的硬件上运行 HANA 实例的产品的官方名称。 本技术部署指南中广泛使用的相关术语“HANA 大型实例”是“Azure 上的 SAP HANA（大型实例）”的简称。
 

在你与 Microsoft 企业帐户团队之间达成“Azure 上的 SAP HANA（大型实例）”的采购协议后，Microsoft 会要求提供以下信息来部署 HANA 大型实例单元：

- 客户名称
- 业务联系人信息（包括电子邮件地址和电话号码）
- 技术联系人信息（包括电子邮件地址和电话号码）
- 技术网络联系人信息（包括电子邮件地址和电话号码）
- Azure 部署区域（截至 2017 年 7 月发布的区域为美国西部、美国东部、澳大利亚东部、澳大利亚东南部、西欧和北欧）
- 确认“Azure 上的 SAP HANA 配置（大型实例）”的 SKU（配置）
- 如 HANA 大型实例的概述和体系结构文档中详述，对于要部署该解决方案的每个 Azure 区域，需要提供：
    - 用于将 Azure VNet 连接到 HANA 大型实例的 ER-P2P 连接的 /29 IP 地址范围
    - 用于 HANA 大型实例服务器 IP 池的 /24 CIDR 块
- 对于连接到 HANA 大型实例的每个 Azure VNet，VNet 地址空间属性中使用的 IP 地址范围值
- 对于每个 HANA 大型实例系统的数据，需要提供：
  - 所需主机名 - 最好是使用完全限定的域名。
  - 超出服务器 IP 池地址范围的 HANA 大型实例单元的相应 IP 地址 - 请注意，服务器 IP 池地址范围中的前 30 个 IP 地址被保留，以供 HANA 大型实例内部使用
  - SAP HANA 实例的 SAP HANA SID 名称（创建必要的 SAP HANA 相关磁盘卷时需要用到）。 需要使用 HANA SID 在要附加到 HANA 大型实例单元的 NFS 卷上创建 sidadm 权限。 它还用作已装载磁盘卷的名称组件之一。 若要在单元上运行多个 HANA 实例，需要列出多个 HANA SID。 每个都会分配有一组单独的卷。
  - 为了创建所需的 SAP HANA 相关磁盘卷，需要获取 sidadm 用户在 Linux OS 中的 groupid。 SAP HANA 安装通常使用组 ID 1001 创建 sapsys 组。 sidadm 用户属于该组
  - 为了创建所需的 SAP HANA 相关磁盘卷，需要获取 sidadm 用户在 Linux OS 中的 userid。 若要在单元上运行多个 HANA 实例，需要列出所有 <sid>adm 用户 
- Azure 上的 SAP HANA（大型实例）将直接连接到的 Azure 订阅的 Azure 订阅 ID。 此订阅 ID 指代将按 HANA 大型实例单元收费的 Azure 订阅。

提供这些信息后，Microsoft 将预配 Azure 上的 SAP HANA（大型实例），并返回所需的信息，以便将 Azure VNet 链接到 HANA 大型实例并访问 HANA 大型实例单元。

## <a name="connecting-azure-vms-to-hana-large-instances"></a>将 Azure VM 连接到 HANA 大型实例

如 [Azure 上的 SAP HANA（大型实例）概述和体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)中所述，Azure 中包含 SAP 应用程序层的 HANA 大型实例最简单部署如下所示：

![连接到了 Azure 上的 SAP HANA（大型实例）和本地的 Azure VNet](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

仔细观察 Azure VNet 端，我们认识到需要以下信息：

- 将用于部署 VM 的 SAP 应用程序层的 Azure VNet 的定义。
- 这自然而然就意味着，已在 Azure VNet 中定义了一个要将 VM 部署到的默认子网。
- 创建的 Azure VNet 至少需要包含一个 VM 子网和一个 ExpressRoute 网关子网。 应按照下面各部分所述，为这些子网分配 IP 地址范围。

让我们更深入地了解如何为 HANA 大型实例创建 Azure VNet

### <a name="creating-the-azure-vnet-for-hana-large-instances"></a>为 HANA 大型实例创建 Azure VNet

>[!Note]
>必须使用 Azure 资源管理器部署模型为 HANA 大型实例创建 Azure VNet。 HANA 大型实例解决方案不支持旧 Azure 部署模型（通常称为“经典部署模型”）。

可以使用 Azure 门户、PowerShell、Azure 模板或 Azure CLI 创建 VNet（请参阅[使用 Azure 门户创建虚拟网络](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)）。 在以下示例中，我们将探讨通过 Azure 门户创建的 VNet。

让我们分析通过 Azure 门户创建的 Azure VNet 的一些定义，并确定这些定义与我们列出的 IP 地址范围不同的定义关系如何。 我们所述的“地址空间”是指允许 Azure VNet 使用的地址空间。 此地址空间也是 VNet 用于 BGP 路由传播的地址范围。 可在以下位置看到**地址空间**：

![Azure 门户中显示的 Azure VNet 地址空间](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

上面的示例使用的是 10.16.0.0/16，Azure VNet 可使用的 IP 地址范围相当宽泛。 这意味着，此 VNet 中后续子网中的所有 IP 地址范围可在该“地址空间”中包含其范围。 通常，对于 Azure 中的单个 VNet，我们不建议使用这么大的地址范围。 让我们继续下一步，分析 Azure VNet 中定义的子网：

![Azure VNet 子网及其 IP 地址范围](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

可以看到，VNet 包含两个子网，第一个 VM 子网为“default”，第二个子网为“GatewaySubnet”。
在后面的部分中，我们将图中“default”子网的 IP 地址范围称为“Azure VM 子网 IP 地址范围”。 在后续部分中，我们将网关子网的 IP 地址范围称为 **VNet 网关子网 IP 地址范围**。 

在上面两张插图演示的案例中可以看到，**VNet 地址空间**涵盖了 **Azure VM 子网 IP 地址范围**和 **VNet 网关子网 IP 地址范围**。 

在其他情况下，如果需要保留 IP 地址范围或指定具体的 IP 地址，可以将 VNet 的 VNet 地址空间限制为每个子网使用的特定地址范围。 在这种情况下，可将 VNet 的 **VNet 地址空间**定义为多个特定的范围，如下所示：

![包含两个空间的 Azure VNet 地址空间](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

在此示例中，定义了两个 VNet 地址空间。 这两个地址空间与定义的“Azure VM 子网 IP 地址范围”和“VNet 网关子网 IP 地址范围”完全相同。

对于这些租户子网（VM 子网），可以使用偏好的任何命名标准。 不过，对于连接到 Azure 上的 SAP HANA（大型实例）ExpressRoute 线路的每个 VNet，必须始终只有一个网关子网。 为了确保 ExpressRoute 网关的位置正确，必须始终将此网关子网命名为“GatewaySubnet”。

> [!WARNING] 
> 请务必始终将此网关子网命名为“GatewaySubnet”。

可以使用多个 VM 子网，甚至可以利用不连续的地址范围。 但如前所述，必须在 VNet 的 **VNet 地址空间**中，以聚合形式或者以包含 VM 子网和网关子网确切范围的列表形式涵盖这些地址范围。

汇总有关要连接到 HANA 大型实例的 Azure VNet 的重要事实：

- 执行 HANA 大型实例的初始部署时，需要将 VNet 地址空间提交给 Microsoft。 
- **VNet 地址空间**可以是涵盖 Azure VM 子网 IP 地址范围和 VNet 网关子网 IP 地址范围的较大范围。
- 或者，可以提交涵盖不同 VM 子网 IP 地址范围和 VNet 网关子网 IP 地址范围的多个 **VNet 地址空间**范围。
- 定义的 **VNet 地址空间**用于 BGP 路由传播。
- 必须将网关子网命名为“GatewaySubnet”。
- **VNet 地址空间**用作 HANA 大型实例端上的筛选器，用于允许或禁止 Azure 的流量传入 HANA 大型实例单位。 如果 Azure VNet 的 BGP 路由信息与配置用于对 HANA 大型实例进行筛选的 IP 地址范围不匹配，可能会出现连接问题。
- “将 VNet 连接到 HANA 大型实例 ExpressRoute”部分中进一步介绍了有关网关子网的一些详细信息。



### <a name="different-ip-address-ranges-to-be-defined"></a>要定义的不同 IP 地址范围 

在前面的部分中，我们已介绍了部署 HANA 大型实例所必需的一些 IP 地址范围。 不过，还有一些 IP 地址范围也很重要。 让我们更详细地了解。 在发送初始部署请求之前，需要定义以下 IP 地址（这些地址并非全部都要提交给 Microsoft）：

- **VNet 地址空间：**如前所述，这是已分配（或打算分配）给要连接到 SAP HANA 大型实例环境的 Azure 虚拟网络 (VNet) 中的地址空间参数的一个或多个 IP 地址范围。 建议将此地址空间参数指定为多行值，其中包括 Azure VM 子网范围和 Azure 网关子网范围，如上图所示。 此范围不得与本地或服务器 IP 池或 ER-P2P 地址范围重叠。 如何获取这一个或多个 IP 地址范围？ 企业网络团队或服务提供程序应提供网络内部未使用的一个或多个 IP 地址范围。 例如，如果上面的 Azure VM 子网为 10.0.1.0/24，下面的 Azure 网关子网为 10.0.2.0/28，建议为 Azure VNet 地址空间指定两行：10.0.1.0/24 和 10.0.2.0/28。 尽管地址空间值可以聚合，仍建议将它们与子网范围匹配，以免将来在网络中的其他位置意外重用较大地址空间内的 IP 地址范围。 VNET 地址空间是需要在请求进行初始部署时提交给 Microsoft 的 IP 地址范围

- **Azure VM 子网 IP 地址范围：**如前所述，这是已分配（或打算分配）给要连接到 SAP HANA 大型实例环境的 Azure VNET 中的 Azure VNet 子网参数的 IP 地址范围。 此 IP 地址范围用于向 Azure VM 分配 IP 地址。 此范围外的 IP 地址可以连接到 SAP HANA 大型实例服务器。 如果需要，可以使用多个 Azure VM 子网。 Microsoft 建议对每个 Azure VM 子网使用一个 /24 CIDR 块。 此地址范围必须是 Azure VNet 地址空间中使用的值的一部分。 如何获取此 IP 地址范围？ 企业网络团队或服务提供程序应提供网络内部当前未使用的 IP 地址范围。

- **VNet 网关子网 IP 地址范围：**根据想要使用的功能，建议的大小为：
   - 超高性能 ExpressRoute 网关：/26 地址块 - II 类 SKU 所必需
   - 通过高性能 ExpressRoute 网关同时使用 VPN 和 ExpressRoute（或更小的规模）：/27 地址块
   - 其他所有情况：/28 地址块。 此地址范围必须是“VNet 地址空间”值中使用的值的一部分。 此地址范围必须是需要提交给 Microsoft 的“Azure VNet 地址空间”值中使用的值的一部分。 如何获取此 IP 地址范围？ 企业网络团队或服务提供程序应提供网络内部当前未使用的 IP 地址范围。 

- **用于 ER-P2P 连接的地址范围：**这是 SAP HANA 大型实例 ExpressRoute (ER) P2P 连接所需的 IP 范围。 此 IP 地址范围必须是 /29 CIDR IP 地址范围。 此范围不得与本地或其他 Azure IP 地址范围重叠。 此 IP 地址范围用于设置从 Azure VNet ExpressRoute 网关到 SAP HANA 大型实例服务器的 ER 连接。 如何获取此 IP 地址范围？ 企业网络团队或服务提供程序应提供网络内部当前未使用的 IP 地址范围。 这是需要在请求进行初始部署时提交给 Microsoft 的 IP 地址范围
  
- **服务器 IP 池地址范围：**此 IP 地址范围用于向 HANA 大型实例服务器分配单个 IP 地址。 建议的子网大小为 /24 CIDR 块 - 但如果需要，可将它缩小到提供 64 个 IP 地址的最小规模。 此范围内的前 30 个 IP 地址保留给 Microsoft 使用。 选择范围大小时，请务必考虑到这一事实。 此范围不得与本地或其他 Azure IP 地址重叠。 如何获取此 IP 地址范围？ 企业网络团队或服务提供商应会提供网络内部当前未使用的 IP 地址范围。 /24（推荐）唯一 CIDR 块，用于为 Azure 上的 SAP HANA（大型实例）分配所需的特定 IP 地址。 这是需要在请求进行初始部署时提交给 Microsoft 的 IP 地址范围
 
尽管需要定义和规划上述 IP 地址范围，但不需要将它们全部传输到 Microsoft。 概括而言，需要提交到 Microsoft 的 IP 地址范围包括：

- Azure VNet 地址空间
- 用于 ER-P2P 连接的地址范围
- 服务器 IP 池地址范围

添加需要连接到 HANA 大型实例的其他 VNet 时，必须将所要添加的新 Azure VNet 地址空间提交给 Microsoft。 

下面的示例展示了不同的范围，以及需要配置并最终提供给 Microsoft 的一些示例范围。 可以看到，Azure VNet 地址空间值未在第一个示例中聚合，而是通过第一个 Azure VM 子网 IP 地址范围和 VNet 网关子网 IP 地址范围进行定义。 在 Azure VNet 中使用多个 VM 子网的相应工作原理是：配置并提交其他 VM 子网的其他 IP 地址范围，作为 Azure VNet 地址空间的一部分。

![Azure 上的 SAP HANA（大型实例）最小部署中所需的 IP 地址范围](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

还可以聚合要提交给 Microsoft 的数据。 在这种情况下，Azure VNet 地址空间只包含一个空间。 使用上面示例使用的 IP 地址范围。 此聚合的 VNet 地址空间如下所示：

![Azure 上的 SAP HANA（大型实例）最小部署中所需 IP 地址范围的另一种可能情况](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

在上面可以看到，我们定义了一个涵盖 4096 个 IP 地址的较大范围，而不是为 Azure VNet 的地址空间定义了两个较小范围。 定义这种较大的地址空间会使一些相当大的范围不被利用。 由于 VNet 地址空间值用于 BGP 路由传播，使用未在本地或网络中其他位置使用的范围可能会导致路由问题。 因此，我们建议使地址空间与使用的实际子网地址空间紧密协调。 如果需要，以后可以随时添加新的地址空间值，而不会导致 VNet 上有故障时间。
 
> [!IMPORTANT] 
> ER-P2P、服务器 IP 池、Azure VNet 地址空间的每个 IP 地址范围既不得相互重叠，也不得与网络中其他某位置使用的其他任何范围重叠；每个范围都必须是独立的，如前面两张图所示，不得是其他任何范围的子网。 如果范围之间存在重叠，Azure VNet 无法连接到 ExpressRoute 线路。

### <a name="next-steps-after-address-ranges-have-been-defined"></a>定义地址范围后的后续步骤
定义 IP 地址范围后，需要执行以下活动：

1. 连同本文档开头部分列出的其他数据一起提交 Azure VNet 地址空间、ER-P2P 连接和服务器 IP 池地址范围的 IP 地址范围。 此时，还可以开始创建 VNet 和 VM 子网。 
2. Express Route 线路是由 Microsoft 在订阅与 HANA 大型实例模具之间创建的。
3. 租户网络由 Microsoft 在大型实例戳记上创建。
4. Microsoft 会在 Azure 上的 SAP HANA（大型实例）基础结构中配置网络，以接受要与 HANA 大型实例通信的 Azure VNet 地址空间中的 IP 地址。
5. Microsoft 会在租户网络中分配计算单元，分配并装载存储，再安装操作系统（SUSE 或 Red Hat Linux），具体视购买的 Azure 上的 SAP HANA（大型实例）SKU 而定。 这些单元的 IP 地址将从提交给 Microsoft 的服务器 IP 池地址范围中排除。

部署过程结束时，Microsoft 将提供以下数据：
- 将 Azure VNet 连接到 ExpressRoute 线路，然后通过该线路将 Azure VNet 连接到 HANA 大型实例所需的信息：
     - 授权密钥
     - ExpressRoute 对等互连 ID
- 建立 ExpressRoute 线路和 Azure VNet 后用于访问 HANA 大型实例的数据。

有关 HANA 大型实例的连接顺序，还可以参阅 [SAP HANA 大型实例的端到端设置](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/)一文。 这篇文档中的示例部署展示了以下许多步骤。 


## <a name="connecting-a-vnet-to-hana-large-instance-expressroute"></a>将 VNet 连接到 HANA 大型实例 ExpressRoute

定义所有 IP 地址范围并从 Microsoft 获取数据后，可以开始将前面创建的 VNet 连接到 HANA 大型实例。 创建 Azure VNet 后，必须在 VNet 中创建 ExpressRoute 网关，以便将 VNet 链接到与大型实例戳记上的客户租户相连接的 ExpressRoute 线路。

> [!NOTE] 
> 此步骤可能需要长达 30 分钟才能完成，因为需要在指定的 Azure 订阅中创建新的网关，然后将其连接到指定的 Azure VNet。

如果网关已存在，请检查它是否为 ExpressRoute 网关。 如果不是，则必须删除该网关，然后重新创建 ExpressRoute 网关。 如果已建立 ExpressRoute 网关，请转到下面的“链接 VNet”部分，因为 Azure VNet 已连接到用于建立本地连接的 ExpressRoute 线路。

- 使用（新的）[Azure 门户](https://portal.azure.com/)或 PowerShell 创建与 VNet 连接的 ExpressRoute VPN 网关。
  - 如果使用 Azure 门户，请添加新的“虚拟网关”，再选择“ExpressRoute”作为网关类型。
  - 如果选择的是 PowerShell，请先下载并使用最新的 [Azure PowerShell SDK](https://azure.microsoft.com/downloads/)，确保获得最佳体验。 以下命令创建 ExpressRoute 网关。 前面带有 $ 的文本是用户定义的变量，需要使用具体信息进行更新。

```PowerShell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

此示例使用了 HighPerformance 网关 SKU。 选项包括 HighPerformance 或 UltraPerformance，因为 Azure 上的 SAP HANA（大型实例）仅支持这两个网关 SKU。

> [!IMPORTANT]
> 对于 SKU 类型 S384、S384m、S384xm、S576、S768 和 S960（II 类 SKU）的 HANA 大型实例，必须使用 UltraPerformance 网关 SKU。

### <a name="linking-vnets"></a>链接 VNet

在 Azure VNet 中创建 ExpressRoute 网关后，可以使用 Microsoft 提供的授权信息，将 ExpressRoute 网关连接到为此连接创建的“Azure 上的 SAP HANA（大型实例）”ExpressRoute 线路。 可以使用 Azure 门户或 PowerShell 执行这一步。 虽然建议使用门户，但下面介绍了 PowerShell 使用说明。 

- 请对每个连接使用不同的 AuthGUID，从而对每个 VNet 网关执行以下命令。 下面脚本中的前两个条目来自 Microsoft 提供的信息。 此外，AuthGUID 特定于每个 VNet 及其网关。 也就是说，如果要添加另一个 Azure VNet，需要获取将 HANA 大型实例连接到 Azure 的 ExpressRoute 线路的另一个 AuthID。 

```PowerShell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

若要将网关连接到与订阅关联的多个 ExpressRoute 线路，建议多次执行这一步。 例如，建议将同一个 VNet 网关连接到将 VNet 连接到本地网络的 ExpressRoute 线路。

## <a name="adding-more-ip-addresses-or-subnets"></a>添加更多 IP 地址或子网

可以使用 Azure 门户、PowerShell 或 CLI 添加多个 IP 地址或子网。

在这种情况下，建议将新 IP 地址范围作为新范围添加到 VNet 地址空间，而不是生成新的聚合范围。 在任一情况下，都需要将此项更改提交给 Microsoft，以便在客户端中将该新 IP 地址范围连接到 HANA 大型实例单元。 可以发起 Azure 支持请求来添加新的 VNet 地址空间。 收到确认后，请执行后续步骤。

若要通过 Azure 门户预览创建其他子网，请参阅[使用 Azure 门户预览版创建虚拟网络](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)一文；若要使用 PowerShell，请参阅[使用 PowerShell 创建虚拟网络](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)。

## <a name="adding-vnets"></a>添加 VNet

首次连接一个或多个 Azure VNet 后，可能需要添加其他用于访问 Azure 上的 SAP HANA（大型实例）的 VNet。 首先，请提交 Azure 支持请求，在该请求中，请包含指明特定 Azure 部署的具体信息，以及 Azure VNet 地址空间的 IP 地址空间范围。 然后，Azure 上的 SAP HANA 服务管理部门将提供所需的信息用于连接其他 VNet 和 ExpressRoute。 对于每个 VNet，需要使用唯一授权密钥连接到 HANA 大型实例 ExpressRoute 线路。

添加新 Azure VNet 的步骤：

1. 完成载入过程的第一步，具体请参阅“创建 Azure VNet”部分。
2. 完成载入过程的第二步，具体请参阅“创建网关子网”部分。
3. 发出 Azure 支持请求，并在其中添加新 VNet 的相关信息，同时请求获取新授权密钥，以便将其他 VNet 连接到 HANA 大型实例 ExpressRoute 线路。
4. 收到已完成授权的通知后，立即使用 Microsoft 提供的授权信息完成载入过程的第三步，具体请参阅“链接 VNet”部分。

## <a name="increasing-expressroute-circuit-bandwidth"></a>提高 ExpressRoute 线路带宽

咨询 Azure 上的 SAP HANA 服务管理部门。 如果有通知指出需要提高 Azure 上的 SAP HANA（大型实例）ExpressRoute 线路的带宽，请创建 Azure 支持请求。 （可以请求将单条线路的带宽提高到最大 10 Gbps）。完成该操作后，会收到通知；不需要采取其他措施即可在 Azure 中提高速度。

## <a name="adding-an-additional-expressroute-circuit"></a>添加其他 ExpressRoute 线路

如果有通知指出需要更多的 ExpressRoute 线路，请咨询 Azure 上的 SAP HANA 服务管理部门，提出 Azure 支持请求来创建新的 ExpressRoute 线路（并获取用于连接该线路的授权信息）。 必须在提出请求之前定义要在 VNet 上使用的地址空间，以便 Azure 上的 SAP HANA 服务管理部门能够提供授权信息。

新建线路且 Azure 上的 SAP HANA 服务管理部门完成配置后，便会收到通知，其中包含继续执行下一步所需的信息。 请遵循上面提供的步骤创建新的 VNet 并将其连接到此附加线路。 如果 Azure VNet 已连接到同一 Azure 区域中的另一条 Azure 上的 SAP HANA（大型实例）ExpressRoute 线路，则无法连接到此附加线路。

## <a name="deleting-a-subnet"></a>删除子网

若要删除 VNet 子网，可以使用 Azure 门户、PowerShell 或 CLI。 如果 Azure VNet IP 地址范围/Azure VNet 地址空间在聚合范围内，则不需要与 Microsoft 协调。 不过，VNet 仍会传播包含已删除的子网的 BGP 路由地址空间。 如果已将 Azure VNet IP 地址范围/Azure VNet 地址空间定义为多个 IP 地址范围，并且其中的某个范围已分配到已删除的子网，则应在 VNet 地址空间中删除该范围，随后告知 Azure 上的 SAP HANA 服务管理部门，让他们从允许 Azure 上的 SAP HANA（大型实例）通信的范围中删除该范围。

若要删除子网，请参阅[删除子网](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet)以获取有关创建子网的详细信息。

## <a name="deleting-a-vnet"></a>删除 VNet

若要删除虚拟网络，请参阅[删除虚拟网络](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network)。 Azure 上的 SAP HANA 服务管理部门将删除 Azure 上的 SAP HANA（大型实例）ExpressRoute 线路中的现有授权，并删除用来与 HANA 大型实例通信的 Azure VNet IP 地址范围/Azure VNet 地址空间。

删除 VNet 后，请提出 Azure 支持请求并提供要删除的 IP 地址空间范围。

为了确保删除所有组件，请删除以下各项：

- ExpressRoute 连接、VNet 网关、VNet 网关公共 IP 和 VNet

## <a name="deleting-an-expressroute-circuit"></a>删除 ExpressRoute 线路

若要删除附加的 Azure 上的 SAP HANA（大型实例）ExpressRoute 线路，请向 Azure 上的 SAP HANA 服务管理部门提出应删除此线路的 Azure 支持请求。 在 Azure 订阅中，可以根据需要删除或保留 VNet。 但是，必须删除 HANA 大型实例 ExpressRoute 线路与链接的 VNet 网关之间的连接。

如果还想要删除某个 VNet，请遵循上一部分中有关删除 VNet 的指导。


