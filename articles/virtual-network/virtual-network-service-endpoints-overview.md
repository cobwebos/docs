---
title: Azure 虚拟网络服务终结点
titlesuffix: Azure Virtual Network
description: 了解如何使用服务终结点通过虚拟网络直接访问 Azure 资源。
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2019
ms.author: sumi
ms.custom: ''
ms.openlocfilehash: 72c2c90f7a71bd9bf251adb492168fa5d2baa60a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78380134"
---
# <a name="virtual-network-service-endpoints"></a>虚拟网络服务终结点

虚拟网络（VNet）服务终结点可扩展虚拟网络专用地址空间。 终结点还通过直接连接将 VNet 的标识扩展到 Azure 服务。 使用终结点可以保护关键的 Azure 服务资源，只允许在客户自己的虚拟网络中对其进行访问。 从 VNet 发往 Azure 服务的流量始终保留在 Microsoft Azure 主干网络中。

此功能适用于以下 Azure 服务和区域。 *\** 的资源在括号中。 为服务配置服务终结点时，从子网端启用此资源：

**正式发布版**

- **[Azure 存储](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** （*Microsoft 存储*）：在所有 azure 区域公开发布。
- **[AZURE SQL 数据库](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** （*Microsoft .sql*）：在所有 azure 区域公开发布。
- **[AZURE SQL 数据仓库](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** （*sql-dmo*）：在所有 azure 区域公开发布。
- **[Azure Database for PostgreSQL server](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** （*Microsoft .sql*）：在数据库服务可用的 Azure 区域中公开发布。
- **[Azure Database for MySQL server](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** （*Microsoft .sql*）：在数据库服务可用的 Azure 区域中公开发布。
- **[Azure Database for MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)** （*Microsoft .sql*）：在数据库服务可用的 Azure 区域中公开发布。
- **[Azure Cosmos DB](../cosmos-db/vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** （*AzureCosmosDB*）：在所有 Azure 区域公开发布。
- **[Azure Key Vault](../key-vault/key-vault-overview-vnet-service-endpoints.md)** （*KeyVault*）：在所有 Azure 区域公开发布。
- **[Azure 服务总线](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** （*Microsoft*服务总线）：在所有 azure 区域公开发布。
- **[Azure 事件中心](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** （*Microsoft EventHub*）：已在所有 azure 区域公开发布。
- **[Azure Data Lake Store 第1代](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** （*AzureActiveDirectory*）：在可用 ADLS Gen1 的所有 Azure 区域中公开发布。
- **[Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)** ：在应用服务可用的所有 Azure 区域中公开发布。

**公共预览版**

- **[Azure 容器注册表](../container-registry/container-registry-vnet.md)** （*Microsoft.containerregistry*）：预览版在 azure 容器注册表可用的所有 azure 区域中可用。

有关最新通知，请查看 [Azure 虚拟网络更新](https://azure.microsoft.com/updates/?product=virtual-network)页。

## <a name="key-benefits"></a>主要优点

服务终结点提供以下优势：

- **提高 Azure 服务资源的安全性**： VNet 专用地址空间可能重叠。 不能使用重叠空间来唯一标识源自 VNet 的流量。 服务终结点可以通过将 VNet 标识扩展到服务，来保护虚拟网络的 Azure 服务资源。 在虚拟网络中启用服务终结点后，可以添加虚拟网络规则，以便将 Azure 服务资源保护到虚拟网络。 规则添加通过完全删除对资源的公共 internet 访问并只允许来自你的虚拟网络的流量，提高了安全性。
- **来自虚拟网络的 Azure 服务流量的最佳路由**：目前，虚拟网络中强制 internet 流量强制 internet 流量的任何路由也会强制 Azure 服务流量采用与 internet 流量相同的路由。 服务终结点为 Azure 流量提供最佳路由。 

  终结点始终将直接来自虚拟网络的服务流量转发到 Microsoft Azure 主干网络上的服务。 将流量保留在 Azure 主干网络上可以通过强制隧道持续审核和监视来自虚拟网络的出站 Internet 流量，而不会影响服务流量。 有关用户定义的路由和强制隧道的详细信息，请参阅[Azure 虚拟网络流量路由](virtual-networks-udr-overview.md)。
- **设置简单，管理开销更少**：不再需要使用虚拟网络中的保留公共 IP 地址通过 IP 防火墙保护 Azure 资源。 不需要网络地址转换（NAT）或网关设备即可设置服务终结点。 你可以通过在子网上简单单击即可配置服务终结点。 维护终结点不会产生额外的开销。

## <a name="limitations"></a>限制

- 该功能仅适用于使用 Azure 资源管理器部署模型部署的虚拟网络。
- 终结点在 Azure 虚拟网络中配置的子网上启用。 终结点不能用于从你的本地到 Azure 服务的流量。 有关详细信息，请参阅[保护从本地进行的 Azure 服务访问](#secure-azure-services-to-virtual-networks)
- 对于 Azure SQL，服务终结点仅适用于虚拟网络区域中的 Azure 服务流量。 对于 Azure 存储，终结点还扩展到包含成对区域，在这些区域中，你将部署虚拟网络以支持读取访问异地冗余存储（GRS）和异地冗余存储（GRS）流量。 有关详细信息，请参阅 [Azure 配对区域](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions)。
- 对于 Azure Data Lake Storage （ADLS）第1代，VNet 集成功能仅适用于同一区域中的虚拟网络。 另请注意，ADLS Gen1 的虚拟网络集成使用虚拟网络和 Azure Active Directory （Azure AD）之间的虚拟网络服务终结点安全，在访问令牌中生成附加的安全声明。 然后，系统会使用这些声明对 Data Lake Storage Gen1 帐户进行虚拟网络身份验证，然后允许访问。 服务支持服务终结点下列出的*AzureActiveDirectory*标记仅用于支持服务终结点到 ADLS 第1代。 Azure AD 不能以本机方式支持服务终结点。 有关 Azure Data Lake Store 第1代 VNet 集成的详细信息，请参阅[Azure Data Lake Storage Gen1 中的网络安全](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="secure-azure-services-to-virtual-networks"></a>保护虚拟网络的 Azure 服务

- 虚拟网络服务终结点为 Azure 服务提供虚拟网络的标识。 在虚拟网络中启用服务终结点后，可以添加虚拟网络规则，以便将 Azure 服务资源保护到虚拟网络。
- 当前，来自虚拟网络的 Azure 服务流量使用公共 IP 地址作为源 IP 地址。 使用服务终结点时，服务流量会在通过虚拟网络访问 Azure 服务时改用虚拟网络专用地址作为源 IP 地址。 通过这种切换，无需 IP 防火墙中使用的保留公共 IP 地址即可访问服务。

   >[!NOTE]
   > 使用服务终结点时，服务流量的子网中虚拟机的源 IP 地址将从公共 IPv4 地址改为专用 IPv4 地址。 使用 Azure 公共 IP 地址的现有 Azure 服务防火墙规则将无法继续适用于此交换机。 请确保 Azure 服务防火墙规则允许此交换机，然后再设置服务终结点。 在配置服务终结点时，可能会遇到来自此子网的服务流量出现暂时性中断的情况。 
 
## <a name="secure-azure-service-access-from-on-premises"></a>保护从本地进行的 Azure 服务访问

  默认情况下，无法从本地网络访问在虚拟网络中保护的 Azure 服务资源。 要允许来自本地的流量，还必须允许来自本地或 ExpressRoute 的公共（通常为 NAT）IP 地址。 可以通过 Azure 服务资源的 IP 防火墙配置添加这些 IP 地址。

  ExpressRoute：如果你要将[ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)用于公共对等互连或 Microsoft 对等互连，则需要识别你使用的 NAT IP 地址。 对于公共对等互连，默认情况下，每条 ExpressRoute 线路都使用两个 NAT IP 地址，当流量进入 Microsoft Azure 网络主干时，它们将应用于 Azure 服务流量。 对于 Microsoft 对等互连，NAT IP 地址为客户提供或由服务提供商提供。 若要允许访问服务资源，必须在资源 IP 防火墙设置中允许这些公共 IP 地址。 若要查找公共对等 ExpressRoute 线路 IP 地址，请通过 Azure 门户[使用 ExpressRoute 打开支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。 有关适用于 ExpressRoute 公共和 Microsoft 对等互连的 NAT 的详细信息，请参阅[EXPRESSROUTE NAT 要求](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)。

![在虚拟网络中保护 Azure 服务](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>配置

- 在虚拟网络中的子网上配置服务终结点。 终结点可以处理该子网中运行的任何类型的计算实例。
- 可以为子网上的所有受支持的 Azure 服务（例如 Azure 存储或 Azure SQL 数据库）配置多个服务终结点。
- 对于 Azure SQL 数据库，虚拟网络必须与 Azure 服务资源位于同一区域。 如果使用 GRS 和 RA-GRS Azure 存储帐户，则主帐户必须与虚拟网络位于同一区域。 对于所有其他服务，你可以在任何区域中的虚拟网络中保护 Azure 服务资源。 
- 配置了终结点的虚拟网络可与 Azure 服务资源位于相同或不同的订阅中。 有关设置终结点和保护 Azure 服务时所需的权限的详细信息，请参阅[预配](#provisioning)。
- 对于受支持的服务，可以使用服务终结点在虚拟网络中保护新的或现有的资源。

### <a name="considerations"></a>注意事项

- 启用服务终结点后，子网交换机中虚拟机的源 IP 地址。 源 IP 地址在与来自该子网的服务进行通信时，从使用公用 IPv4 地址转换为使用其专用 IPv4 地址。 在进行这种切换的过程中，与服务建立的所有现有打开的 TCP 连接将会关闭。 针对子网的服务启用或禁用服务终结点时，请确保未运行任何关键任务。 此外，请确保在完成 IP 地址切换后，应用程序可以自动连接到 Azure 服务。

  IP 地址切换只会影响自己的虚拟网络发出的服务流量， 对于发往或来自分配给虚拟机的公共 IPv4 地址的其他任何流量，不会产生任何影响。 对于 Azure 服务，如果现有的防火墙规则使用 Azure 公共 IP 地址，这些规则会阻止切换到虚拟网络专用地址。
- 对于服务终结点，Azure 服务的 DNS 条目保持原样，并继续解析为分配给 Azure 服务的公共 IP 地址。

- 使用服务终结点的网络安全组 (NSG)：
  - 默认情况下，Nsg 允许出站 internet 流量，并允许从 VNet 到 Azure 服务的流量。 此流量继续与服务终结点一起使用。 
  - 如果你想要拒绝所有出站 internet 流量，并且仅允许流量流向特定的 Azure 服务，则可以使用 Nsg 中的[服务标记](security-overview.md#service-tags)来执行此操作。 你可以在 NSG 规则中将受支持的 Azure 服务指定为目标，而 Azure 还会提供每个标记基础的 IP 地址的维护。 有关详细信息，请参阅 [NSG 的 Azure 服务标记](security-overview.md#service-tags)。 

### <a name="scenarios"></a>方案

- **对等互连的、连接的或多个虚拟网络**：若要在一个虚拟网络中的多个子网内或者跨多个虚拟网络保护 Azure 服务，可以针对每个子网单独启用服务终结点，在所有子网中保护 Azure 服务资源。
- **筛选从虚拟网络到 azure 服务的出站流量**：如果想要检查或筛选从虚拟网络发往 Azure 服务的流量，可以在虚拟网络中部署网络虚拟设备。 然后，可将服务终结点应用到部署了网络虚拟设备的子网，只在该子网中保护 Azure 服务资源。 如果要使用网络虚拟设备筛选将 Azure 服务访问限制为仅限于特定的 Azure 资源，此方案可能很有帮助。 有关详细信息，请阅读[网络虚拟设备出口](/azure/architecture/reference-architectures/dmz/nva-ha)一文。
- **为直接部署到虚拟网络中的服务保护 Azure 资源**：可以将各种 azure 服务直接部署到虚拟网络中的特定子网。 可以通过在托管服务子网上设置服务终结点，在[托管服务](virtual-network-for-azure-services.md)子网中保护 Azure 服务资源。
- **来自 Azure 虚拟机的磁盘流量**：托管和非托管磁盘的虚拟机磁盘流量不受 azure 存储的服务终结点路由更改的影响。 此流量包括 diskIO 以及装载和卸载。 可以通过服务终结点和[Azure 存储网络规则](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)限制对页 BLOB 的 REST 访问。 

### <a name="logging-and-troubleshooting"></a>日志记录和故障排除

将服务终结点配置到特定服务后，请验证服务终结点路由是否生效： 
 
- 验证服务诊断中任何服务请求的源 IP 地址。 使用服务终结点的所有新请求会将请求的源 IP 地址显示为分配给从虚拟网络发出请求的客户端的虚拟网络专用地址。 如果不使用终结点，此地址是 Azure 公共 IP 地址。
- 查看子网中任何网络接口上的有效路由。 服务的路由：
  - 显示更具体的默认路由用于寻址每个服务的前缀范围
  - nextHopType 为 *VirtualNetworkServiceEndpoint*
  - 表示与任何强制隧道路由相比，与该服务的直接连接有效

>[!NOTE]
> 服务终结点路由会替代与 Azure 服务匹配的地址前缀的 BGP 或 UDR 路由。 有关详细信息，请参阅[通过有效路由进行故障排除](diagnose-network-routing-problem.md)。

## <a name="provisioning"></a>设置

可以在虚拟网络上单独配置服务终结点，该用户具有对虚拟网络的写访问权限。 若要在 VNet 中保护 Azure 服务资源，用户必须对所添加的子网具有*virtualNetworks/joinViaServiceEndpoint/subnet//action*权限。 默认情况下，内置服务管理员角色包括此权限。 可以通过创建自定义角色来修改权限。

有关内置角色的详细信息，请参阅[Azure 资源的内置角色](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 有关将特定权限分配给自定义角色的详细信息，请参阅[Azure 资源的自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

虚拟网络和 Azure 服务资源可以位于相同或不同的订阅中。 如果虚拟网络和 Azure 服务资源位于不同的订阅中，资源必须在相同的 Active Directory (AD) 租户下。 

## <a name="pricing-and-limits"></a>定价和限制

使用服务终结点不会产生额外的费用。 Azure 服务（Azure 存储、Azure SQL 数据库等）的当前定价模型现在按原样应用。

虚拟网络中的服务终结点总数没有限制。

某些 Azure 服务（例如 Azure 存储帐户）可能会对用于保护资源的子网数目施加限制。 有关详细信息，请参阅[后续步骤](#next-steps)部分中的各种服务的文档。

## <a name="vnet-service-endpoint-policies"></a>VNet 服务终结点策略 

VNet 服务终结点策略允许你筛选 Azure 服务的虚拟网络流量。 此筛选器只允许服务终结点上的特定 Azure 服务资源。 服务终结点策略针对发往 Azure 服务的虚拟网络流量提供精细的访问控制。 有关详细信息，请参阅[虚拟网络服务终结点策略](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)。

## <a name="faqs"></a>常见问题解答

有关常见问题，请参阅[虚拟网络服务终结点常见问题解答](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#virtual-network-service-endpoints)。

## <a name="next-steps"></a>后续步骤

- [配置虚拟网络服务终结点](tutorial-restrict-network-access-to-resources.md)
- [将 Azure 存储帐户保护到虚拟网络](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [保护 Azure SQL 数据库到虚拟网络的安全](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [保护虚拟网络的 Azure SQL 数据仓库](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [虚拟网络中的 Azure 服务集成](virtual-network-for-azure-services.md)
- [虚拟网络服务终结点策略](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)
- [Azure Resource Manager 模板](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)

