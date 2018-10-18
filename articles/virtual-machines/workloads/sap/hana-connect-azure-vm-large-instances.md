---
title: 从虚拟机到 Azure SAP HANA（大型实例）的连接设置 | Microsoft Docs
description: 从虚拟机进行连接设置以使用 Azure SAP HANA（大型实例）。
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f0e09e6dfce5d0ede525f461193866219b7f9429
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2018
ms.locfileid: "44167595"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>将 Azure VM 连接到 HANA 大型实例

如 [Azure 上的 SAP HANA（大型实例）概述和体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)中所述，Azure 中包含 SAP 应用程序层的 HANA 大型实例最简单部署如下所示：

![连接到了 Azure 上的 SAP HANA（大型实例）和本地的 Azure VNet](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

仔细观察 Azure VNet 端，我们认识到需要以下信息：

- 将用于部署 VM 的 SAP 应用程序层的 Azure VNet 的定义。
- 这自然而然就意味着，已在 Azure VNet 中定义了一个要将 VM 部署到的默认子网。
- 创建的 Azure VNet 至少需要包含一个 VM 子网和一个 ExpressRoute 网关子网。 应按照下面各部分所述，为这些子网分配 IP 地址范围。

让我们更深入地了解如何为 HANA 大型实例创建 Azure VNet

## <a name="creating-the-azure-vnet-for-hana-large-instances"></a>为 HANA 大型实例创建 Azure VNet

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



## <a name="different-ip-address-ranges-to-be-defined"></a>要定义的不同 IP 地址范围 

在前面的部分中，我们已介绍了部署 HANA 大型实例所必需的一些 IP 地址范围。 不过，还有一些 IP 地址范围也很重要。 让我们更详细地了解。 在发送初始部署请求之前，需要定义以下 IP 地址（这些地址并非全部都要提交给 Microsoft）：

- **VNet 地址空间：** 如前所述，这是已分配（或打算分配）给要连接到 SAP HANA 大型实例环境的 Azure 虚拟网络 (VNet) 中的地址空间参数的一个或多个 IP 地址范围。 建议将此地址空间参数指定为多行值，其中包括 Azure VM 子网范围和 Azure 网关子网范围，如上图所示。 此范围不得与本地或服务器 IP 池或 ER-P2P 地址范围重叠。 如何获取这一个或多个 IP 地址范围？ 企业网络团队或服务提供程序应提供网络内部未使用的一个或多个 IP 地址范围。 例如，如果上面的 Azure VM 子网为 10.0.1.0/24，下面的 Azure 网关子网为 10.0.2.0/28，建议为 Azure VNet 地址空间指定两行：10.0.1.0/24 和 10.0.2.0/28。 尽管地址空间值可以聚合，仍建议将它们与子网范围匹配，以免将来在网络中的其他位置意外重用较大地址空间内的 IP 地址范围。 VNET 地址空间是需要在请求进行初始部署时提交给 Microsoft 的 IP 地址范围

- **Azure VM 子网 IP 地址范围：** 如前所述，这是已分配（或打算分配）给要连接到 SAP HANA 大型实例环境的 Azure VNET 中的 Azure VNet 子网参数的 IP 地址范围。 此 IP 地址范围用于向 Azure VM 分配 IP 地址。 此范围外的 IP 地址可以连接到 SAP HANA 大型实例服务器。 如果需要，可以使用多个 Azure VM 子网。 Microsoft 建议对每个 Azure VM 子网使用一个 /24 CIDR 块。 此地址范围必须是 Azure VNet 地址空间中使用的值的一部分。 如何获取此 IP 地址范围？ 企业网络团队或服务提供程序应提供网络内部当前未使用的 IP 地址范围。

- **VNet 网关子网 IP 地址范围：** 根据想要使用的功能，建议的大小为：
   - 超高性能 ExpressRoute 网关：/26 地址块 - II 类 SKU 所必需
   - 通过高性能 ExpressRoute 网关同时使用 VPN 和 ExpressRoute（或更小的规模）：/27 地址块
   - 其他所有情况：/28 地址块。 此地址范围必须是“VNet 地址空间”值中使用的值的一部分。 此地址范围必须是需要提交给 Microsoft 的“Azure VNet 地址空间”值中使用的值的一部分。 如何获取此 IP 地址范围？ 企业网络团队或服务提供程序应提供网络内部当前未使用的 IP 地址范围。 

- **用于 ER-P2P 连接的地址范围：** 这是 SAP HANA 大型实例 ExpressRoute (ER) P2P 连接所需的 IP 范围。 此 IP 地址范围必须是 /29 CIDR IP 地址范围。 此范围不得与本地或其他 Azure IP 地址范围重叠。 此 IP 地址范围用于设置从 Azure VNet ExpressRoute 网关到 SAP HANA 大型实例服务器的 ER 连接。 如何获取此 IP 地址范围？ 企业网络团队或服务提供程序应提供网络内部当前未使用的 IP 地址范围。 这是需要在请求进行初始部署时提交给 Microsoft 的 IP 地址范围
  
- **服务器 IP 池地址范围：** 此 IP 地址范围用于向 HANA 大型实例服务器分配单个 IP 地址。 建议的子网大小为 /24 CIDR 块 - 但如果需要，可将它缩小到提供 64 个 IP 地址的最小规模。 此范围内的前 30 个 IP 地址保留给 Microsoft 使用。 选择范围大小时，请务必考虑到这一事实。 此范围不得与本地或其他 Azure IP 地址重叠。 如何获取此 IP 地址范围？ 企业网络团队或服务提供商应会提供网络内部当前未使用的 IP 地址范围。 /24（推荐）唯一 CIDR 块，用于为 Azure 上的 SAP HANA（大型实例）分配所需的特定 IP 地址。 这是需要在请求进行初始部署时提交给 Microsoft 的 IP 地址范围
 
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

## <a name="next-steps-after-address-ranges-have-been-defined"></a>定义地址范围后的后续步骤
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

**后续步骤**

- 请参阅[将 VNet 连接到 HANA 大型实例 ExpressRoute](hana-connect-vnet-express-route.md)。