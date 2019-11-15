---
title: 将专用终结点用于 Azure 存储 |Microsoft Docs
description: 用于从虚拟网络安全访问存储帐户的专用终结点的概述。
services: storage
author: santoshc
ms.service: storage
ms.topic: article
ms.date: 09/25/2019
ms.author: santoshc
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: fb1f8a1d1f8e1ebbaf3e0e9fe96e3c1bf0ba9ba6
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74078757"
---
# <a name="using-private-endpoints-for-azure-storage-preview"></a>使用 Azure 存储的专用终结点（预览）

你可以使用 Azure 存储帐户的[专用终结点](../../private-link/private-endpoint-overview.md)，以允许虚拟网络（VNet）上的客户端通过[专用链接](../../private-link/private-link-overview.md)安全地访问数据。 专用终结点使用来自你的存储帐户服务的 VNet 地址空间中的 IP 地址。 VNet 和存储帐户上的客户端之间的网络流量通过 VNet 和 Microsoft 主干网络上的专用链接进行遍历，从而消除了公共 internet 的泄露。

使用存储帐户的专用终结点可以：
- 通过将存储防火墙配置为阻止存储服务的公共终结点上的所有连接来保护存储帐户。
- 通过使你能够阻止渗透来自 VNet 的数据，提高虚拟网络（VNet）的安全性。
- 使用[VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)或[ExpressRoutes](../../expressroute/expressroute-locations.md)与专用对等互连连接到 VNet，从而安全地连接到存储帐户。

