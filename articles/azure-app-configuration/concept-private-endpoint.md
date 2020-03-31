---
title: 使用专用终结点进行 Azure 应用配置
description: 使用专用终结点保护您的应用配置存储
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: f18672b9e3a368a833fc8cba279d748dfe3c2a9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366762"
---
# <a name="using-private-endpoints-for-azure-app-configuration"></a>使用专用终结点进行 Azure 应用配置

可以使用 Azure 应用配置的[专用终结点](../private-link/private-endpoint-overview.md)允许虚拟网络 （VNet） 上的客户端通过[专用链路](../private-link/private-link-overview.md)安全地访问数据。 专用终结点使用应用配置存储的 VNet 地址空间中的 IP 地址。 VNet 上的客户端和 App 配置商店之间的网络流量使用 Microsoft 骨干网络上的专用链接遍历 VNet，从而消除了对公共 Internet 的暴露。

在应用配置存储中使用专用终结点使您能够：
- 通过配置防火墙来阻止在公共终结点上与应用配置的所有连接，保护您的应用程序配置详细信息。
- 提高虚拟网络 （VNet） 的安全性，确保数据不会从 VNet 中逸出。
- 使用[VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)或[快速路由](../expressroute/expressroute-locations.md)（使用专用对等）连接到 VNet 的本地网络安全地连接到应用配置存储。

> [!NOTE]
> Azure 应用配置提供使用专用终结点作为公共预览。 使用公共预览版产品/服务，客户可以在产品/服务正式发布之前体验新功能。  公共预览功能和服务并非供生产使用。

## <a name="conceptual-overview"></a>概念概述

专用终结点是[虚拟网络](../virtual-network/virtual-networks-overview.md)（VNet） 中 Azure 服务的特殊网络接口。 为 App Config 存储创建专用终结点时，它将在 VNet 上的客户端和配置存储中的客户端之间提供安全连接。 从 VNet 的 IP 地址范围为专用终结点分配了 IP 地址。 专用终结点和配置存储之间的连接使用安全的专用链路。

VNet 中的应用程序可以使用**它们将使用的相同连接字符串和授权机制**通过专用终结点连接到配置存储。 专用终结点可用于应用配置存储支持的所有协议。

虽然应用配置不支持服务终结点，但可以在使用[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)的子网中创建专用终结点。 子网中的客户端可以使用专用终结点安全地连接到应用配置存储，同时使用服务终结点访问其他终结点。  

在 VNet 中为服务创建专用终结点时，将发送同意请求以向服务帐户所有者发送审批请求。 如果请求创建专用终结点的用户也是帐户的所有者，则此同意请求将自动获得批准。

服务帐户所有者可以通过`Private Endpoints`Azure[门户](https://portal.azure.com)中的配置存储选项卡管理同意请求和专用终结点。

### <a name="private-endpoints-for-app-configuration"></a>应用配置的专用终结点 

创建专用终结点时，必须指定它连接到的应用配置存储。 如果帐户中有多个应用配置实例，则需要为每个存储单独使用专用终结点。

### <a name="connecting-to-private-endpoints"></a>连接到专用终结点

Azure 依靠 DNS 解析通过专用链路将连接从 VNet 路由到配置存储。 您可以通过选择应用配置存储，然后选择 **"设置** > **访问密钥**"，在 Azure 门户中快速查找连接字符串。  

> [!IMPORTANT]
> 使用相同的连接字符串使用专用终结点连接到应用配置存储，就像用于公共终结点一样。 不要使用其`privatelink`子域 URL 连接到存储帐户。

## <a name="dns-changes-for-private-endpoints"></a>专用终结点的 DNS 更改

创建专用终结点时，配置存储的 DNS CNAME 资源记录将更新为子域中的别名，该别名的前缀`privatelink`为 。 Azure 还会创建[private DNS zone](../dns/private-dns-overview.md)与子域对应的`privatelink`专用 DNS 区域，其中具有专用终结点的 DNS A 资源记录。

从 VNet 外部解析终结点 URL 时，它将解析为存储的公共终结点。 从承载专用终结点的 VNet 内部解析时，终结点 URL 解析为专用终结点。

您可以使用 Azure 防火墙服务通过公共终结点控制 VNet 外部客户端的访问。

此方法允许对承载专用终结点的 VNet 上的客户端以及 VNet 外部的客户端**使用相同的连接字符串**访问存储。

如果您在网络上使用自定义 DNS 服务器，则客户端必须能够将服务终结点的完全限定域名 （FQDN） 解析为专用终结点 IP 地址。 配置 DNS 服务器以将专用链接子域委派为 VNet 的专用 DNS 区域，或使用专用终结点`AppConfigInstanceA.privatelink.azconfig.io`IP 地址配置 A 记录。

> [!TIP]
> 使用自定义或本地 DNS 服务器时，应配置 DNS 服务器以将子域中的`privatelink`存储名称解析为专用终结点 IP 地址。 为此，`privatelink`可以将子域委派到 VNet 的专用 DNS 区域，或者配置 DNS 服务器上的 DNS 区域并添加 DNS A 记录。

## <a name="pricing"></a>定价

启用专用终结点需要[标准层](https://azure.microsoft.com/pricing/details/app-configuration/)应用配置存储。  要了解专用链接定价详细信息，请参阅[Azure 专用链接定价](https://azure.microsoft.com/pricing/details/private-link)。

## <a name="next-steps"></a>后续步骤

详细了解如何为应用配置应用商店创建专用终结点，请参阅以下文章：

- [使用 Azure 门户中的专用链接中心创建专用终结点](../private-link/create-private-endpoint-portal.md)
- [使用 Azure CLI 创建专用终结点](../private-link/create-private-endpoint-cli.md)
- [使用 Azure PowerShell 创建专用终结点](../private-link/create-private-endpoint-powershell.md)

了解如何使用专用终结点配置 DNS 服务器：

- [Azure 虚拟网络中资源的名称解析](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [专用终结点的 DNS 配置](/azure/private-link/private-endpoint-overview#dns-configuration)
