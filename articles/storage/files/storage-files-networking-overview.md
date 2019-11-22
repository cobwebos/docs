---
title: Azure 文件存储的网络注意事项 | Microsoft Docs
description: Azure 文件存储的网络选项概述。
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 596479652478bffb6d18a90fc53d5972b3839408
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73126533"
---
# <a name="azure-files-networking-considerations"></a>Azure 文件存储的网络注意事项 
可以通过两种方式连接到 Azure 文件共享：

- 直接通过 SMB 或 FileREST 协议访问共享。 要删除尽可能多的本地服务器时，主要使用此访问模式。
- 使用 Azure 文件同步在本地服务器上创建 Azure 文件共享的缓存，并使用所选协议（SMB、NFS、FTPS 等）从本地服务器访问文件共享的数据。 此访问模式非常方便，因为它结合了本地性能以及云缩放和无服务器可附加服务（例如，Azure 备份）的优势。

本文重点介绍如何在用例要求直接访问 Azure 文件共享而不是使用 Azure 文件同步时配置网络。要详细了解 Azure 文件同步部署的网络注意事项，请参阅[配置 Azure 文件同步代理和防火墙设置](storage-sync-files-firewall-and-proxy.md)。

## <a name="storage-account-settings"></a>存储帐户设置
存储帐户是代表共享存储池的管理结构，你可以在其中部署多个文件共享以及其他存储资源（例如，Blob 容器或队列）。 Azure 存储帐户公开了用于保护网络安全的两套基本设置：传输中加密以及防火墙和虚拟网络 (VNet)。

### <a name="encryption-in-transit"></a>传输中加密
默认情况下，所有 Azure 存储帐户均已启用传输中加密。 即通过 SMB 装载文件共享或通过 FileREST 协议（例如，通过 Azure门户、PowerShell/CLI 或 Azure SDK）访问文件共享时，Azure 文件存储仅允许通过加密或 HTTPS 使用 SMB 3.0 及更高版本建立的连接。 如果启用了传输中加密，则不支持 SMB 3.0 的客户端或支持 SMB 3.0 但不支持 SMB 加密的客户端将无法装载 Azure 文件共享。 要详细了解哪些操作系统支持具有加密功能的 SMB 3.0，请参阅适用于 [Windows](storage-how-to-use-files-windows.md)、[macOS](storage-how-to-use-files-mac.md) 和 [Linux](storage-how-to-use-files-linux.md) 的详细文档。 PowerShell、CLI 和 SDK 的所有当前版本均支持 HTTPS。  

可以为 Azure 存储帐户禁用传输中加密。 禁用加密后，Azure 文件存储还将允许没有加密功能的 SMB 2.1、SMB 3.0 和通过 HTTP 进行的未经加密的 FileREST API 调用。 禁用传输中加密的主要原因是为了支持必须在更低版本的操作系统（例如，Windows Server 2008 R2 或更低版本的 Linux 发行版）上运行的旧版应用程序。 Azure 文件存储仅允许在与 Azure 文件共享相同的 Azure 区域内建立 SMB 2.1 连接；Azure 文件共享的 Azure 区域之外的 SMB 2.1 客户端（例如，本地或其他 Azure 区域）将无法访问文件共享。