## <a name="conceptual-overview"></a>概念性概述
![Azure 存储的专用终结点概述](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

专用终结点是[虚拟网络](../../virtual-network/virtual-networks-overview.md)（VNet）中 Azure 服务的特殊网络接口。 当你为存储帐户创建专用终结点时，它会在 VNet 和你的存储中的客户端之间提供安全连接。 专用终结点是从 VNet 的 IP 地址范围分配的 IP 地址。 专用终结点与存储服务之间的连接使用安全的专用链接。

VNet 中的应用程序可以**使用相同的连接字符串和要使用的授权机制**，以无缝方式通过专用终结点连接到存储服务。 专用终结点可与存储帐户支持的所有协议（包括 REST 和 SMB）结合使用。

在 VNet 中创建存储服务的专用终结点时，会向存储帐户所有者发送同意请求。 如果请求创建专用终结点的用户也是存储帐户的所有者，则会自动批准此同意请求。

存储帐户所有者可以通过[Azure 门户](https://portal.azure.com)中存储帐户的 "*专用终结点*" 选项卡来管理同意请求和专用终结点。

> [!TIP]
> 如果要仅通过专用终结点限制对存储帐户的访问，请将存储防火墙配置为拒绝通过公共终结点进行的所有访问。

你可以通过将[存储防火墙配置](storage-network-security.md#change-the-default-network-access-rule)为拒绝默认情况下通过其公共终结点进行访问，从而保护存储帐户仅接受来自 VNet 的连接。 无需防火墙规则即可允许来自具有专用终结点的 VNet 的流量，因为存储防火墙只控制通过公共终结点进行的访问。 专用终结点而是依赖于授予对存储服务的子网访问权限的许可流。

### <a name="private-endpoints-for-storage-service"></a>用于存储服务的专用终结点

创建专用终结点时，必须指定存储帐户及其连接到的存储服务。 对于需要访问的存储帐户中的每个存储服务，都需要一个单独的专用终结点，即[blob](../blobs/storage-blobs-overview.md)、 [Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md)、[文件](../files/storage-files-introduction.md)、[队列](../queues/storage-queues-introduction.md)、[表](../tables/table-storage-overview.md)或[静态网站](../blobs/storage-blob-static-website.md)。

> [!TIP]
> 为存储服务的辅助实例创建单独的专用终结点，以在 GRS 帐户中获得更好的读取性能。

对于[读取访问异地冗余存储帐户](storage-redundancy-grs.md#read-access-geo-redundant-storage)的读取可用性，需要为该服务的主要实例和辅助实例使用单独的专用终结点。 无需为**故障转移**的辅助实例创建专用终结点。 故障转移后，专用终结点将自动连接到新的主实例。

#### <a name="resources"></a>资源

有关为你的存储帐户创建专用终结点的详细信息，请参阅以下文章：

- [从 Azure 门户中的存储帐户体验私下连接到存储帐户](../../private-link/create-private-endpoint-storage-portal.md)
- [使用 Azure 门户中的专用链接中心创建专用终结点](../../private-link/create-private-endpoint-portal.md)
- [使用 Azure CLI 创建专用终结点](../../private-link/create-private-endpoint-cli.md)
- [使用 Azure PowerShell 创建专用终结点](../../private-link/create-private-endpoint-powershell.md)

### <a name="dns-changes-for-private-endpoints"></a>专用终结点的 DNS 更改

即使使用专用终结点，VNet 中的客户端也应为存储帐户使用相同的连接字符串。

创建专用终结点时，我们会将该存储终结点的 DNS CNAME 资源记录更新为具有前缀 "*privatelink*" 的子域中的别名。 默认情况下，我们还会创建附加到 VNet 的[专用 DNS 区域](../../dns/private-dns-overview.md)。 此专用 DNS 区域对应于前缀为 "*privatelink*" 的子域，其中包含用于专用终结点的 DNS A 资源记录。

当你通过专用终结点在 VNet 外部解析存储终结点 URL 时，它将解析为存储服务的公共终结点。 当从承载专用终结点的 VNet 解析时，存储终结点 URL 解析为专用终结点的 IP 地址。

对于上面所示的示例，存储帐户 "StorageAccountA" 的 DNS 资源记录在托管专用终结点的 VNet 之外进行解析时将为：

| 名称                                                  | 类型  | 值                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<存储服务公共终结点\>                   |
| \<存储服务公共终结点\>                   | A     | \<存储服务公共 IP 地址\>                 |

如前所述，你可以使用存储防火墙拒绝通过公共终结点进行的所有访问。

托管专用终结点的 VNet 中的客户端解析后，StorageAccountA 的 DNS 资源记录将为：

| 名称                                                  | 类型  | 值                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | 10.1.1.5                                              |

此方法可让你使用来自托管专用终结点的 VNet 中**的相同连接字符串**以及 vnet 外部的客户端来访问存储帐户。 可以使用存储防火墙拒绝对 VNet 之外的所有客户端的访问。

> [!IMPORTANT]
> 使用相同的连接字符串通过专用终结点连接到存储帐户，否则你将使用此连接字符串。 请不要使用 "*privatelink*" 子域 URL 连接到存储帐户。

> [!TIP]
> 使用自定义或本地 DNS 服务器时，应在与存储服务的 "privatelink" 子域相对应的 DNS 区域中为专用终结点配置 DNS 资源记录。

建议用于存储服务的专用终结点的 DNS 区域名称为：

| 存储服务        | 区域名称                            |
| :--------------------- | :----------------------------------- |
| Blob 服务           | `privatelink.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `privatelink.dfs.core.windows.net`   |
| 文件服务           | `privatelink.file.core.windows.net`  |
| 队列服务          | `privatelink.queue.core.windows.net` |
| 表服务          | `privatelink.table.core.windows.net` |
| 静态网站        | `privatelink.web.core.windows.net`   |

## <a name="pricing"></a>定价

有关定价详细信息，请参阅 [Azure 专用链接定价](https://azure.microsoft.com/pricing/details/private-link)。

## <a name="known-issues"></a>已知问题

### <a name="copy-blob-support"></a>复制 Blob 支持

在预览期间，我们不支持在源存储帐户受防火墙保护时，发布到通过专用终结点访问的存储帐户的 "[复制 Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) " 命令。

### <a name="subnets-with-service-endpoints"></a>包含服务终结点的子网
目前，无法在具有服务终结点的子网中创建专用终结点。 作为一种解决方法，你可以在服务终结点和专用终结点的同一 VNet 中创建单独的子网。

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>具有专用终结点的 Vnet 中客户端的存储访问约束

Vnet 中具有现有专用终结点的客户端在访问具有专用终结点的其他存储帐户时面临约束。 例如，假设 VNet N1 具有用于存储帐户 A1 的专用终结点，例如，blob 服务。 如果存储帐户 A2 在 VNet N2 中有一个用于 blob 服务的专用终结点，则 VNet N1 中的客户端还必须使用专用终结点访问帐户 A2 的 blob 服务。 如果存储帐户 A2 没有用于 blob 服务的任何专用终结点，则 VNet N1 中的客户端无需专用终结点即可访问其 blob 服务。

此约束是在帐户 A2 创建专用终结点时所做的 DNS 更改的结果。

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>具有专用终结点的子网的网络安全组规则

目前，无法为具有专用终结点的子网配置[网络安全组](../../virtual-network/security-overview.md)（NSG）规则。 此问题的一种有限的解决方法是在源子网中实现专用终结点的访问规则，不过，这种方法可能需要更高的管理开销。
