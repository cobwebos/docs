---
title: 可用的 Azure 文件协议
description: 在创建 Azure 文件共享之前，请了解可用协议。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 8082e694c4282759c9c38560c14eb3659fcd55ec
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708132"
---
# <a name="azure-file-share-protocols"></a>Azure 文件共享协议

Azure 文件提供了两个用于连接和装载 Azure 文件共享的协议。 [服务器消息块 (SMB) 协议](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) 和 [网络文件系统 (NFS) 协议](https://en.wikipedia.org/wiki/Network_File_System) (预览版) 。 Azure 文件目前不支持多协议访问，因此共享只能是 NFS 共享或 SMB 共享。 出于此原因，我们建议在创建 Azure 文件共享之前，确定哪种协议最适合你的需求。

## <a name="differences-at-a-glance"></a>差异概览

|Feature  |NFS (预览)   |SMB  |
|---------|---------|---------|
|访问协议     |NFS 4。1         |SMB 2.1，SMB 3。0         |
|支持的 OS     |Linux 内核版本 4.3 +         |Windows 2008 R2 +、Linux 内核版本 4.11 +         |
|可用层     |高级存储         |高级存储，标准存储，热，冷         |
|复制     |LRS         |LRS、ZRS、GRS         |
|身份验证     |仅限基于主机的身份验证        |基于身份的身份验证，基于用户的身份验证         |
|权限     |UNIX 样式权限         |NTFS 样式权限         |
|文件系统语义     |POSIX 相容         |非 POSIX 相容性         |
|事例敏感性     |区分大小写         |不区分大小写         |
|硬链接支持     |支持         |不支持         |
|符号链接支持     |支持         |不支持         |
|删除或修改打开的文件     |支持         |不支持         |
|锁定     |字节范围公告网络锁管理器         |支持         |
|公共 IP 安全列表 | 不支持 | 支持|
|协议互操作| 不支持 | FileREST|

## <a name="nfs-shares-preview"></a>NFS 共享 (预览) 

将 Azure 文件共享与 NFS 4.1 一起装载目前处于预览阶段。 它提供与 Linux 的更紧密的集成。 这是完全以 POSIX 为标准的产品/服务，它是 Unix 和其他 * nix 的操作系统的不同变化形式。 这种企业级文件存储服务可进行扩展以满足你的存储需求，并可由数千个计算实例同时访问。

### <a name="limitations"></a>限制

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

#### <a name="regional-availability"></a>区域可用性

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

### <a name="best-suited"></a>最适合

Azure 文件的 NFS 适用于：

- 需要 POSIX 兼容文件共享、区分大小写或 Unix 样式权限的工作负荷 (UID/GID) 。
- 不需要 Windows 访问的以 Linux 为中心的工作负荷。

### <a name="security"></a>安全性

所有 Azure 文件数据都静态加密。 对于传输中的加密，Azure 为使用 [MACSec](https://en.wikipedia.org/wiki/IEEE_802.1AE)的 Azure 数据中心之间传输的所有数据提供加密层。 通过此步骤，在 Azure 数据中心之间传输数据时，会存在加密。 不同于使用 SMB 协议的 Azure 文件，使用 NFS 协议的文件共享不提供基于用户的身份验证。 NFS 共享的身份验证基于配置的网络安全规则。 因此，为了确保仅为你的 NFS 共享建立安全连接，你必须使用服务终结点或专用终结点。 如果要从本地访问共享，则除了专用终结点外，还必须设置 VPN 或 ExpressRoute。 不是来自以下源的请求将被拒绝：

- [专用终结点](storage-files-networking-overview.md#private-endpoints)
- [Azure VPN 网关](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
    - [) VPN 的点到站点 (P2S](../../vpn-gateway/point-to-site-about.md)
    - [站点到站点](https://docs.microsoft.com/azure/vpn-gateway/design#s2smulti)
- [ExpressRoute](../../expressroute/expressroute-introduction.md)
- [受限的公共终结点](storage-files-networking-overview.md#storage-account-firewall-settings)

有关可用网络选项的详细信息，请参阅 [Azure 文件网络注意事项](storage-files-networking-overview.md)。

## <a name="smb-shares"></a>SMB 共享

使用 SMB 装载的 azure 文件共享提供更多 Azure 文件功能，且没有 Azure 文件功能限制，因为它已正式发布。

### <a name="features"></a>功能

- Azure 文件同步
- 基于标识的身份验证
- Azure 备份支持
- 快照
- 软删除
- 加密传输和静态加密

### <a name="best-suited"></a>最适合

Azure 文件的 SMB 适用于：

- 生产环境
- 需要[功能](#features)中列出的任何功能的客户

## <a name="next-steps"></a>后续步骤

- [创建 NFS 文件共享](storage-files-how-to-create-nfs-shares.md)
- [创建 SMB 文件共享](storage-how-to-create-file-share.md)