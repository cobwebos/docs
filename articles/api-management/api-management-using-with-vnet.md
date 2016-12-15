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
ms.date: 10/20/2016
ms.author: antonba
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7dfbf0e460dc8de13a4a71c1f925c93f6238df5d


---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>如何在虚拟网络中使用 Azure API 管理
使用 Azure 虚拟网络 (VNET) 可将多个 Azure 资源置于可以控制其访问权限但无法通过 Internet 路由的网络中。 然后，你可以使用多种 VPN 技术将这些网络连接到本地网络。 若要了解有关 Azure 虚拟网络的详细信息，请先了解以下信息：[Azure 虚拟网络概述](../virtual-network/virtual-networks-overview.md)。

可将 Azure API 管理连接到虚拟网络 (VNET)，以便可以访问网络中的后端服务，并从网络内部访问开发人员门户和 API 网关。

## <a name="enable-vpn"></a>启用 VNET 连接
> 只有**高级**层和**开发人员**层才提供 VNET 连接。 若要在层之间切换，请在 Azure 门户中打开 API 管理服务，然后打开“规模和定价”选项卡。 在“定价层”部分下面选择“高级”层，然后单击“保存”。
> 
> 

若要启用 VNET 连接，请在 Azure 门户中打开 API 管理服务，然后打开“虚拟网络”页。

![API 管理的虚拟网络菜单][api-management-using-vnet-menu]

选择所需的访问类型：

* **外部**：可以通过外部负载均衡器从公共 Internet 访问 API 管理网关和开发人员门户。 网关可以访问虚拟网络中的资源。

![公共对等互连][api-management-vnet-public]

* **内部**：只能通过内部负载均衡器从虚拟网络内部访问 API 管理网关和开发人员门户。 网关可以访问虚拟网络中的资源。

![专用对等互连][api-management-vnet-private]

此时会显示一个列表，其中包含预配了 API 管理服务的所有区域。 选择每个区域的 VNET 和子网。 该列表中填充了 Azure 订阅中提供的经典和 ARM 虚拟网络，这些网络是在配置的区域中设置的。

![选择 VPN][api-management-setup-vpn-select]

单击屏幕顶部的“保存”。 

> 在更新时，无法针对 API 管理服务执行管理操作。 API 管理网关和开发人员门户将保持可用。
> 请注意，每次启用或禁用 VNET 时，API 管理实例的 VIP 地址可能更改。
> 
> 

## <a name="enable-vnet-powershell"> </a>使用 PowerShell cmdlet 启用 VNET 连接
还可以使用 PowerShell cmdlet [Set-AzureRmApiManagementVirtualNetworks](https://msdn.microsoft.com/library/mt619277.aspx) 启用 VNET 连接。

## <a name="connect-vnet"> </a>连接到虚拟网络中托管的 Web 服务
将 API 管理服务连接到 VNET 后，访问 VNET 中的后端服务与访问公共服务无异。 在创建新的 API 或编辑现有 API 时，只需将 Web 服务的本地 IP 地址或主机名（如果为 VNET 配置了 DNS 服务器）键入“Web 服务 URL”字段。

![从 VPN 添加 API][api-management-setup-vpn-add-api]

## <a name="custom-dns"> </a>自定义 DNS 服务器设置
API 管理依赖于多个 Azure 服务。 在包含自定义 DNS 服务器的 VNET 中托管 API 管理时，API 管理需要能够解析这些 Azure 服务的主机名。 请根据[此指南](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)进行自定义 DNS 设置。 请参考下面的示意图和端口表。

## <a name="ports-required"> </a>API 管理所需的端口
> 如果其中的任一端口不可用，API 管理可能无法正常工作且不可访问。 在 VNET 中使用 API 管理时，最常见的配置错误是阻止了一个或多个此类端口。
> 
> 

在 VNET 中托管 API 管理服务实例时，将使用下表中的端口。

| 端口 | Direction | 传输协议 | 目的 | 源/目标 | 访问类型 |
| --- | --- | --- | --- | --- | --- |
| 80、443 |入站 |TCP |客户端与 API 管理的通信 |INTERNET/VIRTUAL_NETWORK |外部 |
| 3443 |入站 |TCP |管理终结点 |INTERNET/VIRTUAL_NETWORK |外部和内部 |
| 80、443 |出站 |TCP |与 Azure 存储和 Azure 服务总线的依赖关系 |VIRTUAL_NETWORK/INTERNET |外部和内部 |
| 1433 |出站 |TCP |与 Azure SQL 的依赖关系 |VIRTUAL_NETWORK/INTERNET |外部和内部 |
| 9350, 9351, 9352, 9353, 9354 |出站 |TCP |与服务总线的依赖关系 |VIRTUAL_NETWORK/INTERNET |外部和内部 |
| 5671 |出站 |AMQP |与事件中心策略日志记录的依赖项关系 |VIRTUAL_NETWORK/INTERNET |外部和内部 |
| 6381, 6382, 6383 |入站/出站 |UDP |与 Redis 缓存的依赖关系 |VIRTUAL_NETWORK/VIRTUAL_NETWORK |外部和内部 |
| 445 |出站 |TCP |与适用于 GIT 的 Azure 文件共享的依赖关系 |VIRTUAL_NETWORK/INTERNET |外部和内部 |

## <a name="limitations"> </a>限制
* 包含 API 管理实例的子网不能包含其他任何 Azure 资源类型。
* 子网和 API 管理服务必须在同一个订阅中。
* 无法在订阅之间移动包含 API 管理实例的子网。
* 使用内部虚拟网络时，只能从 [RFC 1918](https://tools.ietf.org/html/rfc1918) 所述的范围中使用一个内部 IP 地址，不能提供公共 IP 地址。
* 对于配置了内部虚拟网络的多区域 API 管理部署，用户需负责管理自己的负载均衡，因为 DNS 由他们拥有。

## <a name="related-content"></a>相关内容
* [使用 Azure 门户创建具有站点到站点 VPN 连接的虚拟网络][使用 Azure 门户创建具有站点到站点 VPN 连接的虚拟网络]
* [如何使用 API 检查器跟踪 Azure API 管理中的调用][如何使用 API 检查器跟踪 Azure API 管理中的调用]

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-type.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-private.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-public.png

[启用 VPN 连接]: #enable-vpn
[连接到 VPN 后面的 Web 服务]: #connect-vpn
[相关内容]: #related-content


[使用 Azure 门户创建具有站点到站点 VPN 连接的虚拟网络]: ../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[如何使用 API 检查器跟踪 Azure API 管理中的调用]: api-management-howto-api-inspector.md



<!--HONumber=Nov16_HO3-->


