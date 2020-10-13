---
title: Azure 文件同步的网络注意事项 | Microsoft Docs
description: 了解如何配置网络以使用 Azure 文件同步在本地缓存文件。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: b291bd45b4003dd2241f40c810ed9d78af9f8bc9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91267782"
---
# <a name="azure-file-sync-networking-considerations"></a>Azure 文件同步的网络注意事项
可以通过两种方式连接到 Azure 文件共享：

- 直接通过 SMB 或 FileREST 协议访问共享。 要删除尽可能多的本地服务器时，主要使用此访问模式。
- 使用 Azure 文件同步在本地服务器上（或在 Azure VM 上）创建 Azure 文件共享的缓存，并使用适合自己用例的所选协议（SMB、NFS、FTPS 等）从本地服务器访问文件共享的数据。 此访问模式非常方便，因为它结合了本地性能以及云缩放和无服务器可附加服务（例如，Azure 备份）的优势。

本文重点介绍如何在用例要求使用 Azure 文件同步在本地缓存文件而不是直接通过 SMB 装载 Azure 文件共享时配置网络。 有关 Azure 文件存储部署的网络注意事项的详细信息，请参阅 [Azure 文件存储的网络注意事项](storage-files-networking-overview.md)。

Azure 文件同步的网络配置跨两个不同的 Azure 对象：存储同步服务和 Azure 存储帐户。 存储帐户是代表共享存储池的管理结构，你可以在其中部署多个文件共享以及其他存储资源（例如，Blob 容器或队列）。 存储同步服务是代表已注册服务器（与 Azure 文件同步建立了信任关系的 Windows 文件服务器以及定义同步关系拓扑的同步组）的管理结构。 

## <a name="connecting-windows-file-server-to-azure-with-azure-file-sync"></a>通过 Azure 文件同步将 Windows 文件服务器连接到 Azure 
若要针对本地 Windows 文件服务器设置并使用 Azure 文件存储和 Azure 文件同步，除了基本 Internet 连接之外，不需要与 Azure 建立任何特殊的网络连接。 若要部署 Azure 文件同步，请在要与 Azure 同步的 Windows 文件服务器上安装 Azure 文件同步代理。 Azure 文件同步代理可通过两种渠道实现与 Azure 文件共享的同步：

- FileREST 协议，它是一种基于 HTTPS 的协议，用于访问 Azure 文件共享。 由于 FileREST 协议使用标准 HTTPS 进行数据传输，因此，只有端口 443 必须可以出站访问。 Azure 文件同步不使用 SMB 协议将数据从本地 Windows 服务器传输到 Azure 文件共享。
- Azure 文件同步同步协议是一种基于 HTTPS 的协议，用于在环境中的终结点之间交换同步知识，即环境中的文件和文件夹的版本信息。 此协议还用于交换有关环境中的文件和文件夹的元数据，例如时间戳和访问控制列表 (ACL)。 

由于 Azure 文件存储在 Azure 文件共享上提供直接 SMB 协议访问，因此客户通常想要知道是否需要配置特殊网络以使用 SMB 装载 Azure 文件共享，以便 Azure 文件同步代理进行访问。 由于无法快速检测到直接对 Azure 文件共享所做的更改（根据 Azure 文件共享中项目的大小和数量，更改可能超过 24 小时也不会被发现），因此这不仅非必需的，而且也不建议这样做，但管理员方案除外。 如果希望直接使用 Azure 文件共享，即，不使用 Azure 文件同步在本地缓存，则可以通过阅读 [Azure 文件存储网络概述](storage-files-networking-overview.md)了解有关直接访问的网络注意事项的详细信息。

尽管 Azure 文件同步不需要任何特殊的网络配置，但某些客户可能想要配置高级网络设置，以支持以下方案：

- 与组织的代理服务器配置进行互操作。
- 对 Azure 文件存储和 Azure 文件同步服务开放组织的本地防火墙。
- 通过 ExpressRoute 或 VPN 连接为 Azure 文件存储和 Azure 文件同步建立隧道。

### <a name="configuring-proxy-servers"></a>配置代理服务器
许多组织使用代理服务器作为其本地网络中的资源与网络外部资源（例如 Azure 中的资源）之间的媒介。 代理服务器适用于许多应用，如网络隔离和安全、监视和日志记录。 Azure 文件同步可以与代理服务器完全互操作，但必须使用 Azure 文件同步为环境手动配置代理终结点设置。必须使用 Azure 文件同步服务器 cmdlet 通过 PowerShell 完成此操作。 

