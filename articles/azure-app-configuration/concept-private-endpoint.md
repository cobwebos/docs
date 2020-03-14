---
title: 为 Azure 应用配置使用专用终结点
description: 使用专用终结点保护应用配置存储
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: f18672b9e3a368a833fc8cba279d748dfe3c2a9e
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79366762"
---
# <a name="using-private-endpoints-for-azure-app-configuration"></a>为 Azure 应用配置使用专用终结点

你可以使用 Azure 应用配置的[专用终结点](../private-link/private-endpoint-overview.md)，以允许虚拟网络（VNet）上的客户端通过[专用链接](../private-link/private-link-overview.md)安全地访问数据。 专用终结点使用应用配置存储的 VNet 地址空间中的 IP 地址。 VNet 上的客户端与应用程序配置存储之间的网络流量使用 Microsoft 主干网络上的专用链接遍历 VNet，从而消除了对公共 internet 的公开。

使用应用配置存储的专用终结点可以：
- 通过将防火墙配置为阻止所有到公共终结点上的应用配置的连接来保护应用程序的配置详细信息。
- 提高虚拟网络（VNet）的安全性，确保数据不会从 VNet 中转义。
- 使用[VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)或[ExpressRoutes](../expressroute/expressroute-locations.md)与专用对等互连连接到 VNet，从本地网络安全连接到应用配置存储。

> [!NOTE]
> Azure 应用配置提供专用终结点作为公共预览。 使用公共预览版产品/服务，客户可以在产品/服务正式发布之前体验新功能。  公共预览功能和服务并非供生产使用。

## <a name="conceptual-overview"></a>概念概述

专用终结点是[虚拟网络](../virtual-network/virtual-networks-overview.md)（VNet）中 Azure 服务的特殊网络接口。 为应用配置存储创建专用终结点时，它会在 VNet 中的客户端和配置存储之间提供安全连接。 专用终结点是从 VNet 的 IP 地址范围分配的 IP 地址。 专用终结点和配置存储之间的连接使用安全的专用链接。

VNet 中的应用程序可以**使用相同的连接字符串和它们将使用的授权机制**，连接到专用终结点上的配置存储。 专用终结点可以与应用配置存储支持的所有协议一起使用。

虽然应用配置不支持服务终结点，但可以在使用[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)的子网中创建专用终结点。 使用服务终结点访问其他用户时，子网中的客户端可以使用专用终结点安全地连接到应用配置存储。  

在 VNet 中创建服务的专用终结点时，会将同意请求发送到服务帐户所有者进行审批。 如果请求创建专用终结点的用户也是该帐户的所有者，则会自动批准此同意请求。

服务帐户所有者可以通过[Azure 门户](https://portal.azure.com)中配置存储的 "`Private Endpoints`" 选项卡来管理同意请求和专用终结点。

### <a name="private-endpoints-for-app-configuration"></a>应用配置的专用终结点 

创建专用终结点时，必须指定它所连接的应用配置存储。 如果帐户中有多个应用配置实例，则每个商店都需要一个单独的专用终结点。

### <a name="connecting-to-private-endpoints"></a>连接到专用终结点

Azure 依赖于 DNS 解析，通过专用链路将连接从 VNet 路由到配置存储。 你可以通过选择应用配置存储，然后选择 "**设置**" > **访问密钥**"，在 Azure 门户中快速查找连接字符串。  

> [!IMPORTANT]
> 使用与用于公共终结点相同的连接字符串连接到应用配置存储。 请勿使用其 `privatelink` 的子域 URL 连接到存储帐户。

## <a name="dns-changes-for-private-endpoints"></a>专用终结点的 DNS 更改

创建专用终结点时，会将配置存储区的 DNS CNAME 资源记录更新为带有前缀 `privatelink`的子域中的别名。 Azure 还会创建与 `privatelink` 子域相对应的[专用 dns 区域](../dns/private-dns-overview.md)，其中 dns a 用于专用终结点的资源记录。

当你从 VNet 外部解析终结点 URL 时，它将解析为存储的公共终结点。 当从承载专用终结点的 VNet 中解析时，终结点 URL 解析为专用终结点。

可以使用 Azure 防火墙服务，通过公共终结点控制对 VNet 外部客户端的访问。

利用此方法，可以对承载专用终结点的 VNet 中的客户端和 VNet 外部的客户端**使用相同的连接字符串**来访问应用商店。

如果在网络上使用自定义 DNS 服务器，则客户端必须能够将服务终结点的完全限定的域名（FQDN）解析为专用终结点 IP 地址。 将你的 DNS 服务器配置为将专用链接子域委托给 VNet 的专用 DNS 区域，或为具有专用终结点 IP 地址的 `AppConfigInstanceA.privatelink.azconfig.io` 配置 A 记录。

> [!TIP]
> 使用自定义或本地 DNS 服务器时，应将 DNS 服务器配置为将 `privatelink` 子域中的存储名称解析为专用终结点 IP 地址。 为此，可以将 `privatelink` 子域委托给 VNet 的专用 DNS 区域，或在 DNS 服务器上配置 DNS 区域并添加 DNS A 记录。

## <a name="pricing"></a>定价

启用专用终结点需要[标准层](https://azure.microsoft.com/pricing/details/app-configuration/)应用配置存储。  若要了解有关私有链接定价的详细信息，请参阅[Azure 专用链接定价](https://azure.microsoft.com/pricing/details/private-link)。

## <a name="next-steps"></a>后续步骤

了解有关为你的应用配置存储创建专用终结点的详细信息，请参阅以下文章：

- [使用 Azure 门户中的专用链接中心创建专用终结点](../private-link/create-private-endpoint-portal.md)
- [使用 Azure CLI 创建专用终结点](../private-link/create-private-endpoint-cli.md)
- [使用 Azure PowerShell 创建专用终结点](../private-link/create-private-endpoint-powershell.md)

了解如何配置具有专用终结点的 DNS 服务器：

- [Azure 虚拟网络中资源的名称解析](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [专用终结点的 DNS 配置](/azure/private-link/private-endpoint-overview#dns-configuration)
