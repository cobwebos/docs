---
title: 从虚拟机到 Azure SAP HANA（大型实例）的连接设置 | Microsoft Docs
description: 从虚拟机进行连接设置以使用 Azure SAP HANA（大型实例）。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fb6f88fbfcbd539603e435b11661c428d54f3c34
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224732"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>将 Azure VM 连接到 HANA 大型实例

[什么是 Azure 上的 SAP HANA（大型实例）？](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)一文 提到 Azure 中使用 SAP 应用程序层的 HANA 大型实例的最小部署如下所示：

![连接到了 Azure 上的 SAP HANA（大型实例）和本地的 Azure VNet](./media/hana-overview-architecture/image1-architecture.png)

Looking closer at the Azure virtual network side, there is a need for:

- SAP 应用程序层 VM 要部署到的 Azure 虚拟网络的定义。
- 该 Azure 虚拟网络中，VM 真正要部署到的默认子网的定义。
- 创建的 Azure 虚拟网络至少需要包含一个 VM 子网和一个 Azure ExpressRoute 虚拟网络网关子网。 应按照下面各部分所述，为这些子网分配 IP 地址范围。


## <a name="create-the-azure-virtual-network-for-hana-large-instances"></a>为 HANA 大型实例创建 Azure 虚拟网络

>[!Note]
>必须使用 Azure 资源管理器部署模型为 HANA 大型实例创建 Azure 虚拟网络。 HANA 大型实例解决方案不支持旧式 Azure 部署模型（通常称为“经典部署模型”）。

可以使用 Azure 门户、PowerShell、Azure 模板或 Azure CLI 创建虚拟网络。 （有关详细信息，请参阅[使用 Azure 门户创建虚拟网络](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)）。 以下示例演示如何使用 Azure 门户创建虚拟网络。

When referring to the **address space** in this documentation, to the address space that the Azure virtual network is allowed to use. 此地址空间也是虚拟网络用于 BGP 路由传播的地址范围。 可在以下位置看到**地址空间**：

