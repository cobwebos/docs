---
title: 使用专用终结点
titleSuffix: Azure Storage
description: 用于从虚拟网络安全访问存储帐户的专用终结点概述。
services: storage
author: santoshc
ms.service: storage
ms.topic: article
ms.date: 03/12/2020
ms.author: santoshc
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: c51f2db698f30368c9d4090d3d571fa0c131178a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299050"
---
# <a name="use-private-endpoints-for-azure-storage"></a>使用专用终结点进行 Azure 存储

可以使用 Azure 存储帐户的[专用终结点](../../private-link/private-endpoint-overview.md)，允许虚拟网络 （VNet） 上的客户端通过[专用链路](../../private-link/private-link-overview.md)安全地访问数据。 专用终结点使用来自 VNet 地址空间的 IP 地址来存储帐户服务。 VNet 上的客户端和存储帐户之间的网络流量通过 VNet 和 Microsoft 骨干网络上的专用链路遍历，从而消除了公共 Internet 的暴露。

为存储帐户使用专用终结点使您能够：

- 通过配置存储防火墙来阻止存储服务的公共终结点上的所有连接，从而保护存储帐户的安全。
- 通过阻止从 VNet 中挖掘数据，提高虚拟网络 （VNet） 的安全性。
- 使用[VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)或[快速路由](../../expressroute/expressroute-locations.md)（使用专用对等）安全地连接到本地网络的存储帐户。

## <a name="conceptual-overview"></a>概念概述