有关如何使用代理服务器配置 Azure 文件同步的详细信息，请参阅[使用代理服务器配置 Azure 文件同步](storage-sync-files-firewall-and-proxy.md)。

### <a name="configuring-firewalls-and-service-tags"></a>配置防火墙和服务标记
出于安全目的，你可能会将文件服务器与大多数 Internet 位置隔离。 若要在环境中使用 Azure 文件同步，需要调整防火墙以对选定的 Azure 服务开放。 为此，可以通过一种称为[服务标记](../../virtual-network/service-tags-overview.md)的机制来检索所需服务的 IP 地址范围。

Azure 文件同步需要以下服务的 IP 地址范围，由其服务标记标识：

| 服务 | 说明 | 服务标记 |
|---------|-------------|-------------|
| Azure 文件同步 | Azure 文件同步服务由存储同步服务对象表示，负责在 Azure 文件共享与 Windows 文件服务器之间同步数据的核心活动。 | `StorageSyncService` |
| Azure 文件 | 通过 Azure 文件同步同步的所有数据都存储在 Azure 文件共享中。 Windows 文件服务器上已更改的文件将复制到 Azure 文件共享，并且在本地文件服务器上分层的文件会在用户请求时无缝下载。 | `Storage` |
| Azure 资源管理器 | Azure 资源管理器是 Azure 的管理接口。 所有管理调用（包括 Azure 文件同步服务器注册和正在进行的同步服务器任务）都通过 Azure 资源管理器进行。 | `AzureResourceManager` |
| Azure Active Directory | Azure Active Directory 简称 Azure AD，包含对存储同步服务授权服务器注册所需的用户主体，以及授权 Azure 文件同步访问云资源所需的服务主体。 | `AzureActiveDirectory` |

如果在 Azure 中使用 Azure 文件同步，即使是不同的区域，也可以直接在网络安全组中使用服务标记的名称，以允许流量发送到该服务。 若要了解有关如何执行此操作的详细信息，请参阅[网络安全组](../../virtual-network/security-overview.md)。 

如果是在本地使用 Azure 文件同步，则可以使用服务标记 API 为防火墙的允许列表获取特定的 IP 地址范围。 可以通过两种方法获取此信息：