![Azure 门户中显示的 Azure 虚拟网络地址空间](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

In the previous example, with 10.16.0.0/16, the Azure virtual network was given a rather large and wide IP address range to use. 因此，此虚拟网络中后续子网中的所有 IP 地址范围可在该地址空间中包含其范围。 通常，对于 Azure 中的单个虚拟网络，我们不建议使用这么大的地址范围。 但是，让我们继续分析 Azure 虚拟网络中定义的子网：

![Azure 虚拟网络子网及其 IP 地址范围](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

可以看到，虚拟网络包含两个子网，第一个是 VM 子网（名为“default”），第二个子网名为“GatewaySubnet”。

In the two previous graphics, the **virtual network address space** covers both **the subnet IP address range of the Azure VM** and that of the virtual network gateway.

可以将虚拟网络地址空间限制为每个子网使用的特定范围。 也可将虚拟网络的虚拟网络地址空间定义为多个特定范围，如下所示：

![包含两个空间的 Azure 虚拟网络地址空间](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

在本例中，为**虚拟网络地址空间**定义了两个空间。 这两个空间的 IP 地址范围与定义的 Azure VM 子网 IP 地址范围和虚拟网络网关子网 IP 地址范围相同。 

对于这些租户子网（VM 子网），可以使用偏好的任何命名标准。 但是，对于连接到 Azure 上的 SAP HANA（大型实例）ExpressRoute 线路的每个虚拟网络，**必须始终只有一个网关子网**。 **This gateway subnet has to be named "GatewaySubnet"** to make sure that the ExpressRoute gateway is properly placed.

> [!WARNING] 
> It's critical that the gateway subnet always be named "GatewaySubnet".

可以使用多个 VM 子网和不连续的地址范围。 虚拟网络的**虚拟网络地址空间**必须涵盖这些地址范围。 可以是以聚合形式。 也可以是以 VM 子网和网关子网的确切范围列表形式。

下面汇总了有关要连接到 HANA 大型实例的 Azure 虚拟网络的重要事实：

- You must submit the **virtual network address space** to Microsoft  when you're performing an initial deployment of HANA Large Instances. 
- 虚拟网络地址空间可以是涵盖 Azure VM 子网 IP 地址范围和虚拟网络网关子网 IP 地址范围的较大范围。
- 或者，可以提交涵盖不同 VM 子网 IP 地址范围和虚拟网络网关 IP 地址范围的多个范围。
- 定义的**虚拟网络地址空间**用于 BGP 路由传播。
- 必须将网关子网命名为 **“GatewaySubnet”** 。
- 地址空间用作 HANA 大型实例端上的筛选器，用于允许或禁止 Azure 的流量传入 HANA 大型实例单位。 Azure 虚拟网络的 BGP 路由信息应与配置以用于对 HANA 大型实例进行筛选的 IP 地址范围相匹配。 否则可能会出现连接问题。
- 后面的“将虚拟网络连接到 HANA 大型实例 ExpressRoute”部分中详细介绍了网关子网。



## <a name="different-ip-address-ranges-to-be-defined"></a>要定义的不同 IP 地址范围 

Some of the IP address ranges that are necessary for deploying HANA Large Instances got introduced already. 但是，还有一些 IP 地址范围也很重要。 Not all of the following IP address ranges need to be submitted to Microsoft. 但是，在发送初始部署请求之前，需要定义这些 IP 地址：

- **Virtual network address space**: The **virtual network address space** is the IP address ranges that you assign to your address space parameter in the Azure virtual networks. 这些网络连接到 SAP HANA 大型实例环境。 我们建议将此地址空间参数设置为多行值。 它应该由 Azure VM 的子网范围和 Azure 网关的子网范围组成。 此子网范围显示在前面的图中。 此范围不得与本地或服务器 IP 池或 ER-P2P 地址范围重叠。 如何获取这些 IP 地址范围？ Your corporate network team or service provider should provide one or multiple IP address range(s) that aren't used inside your network. For example, the subnet of your Azure VM  is 10.0.1.0/24, and the subnet of your Azure gateway subnet is 10.0.2.0/28.  We recommend that your Azure virtual network address space is defined as: 10.0.1.0/24 and 10.0.2.0/28. 尽管地址空间值可以聚合，仍我们建议使它们与子网范围匹配。 这样就可以避免在网络中其他较大的地址空间中重用空闲的 IP 地址范围。 **The virtual network address space is an IP address range. It needs to be submitted to Microsoft when you ask for an initial deployment**.
- **Azure VM subnet IP address range:** This IP address range is the one you assign to the Azure virtual network subnet parameter. 此参数位于 Azure 虚拟网络中，并连接到 SAP HANA 大型实例环境。 此 IP 地址范围用于向 Azure VM 分配 IP 地址。 此范围外的 IP 地址可以连接到 SAP HANA 大型实例服务器。 如果需要，可以使用多个 Azure VM 子网。 我们建议对每个 Azure VM 子网使用一个 /24 CIDR 块。 此地址范围必须是 Azure 虚拟网络地址空间中使用的值的一部分。 如何获取此 IP 地址范围？ 企业网络团队或服务提供商应会提供网络内部未使用的 IP 地址范围。
- **虚拟网络网关子网 IP 地址范围：** 根据想要使用的功能，建议的大小为：
   - 超高性能 ExpressRoute 网关：/26 地址块 -- II 类 SKU 所必需。
   - 通过高性能 ExpressRoute 虚拟网络网关同时使用 VPN 和 ExpressRoute（或更小的规模）：/27 地址块。
   - 其他所有情况：/28 地址块。 此地址范围必须是“VNet 地址空间”值中使用的值的一部分。 此地址范围必须是提交给 Microsoft 的 Azure 虚拟网络地址空间值中使用的值的一部分。 如何获取此 IP 地址范围？ 企业网络团队或服务提供程序应提供网络内部当前未使用的 IP 地址范围。 
- **用于 ER-P2P 连接的地址范围：** 这是 SAP HANA 大型实例 ExpressRoute (ER) P2P 连接所需的 IP 范围。 此 IP 地址范围必须是 /29 CIDR IP 地址范围。 此范围不得与本地或其他 Azure IP 地址范围重叠。 此 IP 地址范围用于设置从 ExpressRoute 虚拟网关到 SAP HANA 大型实例服务器的 ER 连接。 如何获取此 IP 地址范围？ 企业网络团队或服务提供程序应提供网络内部当前未使用的 IP 地址范围。 **This range is an IP address range. It needs to be submitted to Microsoft when you ask for an initial deployment**.  
- **服务器 IP 池地址范围：** 此 IP 地址范围用于向 HANA 大型实例服务器分配单个 IP 地址。 建议的子网大小为 /24 CIDR 块。 如果需要，可将它缩小为 64 个 IP 地址。 此范围内的前 30 个 IP 地址保留给 Microsoft 使用。 选择范围大小时，请务必考虑到这一事实。 此范围不得与本地或其他 Azure IP 地址重叠。 如何获取此 IP 地址范围？ 企业网络团队或服务提供程序应提供网络内部当前未使用的 IP 地址范围。  **这是需要在请求进行初始部署时提交给 Microsoft 的 IP 地址范围**。

Optional IP address ranges that eventually need to be submitted to Microsoft:

- If you choose to use [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) to enable direct routing from on-premises to HANA Large Instance units, you need to reserve another /29 IP address range. This range may not overlap with any of the other IP address ranges you defined before.
- If you choose to use [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) to enable direct routing from a HANA Large Instance tenant in one Azure region to another HANA Large Instance tenant in another Azure region, you need to reserve another /29 IP address range. This range may not overlap with any of the other IP address ranges you defined before.

For more information about ExpressRoute Global Reach and usage around HANA large instances, check the documents:

- [SAP HANA (Large Instances) network architecture](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture)
- [Connect a virtual network to HANA large instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)
 
需要定义和计划前面所述的 IP 地址范围。 但是，不必将其全部发送给 Microsoft。 需要提交到 Microsoft 的 IP 地址范围包括：

- Azure 虚拟网络地址空间
- 用于 ER-P2P 连接的地址范围
- 服务器 IP 池地址范围

如果添加需要连接到 HANA 大型实例的其他虚拟网络，则必须将所要添加的新 Azure 虚拟网络地址空间提交给 Microsoft。 

下面的示例展示了不同的范围，以及需要配置并最终提供给 Microsoft 的一些示例范围。 第一个示例中，Azure 虚拟网络地址空间的值没有聚合。 但是，它是根据第一个 Azure VM 子网 IP 地址范围和虚拟网络网关子网 IP 地址范围进行定义的。 

配置并提交其他 VM 子网的其他 IP 地址范围作为 Azure 虚拟网络地址空间的一部分时，可以使用 Azure 虚拟网络中的多个 VM 子网。

![Azure 上的 SAP HANA（大型实例）最小部署中所需的 IP 地址范围](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

The graphic does not show the additional IP address range(s) that are required for the optional use of ExpressRoute Global Reach.

还可以聚合要提交给 Microsoft 的数据。 在这种情况下，Azure 虚拟网络的地址空间只包含一个空间。 沿用上面示例中的 IP 地址范围，聚合的虚拟网络地址空间如下图所示：

![Azure 上的 SAP HANA（大型实例）最小部署中所需 IP 地址范围的另一种可能情况](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

在示例中，我们定义了一个涵盖 4096 个 IP 地址的较大范围，而不是为 Azure 虚拟网络的地址空间定义了两个较小范围。 定义这种较大的地址空间会使一些相当大的范围不被利用。 由于虚拟网络地址空间值用于 BGP 路由传播，使用未在本地或网络中其他位置使用的范围可能会导致路由问题。 The graphic does not show the additional IP address range(s) that are required for the optional use of ExpressRoute Global Reach.

We recommend that you keep the address space tightly aligned with the actual subnet address space that you use. 如果需要，以后随时可以添加新的地址空间值，而不会导致虚拟网络出现故障。
 
> [!IMPORTANT] 
> ER-P2P、服务器 IP 池、Azure 虚拟网络地址空间中的每个 IP 地址范围既**不得**相互重叠，也不得与网络中使用的其他任何范围重叠。 每个范围都必须是独立的。 如前面两幅插图所示，它们也不能是其他任何范围的子网。 如果范围之间存在重叠，Azure 虚拟网络可能无法连接到 ExpressRoute 线路。

## <a name="next-steps-after-address-ranges-have-been-defined"></a>定义地址范围后的后续步骤
定义 IP 地址范围后，需要执行以下操作：

1. 连同本文档开头部分列出的其他数据一起提交 Azure 虚拟网络地址空间、ER-P2P 连接和服务器 IP 池地址范围的 IP 地址范围。 此时，还可以开始创建虚拟网络和 VM 子网。 
2. ExpressRoute 线路是由 Microsoft 在 Azure 订阅与 HANA 大型实例阵列之间创建的。
3. 租户网络由 Microsoft 在大型实例戳记上创建。
4. Microsoft 会在 Azure 上的 SAP HANA（大型实例）基础结构中配置网络，以接受要与 HANA 大型实例通信的 Azure 虚拟网络地址空间中的 IP 地址。
5. Microsoft 会在租户网络中分配计算单元，具体视购买的 Azure 上的 SAP HANA（大型实例）SKU 而定。 还会分配并装载存储，并安装操作系统（SUSE 或 Red Hat Linux）。 这些单元的 IP 地址将从提交给 Microsoft 的服务器 IP 池地址范围中排除。

部署过程结束时，Microsoft 将提供以下数据：
- 将 Azure 虚拟网络连接到 ExpressRoute 线路，然后通过该线路将 Azure 虚拟网络连接到 HANA 大型实例所需的信息：
     - 授权密钥
     - ExpressRoute 对等互连 ID
- 建立 ExpressRoute 线路和 Azure 虚拟网络后用于访问 HANA 大型实例的数据。

在文档 [Azure 上的 SAP HANA（大型实例）设置](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/)中还可以找到 HANA 大型实例的连接顺序。 这篇文档中的示例部署展示了以下许多步骤。 

## <a name="next-steps"></a>后续步骤

- 参阅[将虚拟网络连接到 HANA 大型实例 ExpressRoute](hana-connect-vnet-express-route.md)。
