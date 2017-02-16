---
title: "如何在虚拟网络中使用 Azure API 管理"
description: "了解如何在 Azure API 管理中设置与虚拟网络的连接并通过它访问 Web 服务。"
services: api-management
documentationcenter: 
author: antonba
manager: erikre
editor: 
ms.assetid: 64b58f7b-ca22-47dc-89c0-f6bb0af27a48
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 40d9b0ee5a24e5503de19daa030bf1e8169dec24
ms.openlocfilehash: 58be070ea5d5f4ea9f6d9453a1adcc23c4b9b2a2


---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>如何在虚拟网络中使用 Azure API 管理
使用 Azure 虚拟网络 (VNET) 可将多个 Azure 资源置于可以控制其访问权限但无法通过 Internet 路由的网络中。 然后，可以使用各种 VPN 技术将这些网络连接到本地网络。 若要了解有关 Azure 虚拟网络的详细信息，请先了解以下信息：[Azure 虚拟网络概述](../virtual-network/virtual-networks-overview.md)。

可将 Azure API 管理连接到虚拟网络 (VNET)，以便可以访问网络中的后端服务，并从网络内部访问开发人员门户和 API 网关。

> [!NOTE]
> Azure API 管理同时支持经典 VNet 和 Azure Resource Manager VNet。
> 
> 

## <a name="enable-vpn"></a>启用 VNET 连接
> [!NOTE]
> **高级**层和**开发人员**层中提供了 VNET 连接。 若要在层之间切换，请在 Azure 门户中打开 API 管理服务，然后打开“规模和定价”选项卡。 在“定价层”部分下，选择“高级”层或“开发人员”层，然后单击“保存”。
>

若要启用 VNET 连接，请在 Azure 门户中打开 API 管理服务，然后打开“虚拟网络”页。

![API 管理的虚拟网络菜单][api-management-using-vnet-menu]

选择所需的访问类型：

* **外部**：可以通过外部负载均衡器从公共 Internet 访问 API 管理网关和开发人员门户。 网关可以访问虚拟网络中的资源。

![公共对等互连][api-management-vnet-public]

* **内部**：只能通过内部负载均衡器从虚拟网络内部访问 API 管理网关和开发人员门户。 网关可以访问虚拟网络中的资源。

![专用对等互连][api-management-vnet-private]

此时会显示一个列表，其中包含预配了 API 管理服务的所有区域。 选择每个区域的 VNET 和子网。 该列表中填充了在你配置的区域中设置的 Azure 订阅中可用的经典和 Resource Manager 虚拟网络。

> [!NOTE]
> 上图中的**服务终结点**包括网关/代理、发布者门户、开发人员门户、GIT 和直接管理终结点。
> 上图中的**管理终结点**是托管在服务上用于通过 Azure 门户和 Powershell 管理配置的终结点。
> 另请注意：即使图示中显示了各个终结点的 IP 地址，但 API 管理服务**仅**对其配置的主机名作出响应。

> [!IMPORTANT]
> 将 Azure API 管理实例部署到 Resource Manager VNET 时，该服务必须位于除了 Azure API 管理实例之外不包含其他资源的专用子网中。 如果尝试将 Azure API 管理实例部署到包含其他资源的 Resource Manager VNET 子网，则部署将失败。
> 
> 

![选择 VPN][api-management-setup-vpn-select]

单击屏幕顶部的“保存”。 

> [!NOTE]
> 每次启用或禁用 VNET 时，API 管理实例的 VIP 地址都会更改。  
> 当 API 管理从**外部**移动到**内部**时或者反向移动时，VIP 地址也会更改。
> 


> [!IMPORTANT] 
> 如果从 VNET 中删除 API 管理或更改在其中部署的 API 管理，则之前使用的 VNET 可最多 4 小时保持锁定状态。 在此期间，无法删除该 VNET 或向其部署新资源。

## <a name="enable-vnet-powershell"> </a>使用 PowerShell cmdlet 启用 VNET 连接
还可以使用 PowerShell cmdlet 启用 VNET 连接

* **在 VNET 内创建 API 管理服务**：使用 cmdlet [New-AzureRmApiManagement](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.apimanagement/v3.1.0/new-azurermapimanagement) 在 VNET 内创建 Azure API 管理服务。

* **在 VNET 内部署现有 API 管理服务**：使用 cmdlet [Update-AzureRmApiManagementDeployment](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.apimanagement/v3.1.0/update-azurermapimanagementdeployment) 将现有 Azure API 管理服务移动到虚拟网络内。

## <a name="connect-vnet"> </a>连接到虚拟网络中托管的 Web 服务
将 API 管理服务连接到 VNET 后，访问 VNET 中的后端服务与访问公共服务无异。 在创建新的 API 或编辑现有 API 时，只需将 Web 服务的本地 IP 地址或主机名（如果为 VNET 配置了 DNS 服务器）键入“Web 服务 URL”字段。