- 支持服务标记的所有 Azure 服务的最新 IP 地址范围列表每周都会以 JSON 文档的形式在 Microsoft 下载中心上发布。 每个 Azure 云都有自己的 JSON 文档，其中包含与该云相关的 IP 地址范围：
    - [Azure 公用](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure US Government](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure 中国：](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure 德国](https://www.microsoft.com/download/details.aspx?id=57064)
- 服务标记发现 API（预览版）允许以编程方式检索最新服务标记列表。 在预览版中，服务标记发现 API 返回的信息可能早于从 Microsoft 下载中心发布的 JSON 文档返回的信息。 可以根据自动化首选项使用 API 接口：
    - [REST API](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Azure CLI](https://docs.microsoft.com/cli/azure/network#az-network-list-service-tags)

若要详细了解如何使用服务标记 API 来检索服务的地址，请参阅 [Azure 文件同步 IP 地址允许列表](storage-sync-files-firewall-and-proxy.md#allow-list-for-azure-file-sync-ip-addresses)。

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>通过虚拟专用网络或 ExpressRoute 以隧道方式传输流量
某些组织需要通过网络隧道（如虚拟专用网 (VPN) 或 ExpressRoute）与 Azure 进行通信来实现额外的安全层，或确保与 Azure 的通信遵循确定性路由。 

在本地网络与 Azure 之间建立网络隧道时，会将本地网络对等互连到 Azure 中的一个或多个虚拟网络。 [虚拟网络](../../virtual-network/virtual-networks-overview.md) (VNet) 类似于在本地运行的传统网络。 与 Azure 存储帐户或 Azure VM 一样，VNet 是在资源组中部署的 Azure 资源。 

Azure 文件存储和文件同步支持以下机制，以在本地服务器与 Azure 之间以隧道方式传输流量：

- [Azure VPN 网关](../../vpn-gateway/vpn-gateway-about-vpngateways.md)：VPN 网关是特定类型的虚拟网关，用于通过 Internet 在 Azure 虚拟网络和备用位置（例如，本地）之间发送加密的流量。 Azure VPN 网关是一种 Azure 资源，可以与存储帐户或其他 Azure 资源一起部署在资源组中。 由于 Azure 文件同步将用于本地 Windows 文件服务器，因此通常会使用[站点到站点 (S2S) VPN](../../vpn-gateway/design.md#s2smulti)，不过，从技术上讲，可以使用[点到站点 (P2S) VPN](../../vpn-gateway/point-to-site-about.md)。 

    站点到站点 (S2S) VPN 连接可连接 Azure 虚拟网络和组织的本地网络。 通过 S2S VPN 连接，你可以为组织的网络上托管的 VPN 服务器或设备一次性配置 VPN 连接，而不是为需要访问 Azure 文件共享的每个客户端设备都进行一次配置。 若要简化 S2S VPN 连接的部署，请参阅[配置用于 Azure 文件存储的站点到站点 (S2S) VPN](storage-files-configure-s2s-vpn.md)。

- [ExpressRoute](../../expressroute/expressroute-introduction.md)，使你可以在 Azure 与不经过 Internet 的本地网络之间创建定义的路由。 因为 ExpressRoute 在本地数据中心和 Azure 之间提供了专用路径，所以当存在网络性能方面的顾虑时，ExpressRoute 可能会很有用。 组织的策略或法规要求使用确定的路径访问云中的资源时，ExpressRoute 也是一个不错的选择。

### <a name="private-endpoints"></a>专用终结点
除了 Azure 文件存储和文件同步通过存储帐户和存储同步服务提供的默认公共终结点，Azure 文件存储和文件同步还提供了让每个资源有一个或多个专用终结点的选项。 为 Azure 资源创建专用终结点时，该资源将从虚拟网络地址空间中获取一个专用 IP 地址，这非常类似于本地 Windows 文件服务器具有本地网络专用地址空间中的 IP 地址。 

> [!Important]  
> 为了使用存储同步服务资源上的专用终结点，必须使用 Azure 文件同步代理 10.1 或更高版本。 10.1 之前的代理版本不支持存储同步服务上的专用终结点。 所有以前的代理版本都支持存储帐户资源上的专用终结点。

单个专用终结点与特定的 Azure 虚拟网络子网相关联。 存储帐户和存储同步服务可以在多个虚拟网络中具有专用终结点。

使用专用终结点可以：
- 结合专用对等互连使用 VPN 或 ExpressRoute 连接，从本地网络安全连接到 Azure 资源。
- 通过禁用 Azure 文件存储和文件同步的公共终结点来保护 Azure 资源。默认情况下，创建专用终结点不会阻止连接到公共终结点。
- 阻止数据从虚拟网络（和对等互连边界）渗出，从而提高虚拟网络的安全性。

若要创建专用终结点，请参阅[配置 Azure 文件同步的专用终结点](storage-sync-files-networking-endpoints.md)。

### <a name="private-endpoints-and-dns"></a>专用终结点和 DNS
创建专用终结点时，默认还会创建（或更新现有的）对应于 `privatelink` 子域的专用 DNS 区域。 对于公有云区域，这些 DNS 区域是 `privatelink.file.core.windows.net`（Azure 文件存储）和 `privatelink.afs.azure.net`（Azure 文件同步）。

> [!Note]  
> 本文将使用 Azure 公共区域的存储帐户 DNS 后缀 `core.windows.net`。 此段注释也适用于 Azure 主权云（例如 Azure 美国政府云和 Azure 中国云）- 只需根据环境替换相应的后缀即可。 

为存储帐户和存储同步服务创建专用终结点时，我们会在各自的专用 DNS 区域中为其创建 A 记录。 我们还会更新公用 DNS 条目，以便对于相关 privatelink 名称，常规完全限定的域名是 CNAME。 当请求者位于虚拟网络内部时，这将允许完全限定的域名指向专用终结点 IP 地址，而在请求者位于虚拟网络外部时指向公共终结点 IP 地址。 

对于 Azure 文件存储，每个专用终结点都有一个完全限定的域名，遵循 `storageaccount.privatelink.file.core.windows.net` 模式，映射到专用终结点的一个专用 IP 地址。 对于 Azure 文件同步，每个专用终结点都有四个完全限定的域名，分别用于 Azure 文件同步公开的四个不同的终结点：管理、同步（主要）、同步（辅助）和监视。 这些终结点的完全限定的域名通常将遵循存储同步服务的名称，除非该名称包含非 ASCII 字符。 例如，如果存储同步服务名称在美国西部 2 区域中为 `mysyncservice`，则等效的终结点将是 `mysyncservicemanagement.westus2.afs.azure.net`、`mysyncservicesyncp.westus2.afs.azure.net`、`mysyncservicesyncs.westus2.afs.azure.net` 和 `mysyncservicemonitoring.westus2.afs.azure.net`。 存储同步服务的每个专用终结点将包含 4 个不同的 IP 地址。 

由于 Azure 专用 DNS 区域将连接到包含专用终结点的虚拟网络，因此，可以通过在 Azure VM 上的 PowerShell 中调用 `Resolve-DnsName` cmdlet（或者在 Windows 和 Linux 中运行 `nslookup`）来观察 DNS 配置：

```powershell
Resolve-DnsName -Name "storageaccount.file.core.windows.net"
```

对于本示例，存储帐户 `storageaccount.file.core.windows.net` 解析为专用终结点的专用 IP 地址（正好是 `192.168.0.4`）。

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4


Name                   : privatelink.file.core.windows.net
QueryType              : SOA
TTL                    : 269
Section                : Authority
NameAdministrator      : azureprivatedns-host.microsoft.com
SerialNumber           : 1
TimeToZoneRefresh      : 3600
TimeToZoneFailureRetry : 300
TimeToExpiration       : 2419200
DefaultTTL             : 300
```

如果从本地运行同一命令，则会看到同一存储帐户名称解析为存储帐户的公共 IP 地址；`storageaccount.file.core.windows.net` 是 `storageaccount.privatelink.file.core.windows.net` 的 CNAME 记录，后者又是托管存储帐户的 Azure 存储群集的 CNAME 记录：

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
net
storageaccount.privatelink.file.c CNAME  60    Answer     file.par20prdstr01a.store.core.windows.net
ore.windows.net

Name       : file.par20prdstr01a.store.core.windows.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 52.239.194.40
```

这反映了 Azure 文件存储和 Azure 文件同步可以同时公开其公共终结点以及每个资源一个或多个专用终结点这一事实。 为了确保资源的完全限定的域名解析为专用终结点的专用 IP 地址，必须更改本地 DNS 服务器上的配置。 可通过多种方式实现此目的：

- 修改客户端上的 hosts 文件，以便将存储帐户和存储同步服务的完全限定的域名解析为所需的专用 IP 地址。 对于生产环境，强烈建议不要这样做，因为需要对需要访问专用终结点的每个客户端进行这些更改。 将不会自动处理对专用终结点/资源的更改（删除、修改等）。
- 使用 Azure 资源的 A 记录在本地服务器上为 `privatelink.file.core.windows.net` 和 `privatelink.afs.azure.net` 创建 DNS 区域。 这种方法的优势在于，本地环境中的客户端能够自动解析 Azure 资源，而无需配置每个客户端，但是，与修改 hosts 文件一样，此解决方法较不可靠，因为不会反映所做的更改。 尽管此解决方法不太可靠，但在某些环境中，它却是最佳的选择。
- 将 `core.windows.net` 和 `afs.azure.net` 区域从本地 DNS 服务器转发到 Azure 专用 DNS 区域。 可以通过特殊 IP 地址 (`168.63.129.16`) 访问 Azure 专用 DNS 主机，该 IP 地址只能在链接到 Azure 专用 DNS 区域的虚拟网络内部访问。 若要解决此限制，可以在虚拟网络中运行其他 DNS 服务器，以便将 `core.windows.net` 和 `afs.azure.net` 转发到等效的 Azure 专用 DNS 区域。 为了简化此设置，我们提供了 PowerShell cmdlet 用于在 Azure 虚拟网络中自动部署 DNS 服务器并根据需要对其进行配置。 若要了解如何设置 DNS 转发，请参阅[配置 Azure 文件存储的 DNS](storage-files-networking-dns.md)。

## <a name="encryption-in-transit"></a>传输中加密
从 Azure 文件同步代理到 Azure 文件共享或存储同步服务建立的连接始终是加密的。 尽管 Azure 存储帐户有一项设置可对 Azure 文件存储（以及从存储帐户管理的其他 Azure 存储服务）通信禁用传输加密，但当与 Azure 文件存储通信时，禁用此设置不会影响 Azure 文件同步加密。 默认情况下，所有 Azure 存储帐户均已启用传输中加密。 

有关传输中加密的详细信息，请参阅[要求在 Azure 存储中进行安全传输](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

## <a name="see-also"></a>另请参阅
- [规划 Azure 文件同步部署](storage-sync-files-planning.md)
- [部署 Azure 文件同步](storage-sync-files-deployment-guide.md)