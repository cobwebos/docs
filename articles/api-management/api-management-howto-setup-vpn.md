---
title: "如何在 Azure API 管理中设置 VPN 连接"
description: "了解如何在 Azure API 管理中设置 VPN 连接并通过它访问 Web 服务。"
services: api-management
documentationcenter: 
author: antonba
manager: erikre
editor: 
ms.assetid: 55a2a1e1-d07e-4111-9ce3-8837ed5040d6
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 30ec6f45da114b6c7bc081f8a2df46f037de61fd
ms.openlocfilehash: 98e88b1c69de8732f1a4ae54c8ecd0b11a547f35


---
# <a name="how-to-setup-vpn-connections-in-azure-api-management"></a>如何在 Azure API 管理中设置 VPN 连接
可以通过 API 管理的 VPN 支持功能将 API 管理网关连接到 Azure 虚拟网络（经典）。 API 管理客户可以通过此功能安全地连接到其后端的本地 Web 服务或无法通过公共 Internet 访问的 Web 服务。

> [!NOTE]
> Azure API 管理适用于经典 VNET。 有关如何创建经典 VNET 的信息，请参阅[使用 Azure 门户创建虚拟网络（经典）](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。 有关如何将经典 VNET 连接到 ARM VNET 的信息，请参阅[将经典 VNet 连接到新的 VNet](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)。
> 
> 

## <a name="enable-vpn"> </a>启用 VPN 连接
> VPN 连接仅适用于“高级”层和“开发人员”层。 若要切换到该连接，请在 [Azure 经典门户][Azure Classic Portal]中打开 API 管理服务，然后打开“规模”选项卡。 在“常规”部分选择“高级”层，然后单击“保存”。
> 
> 

若要启用 VPN 连接，请在 [Azure 经典门户][Azure Classic Portal]中打开 API 管理服务，然后切换到“配置”选项卡。 

在 VPN 部分，将“VPN 连接”切换到“开”。

![API 管理实例的“配置”选项卡][api-management-setup-vpn-configure]

此时会显示一个列表，包含预配了 API 管理服务的所有区域。

选择每个区域的 VPN 和子网。 VPN 列表根据 Azure 订阅中提供的虚拟网络进行填充，这些虚拟网络是在用户所配置的区域中设置的。

![选择 VPN][api-management-setup-vpn-select]

单击屏幕底部的“保存”。 正在更新时，将无法通过 Azure 经典门户对 API 管理服务执行其他操作。 服务网关将始终可用，运行时调用不会受影响。

请注意，每次启用或禁用 VPN 都会导致网关的 VIP 地址发生变化。

## <a name="connect-vpn"> </a>连接到 VPN 后面的 Web 服务
在 API 管理服务连接到 VPN 后，访问虚拟网络中的 Web 服务与访问公共服务无异。 在创建新的 API 或编辑现有的 API 时，直接将 Web 服务的本地地址或主机名（如果已为 Azure 虚拟网络配置为了 DNS 服务器）键入“Web 服务 URL”字段中。

![从 VPN 添加 API][api-management-setup-vpn-add-api]

## <a name="required-ports-for-api-management-vpn-support"></a>API 管理 VPN 支持需要的端口
在 VNET 中托管 API 管理服务实例时，会使用下表中的端口。 如果这些端口受阻，则服务可能无法正常运行。 在 VNET 中使用 API 管理时，最常见的配置错误是阻止了一个或多个此类端口。

| 端口 | Direction | 传输协议 | 目的 | 源/目标 |
| --- | --- | --- | --- | --- |
| 80、443 |入站 |TCP |客户端与 API 管理的通信 |INTERNET / VIRTUAL_NETWORK |
| 80,443 |出站 |TCP |Azure 存储和 Azure 服务总线上的 API 管理依赖项 |VIRTUAL_NETWORK / INTERNET |
| 1433 |出站 |TCP |SQL 上的 API 管理依赖项 |VIRTUAL_NETWORK / INTERNET |
| 9350, 9351, 9352, 9353, 9354 |出站 |TCP |服务总线上的 API 管理依赖项 |VIRTUAL_NETWORK / INTERNET |
| 5671 |出站 |AMQP |事件中心策略日志的 API 管理依赖项 |VIRTUAL_NETWORK / INTERNET |
| 6381, 6382, 6383 |入站/出站 |UDP |Redis 缓存上的 API 管理依赖项 |VIRTUAL_NETWORK / VIRTUAL_NETWORK |
| 445 |出站 |TCP |适用于 GIT 的 Azure 文件共享的 API 管理依赖项 |VIRTUAL_NETWORK / INTERNET |

## <a name="custom-dns"> </a>自定义 DNS 服务器安装程序
API 管理依赖于多个 Azure 服务。 在 VNET 中托管 API 管理服务实例时，如果在 VNET 中使用了自定义 DNS 服务器，则该实例需要能够解析这些 Azure 服务的主机名。 请根据[此](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)指南进行自定义 DNS 安装。  

## <a name="related-content"></a>相关内容
* [使用 Azure 经典门户创建具有站点到站点 VPN 连接的虚拟网络][Create a virtual network with a site-to-site VPN connection using the Azure Classic Portal]
* [如何使用 API 检查器跟踪 Azure API 管理中的调用][How to use the API Inspector to trace calls in Azure API Management]

[api-management-setup-vpn-configure]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-configure.png
[api-management-setup-vpn-select]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-add-api.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[Azure Classic Portal]: https://manage.windowsazure.com/

[Create a virtual network with a site-to-site VPN connection using the Azure Classic Portal]: ../vpn-gateway/vpn-gateway-site-to-site-create.md
[How to use the API Inspector to trace calls in Azure API Management]: api-management-howto-api-inspector.md



<!--HONumber=Dec16_HO3-->