![从 VPN 添加 API][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"> </a>常见网络配置问题
下面是将 API 管理服务部署到虚拟网络时可能会发生的常见错误配置问题的列表。

* **自定义 DNS 服务器设置**：API 管理服务依赖于多项 Azure 服务。 当 API 管理托管在包含自定义 DNS 服务器的 VNET 中时，API 管理需要解析这些 Azure 服务的主机名。 请根据[此指南](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)进行自定义 DNS 设置。 有关参考信息，请参阅下面的端口表和其他网络要求。

> [!IMPORTANT]
> 如果对 VNET 使用自定义 DNS 服务器，建议在向其部署 API 管理服务**之前**完成该设置。 否则需要重启托管该服务的 CloudService，供其选取新的 DNS 服务器设置。
> 

* **API 管理所需的端口**：可以使用[网络安全组][Network Security Group]控制其中部署了 API 管理的子网的入站和出站流量。 如果其中的任一端口不可用，API 管理可能无法正常工作且不可访问。 在 VNET 中使用 API 管理时，另一个常见的错误配置问题是阻止了这些端口中的一个或多个。

在 VNET 中托管 API 管理服务实例时，将使用下表中的端口。

| 源 / 目标端口 | 方向 | 传输协议 | 目的 | 源/目标 | 访问类型 |
| --- | --- | --- | --- | --- | --- |
| 80、443 / 80、443 |入站 |TCP |客户端与 API 管理的通信 |INTERNET/VIRTUAL_NETWORK |外部 |
| * / 3443 |入站 |TCP |Azure 门户和 Powershell 的管理终结点 |INTERNET / VIRTUAL_NETWORK |外部和内部 |
| 80、443 / 80、443 |出站 |TCP |与 Azure 存储和 Azure 服务总线的依赖关系 |VIRTUAL_NETWORK/INTERNET |外部和内部 |
| 1433 / 1433 |出站 |TCP |与 Azure SQL 的依赖关系 |VIRTUAL_NETWORK/INTERNET |外部和内部 |
| 9350 - 9354 / 9350 - 9354 |出站 |TCP |与服务总线的依赖关系 |VIRTUAL_NETWORK/INTERNET |外部和内部 |
| 5671 / 5671 |出站 |AMQP |与事件中心策略日志记录的依赖项关系 |VIRTUAL_NETWORK/INTERNET |外部和内部 |
| 6381 - 6383 / 6381 - 6383 |入站和出站 |UDP |与 Redis 缓存的依赖关系 |VIRTUAL_NETWORK/VIRTUAL_NETWORK |外部和内部 |-
| * / 445 |出站 |TCP |与适用于 GIT 的 Azure 文件共享的依赖关系 |VIRTUAL_NETWORK/INTERNET |外部和内部 |
| * / * | 入站 |TCP |Azure 基础结构负载均衡器 | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK |外部和内部 |

* **SSL 功能**：若要启用 SSL 证书链构建和验证，API 管理服务需要到 ocsp.msocsp.com、mscrl.microsoft.com 和 crl.microsoft.com 的出站网络连接。

* **ExpressRoute 设置**：一种常见的客户配置是定义其自己的默认路由 (0.0.0.0/0)，以强制出站 Internet 流量改为流向本地。 此流量流一定会中断与 Azure API 管理的连接，因为已在本地阻止出站流量，或者已 NAT 到不再与各种 Azure 终结点一起工作的一组无法识别的地址。 解决方案是在包含 Azure API 管理的子网上定义一个（或多个）用户定义的路由 ([UDR][UDRs])。 UDR 定义了要遵循的子网特定路由，而不是默认路由。
  如果可能，建议使用以下配置：
 * ExpressRoute 配置播发 0.0.0.0/0 并默认使用强制隧道将所有输出流量发送到本地。
 * 应用于包含 Azure API 管理的子网的 UDR 定义 0.0.0.0/0 以及 Internet 的下一个跃点类型。
 这些步骤的组合效应是子网级 UDR 将优先于 ExpressRoute 强制隧道，从而确保来自 Azure API 管理的出站 Internet 访问。

>[!WARNING]  
>**从公共对等路径到专用对等路径未正确交叉播发路由**的 ExpressRoute 配置不支持 Azure API 管理。 已配置公共对等互连的 ExpressRoute 配置将收到来自 Microsoft 的大量 Microsoft Azure IP 地址范围的路由播发。 如果这些地址范围在专用对等路径上未正确交叉播发，最后的结果是来自 Azure API 管理实例子网的所有出站网络数据包都不会正确地使用强制隧道发送到客户的本地网络基础结构。 此网络流会破坏 Azure API 管理。 此问题的解决方法是停止从公共对等路径到专用对等路径的交叉播发路由。

## <a name="limitations"> </a>限制
* 包含 API 管理实例的子网不能包含其他任何 Azure 资源类型。
* 子网和 API 管理服务必须在同一个订阅中。
* 无法在订阅之间移动包含 API 管理实例的子网。
* 使用内部虚拟网络时，只能从 [RFC 1918](https://tools.ietf.org/html/rfc1918) 所述的范围中使用一个内部 IP 地址，不能提供公共 IP 地址。
* 对于配置了内部虚拟网络的多区域 API 管理部署，用户需负责管理自己的负载均衡，因为 DNS 由他们拥有。


## <a name="related-content"></a>相关内容
* [使用 Vpn 网关将虚拟网络连接到后端][Different topologies to connect to Vpn Gateway]
* [通过不同的部署模型连接虚拟网络](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [如何使用 API 检查器跟踪 Azure API 管理中的调用](api-management-howto-api-inspector.md)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-type.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-private.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-public.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[Different topologies to connect to Vpn Gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site-connections
[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/virtual-networks-nsg.md



<!--HONumber=Jan17_HO2-->