有关传输中加密的详细信息，请参阅[要求在 Azure 存储中进行安全传输](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

### <a name="firewalls-and-virtual-networks"></a>防火墙和虚拟网络 
防火墙是一种网络策略，使请求能够访问存储帐户中的 Azure 文件共享和其他存储资源。 使用默认网络设置创建存储帐户时，它不限于特定网络，因此可以访问 Internet。 这并不意味着 Internet 上的任何人都可以访问存储帐户中托管的 Azure 文件共享上存储的数据，反而存储帐户将接受来自任何网络的授权请求。 可以使用存储帐户密钥、共享访问签名 (SAS) 令牌（仅限 FileREST）或 Active Directory 用户主体来授权请求。 

用于存储帐户的防火墙策略可以用于限制对某些 IP 地址/范围或虚拟网络的访问权限。 通常，大多数针对存储帐户的防火墙策略均限制了对虚拟网络的网络访问权限。 

[虚拟网络](../../virtual-network/virtual-networks-overview.md)（或 VNet）类似于你在自己的数据中心运行的传统网络。 使用虚拟网络，你可以为 Azure 文件共享、VM、SQL 数据库等 Azure 资源创建安全的通信通道，以便相互进行通信。 与 Azure 存储帐户或 Azure VM 一样，VNet 是在资源组中部署的 Azure 资源。 通过其他网络配置，Azure VNet 也可以连接到本地网络。

将 Azure VM 等资源添加到虚拟网络时，附加到虚拟机的虚拟网络接口 (NIC) 仅限于该 VNet。 Azure VM 是虚拟计算机，自然具有 NIC，所以这是可能的。 虚拟机作为 Azure 的基础结构即服务（或 IaaS）产品系列的一部分提供。 由于 Azure 文件共享是无服务器文件共享，因此它们没有 NIC 可以添加到 VNet。 换句话说，Azure 文件存储作为 Azure 的平台即服务（或 PaaS）产品系列的一部分提供。 为了使存储帐户能够成为 VNet 的一部分，Azure 支持 PaaS 服务的服务终结点这一概念。 服务终结点允许 PaaS 服务成为虚拟网络的一部分。 若要详细了解服务终结点，请参阅[虚拟网络服务终结点](../../virtual-network/virtual-network-service-endpoints-overview.md)。

可以将一个存储帐户添加到一个或多个虚拟网络中。 若要详细了解如何将存储帐户添加到虚拟网络或配置其他防火墙设置，请参阅[配置 Azure 存储防火墙和虚拟网络](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

## <a name="azure-networking"></a>Azure 网络
默认情况下，可以通过 Internet 访问包含 Azure 文件存储的 Azure 服务。 由于流向存储帐户的流量默认是加密的（并且从不允许在 Azure 区域之外进行 SMB 2.1 装载），因此通过 Internet 访问 Azure 文件共享实际上是安全的。 根据组织的策略或独特的法规要求，可能需要与 Azure 进行更严格的通信，因此 Azure 提供了几种方法来限制从 Azure 外部到 Azure 文件存储的通信方式。 使用以下服务产品/服务，可以在访问 Azure 文件共享时进一步保护网络安全：

- [Azure VPN 网关](../../vpn-gateway/vpn-gateway-about-vpngateways.md)：VPN 网关是特定类型的虚拟网关，用于通过 Internet 在 Azure 虚拟网络和备用位置（例如，本地）之间发送加密的流量。 Azure VPN 网关是一种 Azure 资源，可以与存储帐户或其他 Azure 资源一起部署在资源组中。 VPN 网关公开了两种不同类型的连接：
    - [点到站点 (P2S) VPN](../../vpn-gateway/point-to-site-about.md) 网关连接，这是 Azure 与单个客户端之间建立的 VPN 连接。 对于那些不属于组织的本地网络的设备（例如，希望能够在家中、咖啡店或酒店随时随地装载 Azure 文件共享的远程办公人员），此解决方案非常有用。 若要将 P2S VPN 连接与 Azure 文件存储一起使用，需要为每个要连接的客户端配置 P2S VPN 连接。 
    - [站点到站点 (S2S) VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)，这是 Azure 与组织的网络之间建立的 VPN 连接。 通过 S2S VPN 连接，你可以为组织的网络上托管的 VPN 服务器或设备一次性配置 VPN 连接，而不是为需要访问 Azure 文件共享的每个客户端设备都进行一次配置。
- [ExpressRoute](../../expressroute/expressroute-introduction.md)，使你可以在 Azure 与不经过 Internet 的本地网络之间创建定义的路由。 因为 ExpressRoute 在本地数据中心和 Azure 之间提供了专用路径，所以当存在网络性能方面的顾虑时，ExpressRoute 可能会很有用。 组织的策略或法规要求使用确定的路径访问云中的资源时，ExpressRoute 也是一个不错的选择。

## <a name="securing-access-from-on-premises"></a>保护来自本地的访问 
将常规用途文件共享（例如，Office 文档、PDF、CAD 文档等）迁移到 Azure 文件存储时，用户通常需要继续从本地设备（例如，工作站、笔记本电脑和平板电脑）访问他们的文件。 使用常规用途文件共享的主要考虑因素是本地用户如何通过 Internet 或 WAN 安全地访问其文件共享。

若要从本地访问 Azure 文件共享，最简单方法是为端口 445（SMB 使用的端口）打开本地网络，并装载 Azure 门户提供的 UNC 路径。 此操作不需要特殊网络。 许多客户都不愿意打开端口 445，因为关于 SMB 1.0 的安全指南已过时，Microsoft 不会将其视为 Internet 安全协议。 Azure 文件存储不实现 SMB 1.0。 

SMB 3.0 的设计明确要求采用 Internet 安全文件共享协议。 因此，使用 SMB 3.0 及更高版本时，从计算机网络的角度来看，打开端口 445 与打开用于 HTTPS 连接的端口 443 是一样的。 Microsoft 建议你按照以下步骤操作，而不是通过阻止端口 445 来防止不安全的 SMB 1.0 流量：

> [!Important]  
> 即使你决定对出站流量关闭端口 445，Microsoft 仍建议你按照以下步骤从环境中删除 SMB 1.0。

1. 请确认是否在组织的设备上删除或禁用了 SMB 1.0。 Windows 和 Windows Server 当前支持的所有版本均支持删除或禁用 SMB 1.0，并且自 Windows 10 1709 版起，默认情况下 Windows 上未安装 SMB 1.0。 若要详细了解如何禁用 SMB 1.0，请参阅特定于 OS 的页面：
    - [保护 Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [保护 Linux](storage-how-to-use-files-linux.md#securing-linux)
1. 请确认组织内没有需要 SMB 1.0 的产品并将其删除。 我们提供 [SMB1 产品交换所](https://aka.ms/stillneedssmb1)，其中包含需要 SMB 1.0 的所有 Microsoft 已知的第一和第三方产品。 
1. （可选）在组织的本地网络中使用第三方防火墙，以阻止 SMB 1.0 流量。

如果组织要求按策略或法规阻止端口 445，则可以使用 Azure VPN 网关或 ExpressRoute 通过端口 443 传输流量。 若要详细了解部署上述内容的具体步骤，请参阅操作说明页面：
- [配置站点到站点 (S2S) VPN 以与 Azure 文件存储一起使用](storage-files-configure-s2s-vpn.md)
- [在 Windows 上配置点到站点 (P2S) VPN 以与 Azure 文件存储一起使用](storage-files-configure-p2s-vpn-windows.md)
- [在 Linux 上配置点到站点 (P2S) VPN 以与 Azure 文件存储一起使用](storage-files-configure-p2s-vpn-linux.md)

组织可能还有其他要求，即通过本地站点的出站流量必须遵循云中资源的确定路径。 如果是这样，则 ExpressRoute 能够满足此要求。

## <a name="securing-access-from-cloud-resources"></a>保护来自云资源的访问
通常，将本地应用程序直接迁移到云时，将同时迁移该应用程序和应用程序的数据。 这意味着，对于需要访问文件共享才能运行的特定虚拟机或 Azure 服务，直接迁移的主要考虑因素是锁定对 Azure 文件共享的访问权限。 

你可能想要使用 VNet 来限制哪些 VM 或其他 Azure 资源可以建立网络连接（SMB 装载或对 Azure 文件共享的 REST API 调用）。 如果允许未经加密的流量传输到存储帐户，我们始终建议你将 Azure 文件共享置于 VNet 中。 否则，是否使用 VNet 取决于业务需求和组织政策。

允许未经加密的流量传输到 Azure 文件共享的主要原因是为了支持使用 SMB 2.1 或使用没有加密功能的 SMB 3.0（对于一些 Linux 发行版）访问 Azure 文件共享的 Windows Server 2008 R2、Windows 7 或其他更低版本的 OS。 不建议在支持带加密功能的 SMB 3.0 及更高版本的操作系统上使用没有加密功能的 SMB 2.1 或 SMB 3.0。

## <a name="see-also"></a>另请参阅
- [Azure 文件存储概述](storage-files-introduction.md)
- [规划 Azure 文件部署](storage-files-planning.md)