![Azure 存储专用终结点概述](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

专用终结点是[虚拟网络](../../virtual-network/virtual-networks-overview.md)（VNet） 中 Azure 服务的特殊网络接口。 为存储帐户创建专用终结点时，它将在 VNet 上的客户端和存储中的客户端之间提供安全连接。 从 VNet 的 IP 地址范围为专用终结点分配了 IP 地址。 专用终结点和存储服务之间的连接使用安全的专用链路。

VNet 中的应用程序可以使用**它们使用的相同连接字符串和授权机制**，通过专用终结点无缝连接到存储服务。 专用终结点可用于存储帐户支持的所有协议，包括 REST 和 SMB。

私有终结点可以在使用[服务终结点](../../virtual-network/virtual-network-service-endpoints-overview.md)的子网中创建。 因此，子网中的客户端可以使用专用终结点连接到一个存储帐户，同时使用服务终结点访问其他存储帐户。

在 VNet 中创建用于存储服务的专用终结点时，会将一个申请批准的许可请求发送到存储帐户所有者。 如果请求创建专用终结点的用户也是存储帐户的所有者，则此同意请求将自动获得批准。

存储帐户所有者可以通过[Azure 门户](https://portal.azure.com)中的存储帐户的"*专用终结点*"选项卡管理同意请求和专用终结点。

> [!TIP]
> 如果只想通过专用终结点限制对存储帐户的访问，请将存储防火墙配置为拒绝或控制通过公共终结点的访问。

通过[配置存储防火墙](storage-network-security.md#change-the-default-network-access-rule)以默认拒绝通过其公共终结点进行访问，可以保护存储帐户仅接受来自 VNet 的连接。 您不需要防火墙规则来允许来自具有专用终结点的 VNet 的流量，因为存储防火墙仅控制通过公共终结点的访问。 相反，专用终结点依赖于同意流来授予对存储服务的子网访问权限。

### <a name="private-endpoints-for-azure-storage"></a>用于 Azure 存储的专用终结点

创建专用终结点时，必须指定存储帐户及其连接到的存储服务。 您需要访问的存储帐户中的每个存储服务都需要单独的专用终结点，即[Blob、](../blobs/storage-blobs-overview.md)[数据存储 Gen2、](../blobs/data-lake-storage-introduction.md)[文件](../files/storage-files-introduction.md)、[队列](../queues/storage-queues-introduction.md)、[表](../tables/table-storage-overview.md)或[静态网站](../blobs/storage-blob-static-website.md)。

> [!TIP]
> 为存储服务的辅助实例创建单独的专用终结点，以在 RA-GRS 帐户上提高读取性能。

对于已配置为异地冗余存储的存储帐户的辅助区域的读取访问，需要为服务的主实例和辅助实例单独使用专用终结点。 不需要为故障**转移**为辅助实例创建专用终结点。 故障转移后，专用终结点将自动连接到新的主实例。 有关存储冗余选项的详细信息，请参阅[Azure 存储冗余](storage-redundancy.md)。

有关为存储帐户创建专用终结点的更多详细信息，请参阅以下文章：

- [从 Azure 门户中的存储帐户体验私下连接到存储帐户](../../private-link/create-private-endpoint-storage-portal.md)
- [使用 Azure 门户中的专用链接中心创建专用终结点](../../private-link/create-private-endpoint-portal.md)
- [使用 Azure CLI 创建专用终结点](../../private-link/create-private-endpoint-cli.md)
- [使用 Azure PowerShell 创建专用终结点](../../private-link/create-private-endpoint-powershell.md)

### <a name="connecting-to-private-endpoints"></a>连接到专用终结点

使用专用终结点的 VNet 上的客户端应对存储帐户使用相同的连接字符串，作为连接到公共终结点的客户端。 我们依靠 DNS 解析通过专用链路自动将连接从 VNet 路由到存储帐户。

> [!IMPORTANT]
> 使用相同的连接字符串使用专用终结点连接到存储帐户，否则可以使用。 请不要使用其"*专用链接*"子域 URL 连接到存储帐户。

默认情况下，我们创建一个连接到 VNet 的[专用 DNS 区域](../../dns/private-dns-overview.md)，并包含专用终结点的必要更新。 但是，如果您使用的是自己的 DNS 服务器，则可能需要对 DNS 配置进行其他更改。 下面有关[DNS 更改](#dns-changes-for-private-endpoints)的部分介绍了专用终结点所需的更新。

## <a name="dns-changes-for-private-endpoints"></a>专用终结点的 DNS 更改

创建专用终结点时，存储帐户的 DNS CNAME 资源记录将更新为子域中的别名，其前缀为"*专用链接*"。 默认情况下，我们还创建一个[专用 DNS 区域](../../dns/private-dns-overview.md)，对应于"*专用链接*"子域，并带有专用终结点的 DNS A 资源记录。

当您使用专用终结点从 VNet 外部解析存储终结点 URL 时，它将解析为存储服务的公共终结点。 从托管专用终结点的 VNet 解析时，存储终结点 URL 解析为专用终结点的 IP 地址。

对于上面所示的示例，存储帐户"StorageAccountA"的 DNS 资源记录（从托管专用终结点的 VNet 外部解析）将为：

| “属性”                                                  | 类型  | “值”                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<存储服务公共终结点\>                   |
| \<存储服务公共终结点\>                   | A     | \<存储服务公共 IP 地址\>                 |

如前所述，您可以使用存储防火墙拒绝或控制 VNet 外部客户端的访问。这些访问通过公共终结点。

存储帐户A 的 DNS 资源记录，当由托管专用终结点的 VNet 中的客户端解析时，将为：

| “属性”                                                  | 类型  | “值”                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | 10.1.1.5                                              |

此方法允许对承载专用终结点的 VNet 上的客户端以及 VNet 外部的客户端**使用相同的连接字符串**访问存储帐户。

如果您在网络上使用自定义 DNS 服务器，则客户端必须能够将存储帐户终结点的 FQDN 解析为专用终结点 IP 地址。 应配置 DNS 服务器以将专用链接子域委派为 VNet 的专用 DNS 区域，或使用专用终结点 IP 地址配置 *"StorageAccountA.privatelink.blob.core.windows.net"* 的 A 记录。

> [!TIP]
> 使用自定义或本地 DNS 服务器时，应配置 DNS 服务器以解析"专用链接"子域中到专用终结点 IP 地址的存储帐户名称。 为此，可以将"专用链接"子域委派到 VNet 的专用 DNS 区域，或者配置 DNS 服务器上的 DNS 区域并添加 DNS A 记录。

存储服务的专用终结点的推荐 DNS 区域名称包括：

| 存储服务        | 区域名称                            |
| :--------------------- | :----------------------------------- |
| Blob 服务           | `privatelink.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `privatelink.dfs.core.windows.net`   |
| 文件服务           | `privatelink.file.core.windows.net`  |
| 队列服务          | `privatelink.queue.core.windows.net` |
| 表服务          | `privatelink.table.core.windows.net` |
| 静态网站        | `privatelink.web.core.windows.net`   |

有关配置自己的 DNS 服务器以支持专用终结点的详细信息，请参阅以下文章：

- [Azure 虚拟网络中资源的名称解析](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [专用终结点的 DNS 配置](/azure/private-link/private-endpoint-overview#dns-configuration)

## <a name="pricing"></a>定价

有关定价详细信息，请参阅 [Azure 专用链接定价](https://azure.microsoft.com/pricing/details/private-link)。

## <a name="known-issues"></a>已知问题

请记住有关 Azure 存储专用终结点的以下已知问题。

### <a name="copy-blob-support"></a>复制 Blob 支持

如果存储帐户受防火墙保护，并且该帐户通过专用终结点访问，则该帐户不能用作[复制 Blob](/rest/api/storageservices/copy-blob)操作的源。

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>具有专用终结点的 VNet 中客户端的存储访问约束

访问具有专用终结点的其他存储帐户时，VNet 中具有现有专用终结点的客户端将面临约束。 例如，假设 VNet N1 具有用于 Blob 存储的存储帐户 A1 的专用终结点。 如果存储帐户 A2 在用于 Blob 存储的 VNet N2 中具有专用终结点，则 VNet N1 中的客户端还必须使用专用终结点访问帐户 A2 中的 Blob 存储。 如果存储帐户 A2 没有任何用于 Blob 存储的专用终结点，则 VNet N1 中的客户端可以访问该帐户中的 Blob 存储，而无需专用终结点。

此约束是帐户 A2 创建专用终结点时进行的 DNS 更改的结果。

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>专用终结点所在子网的网络安全组规则

目前，您无法为专用终结点配置[网络安全组](../../virtual-network/security-overview.md)（NSG） 规则和用户定义的路由。 应用于托管专用终结点的子网的 NSG 规则将应用于专用终结点。 此问题的有限解决方法是实现源子网上专用终结点的访问规则，尽管此方法可能需要更高的管理开销。

## <a name="next-steps"></a>后续步骤

- [配置 Azure 存储防火墙和虚拟网络](storage-network-security.md)
- [适用于 Blob 存储的安全建议](../blobs/security-recommendations.md)
