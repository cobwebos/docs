---
title: Azure Blob 存储中的网络文件系统3.0 支持 (预览) |Microsoft Docs
description: Blob 存储现在支持网络文件系统 (NFS) 3.0 协议。 这种支持使 Windows 和 Linux 客户端能够在 Blob 存储中从 Azure 虚拟机 (VM) 或本地运行的计算机上装载容器。
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 32eafc41451c42a9c929e94d30450b923e7eae83
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/04/2020
ms.locfileid: "87760917"
---
# <a name="network-file-system-nfs-30-protocol-support-in-azure-blob-storage-preview"></a>Azure Blob 存储中的网络文件系统 (NFS) 3.0 协议支持 (预览) 

Blob 存储现在支持网络文件系统 (NFS) 3.0 协议。 通过此支持，Windows 或 Linux 客户端可以从 Azure 虚拟机 (VM) 或本地计算机中的 Azure 虚拟机装载容器。 

> [!NOTE]
> Azure Blob 存储中的 NFS 3.0 协议支持提供公共预览版，并在以下区域提供：美国东部、美国中部和加拿大中部。

## <a name="general-workflow-mounting-a-storage-account-container"></a>常规工作流：装载存储帐户容器

若要装载存储帐户容器，则必须执行这些操作。

1. 将 NFS 3.0 协议功能注册到你的订阅。

2. 验证功能是否已注册。

3.  (VNet) 创建 Azure 虚拟网络。

4. 配置网络安全。

5. 创建并配置仅接受来自 VNet 的流量的存储帐户。

6. 在存储帐户中创建一个容器。

7. 装载容器。

有关分步指南，请参阅[使用网络文件系统装载 Blob 存储 (NFS) 3.0 协议 (预览) ](network-file-system-protocol-support-how-to.md)。

> [!IMPORTANT]
> 务必按顺序完成这些任务。 你无法在你的帐户上启用 NFS 3.0 协议之前装入你创建的容器。 此外，在你的帐户上启用了 NFS 3.0 协议后，你将无法禁用它。

## <a name="network-security"></a>网络安全

存储帐户必须包含在 VNet 中。 VNet 使客户端可以安全地连接到你的存储帐户。 保护帐户中数据的唯一方法是使用 VNet 和其他网络安全设置。 用于保护数据的任何其他工具（包括帐户密钥授权、Azure Active Directory (AD) 安全性和访问控制列表 () Acl）在其上启用了 NFS 3.0 协议支持的帐户中不受支持。 

若要了解详细信息，请参阅[Blob 存储的网络安全建议](security-recommendations.md#networking)。

## <a name="supported-network-connections"></a>支持的网络连接

客户端可以通过公共或[专用终结点](../common/storage-private-endpoints.md)进行连接，并且可以从以下任意网络位置进行连接：

- 为存储帐户配置的 VNet。 

  为了本文的目的，我们将该 VNet 称为*主 vnet*。 若要了解详细信息，请参阅[从虚拟网络授予访问权限](../common/storage-network-security.md#grant-access-from-a-virtual-network)。

- 与主 VNet 位于同一区域的对等互连 VNet。

  需要配置存储帐户以允许访问此对等互连 VNet。 若要了解详细信息，请参阅[从虚拟网络授予访问权限](../common/storage-network-security.md#grant-access-from-a-virtual-network)。

- 使用[VPN 网关](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)或[ExpressRoute 网关](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager)连接到主 VNet 的本地网络。 

  若要了解详细信息，请参阅[配置来自本地网络的访问权限](../common/storage-network-security.md#configuring-access-from-on-premises-networks)。

- 连接到对等互连网络的本地网络。

  这可以通过使用[VPN 网关](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)或[ExpressRoute 网](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager)关以及[网关传输](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/vnet-peering#gateway-transit)来实现。 

> [!IMPORTANT]
> 如果要从本地网络连接，请确保客户端允许通过端口111和2048进行传出通信。 NFS 3.0 协议使用这些端口。

## <a name="azure-storage-features-not-yet-supported"></a>尚不支持 Azure 存储功能

在你的帐户上启用 NFS 3.0 协议后，不支持以下 Azure 存储功能。 

- Azure Active Directory (AD) 安全性

- 类似于 POSIX 的访问控制列表 (Acl) 

- 能够对现有存储帐户启用 NFS 3.0 支持

- 支持在存储帐户中禁用 NFS 3.0 支持的功能 (在启用后) 

- 能够使用 REST Api 或 Sdk 写入 blob。 
  
## <a name="nfs-30-features-not-yet-supported"></a>尚不支持 NFS 3.0 功能

Azure Data Lake Storage Gen2 尚不支持以下 NFS 3.0 功能。

- 经由 UDP 的 NFS 3.0。 仅支持通过 TCP 的 NFS 3.0。

- 通过网络锁管理器 (NLM) 锁定文件。 装载命令必须包含 `-o nolock` 参数。

- 装载子目录。 只能 (容器) 中装载根目录。

- 列出装载 (例如：通过使用命令 `showmount -a`) 

- 列出导出 (例如：通过使用命令 `showmount -e`) 

- 以只读方式导出容器

## <a name="pricing"></a>定价

在预览期间，存储在存储帐户中的数据按每 GB 每 GB 的每 GB 的相同容量费率计费。 

在预览期间，不会对事务收费。 交易的价格可能会有所变化，并将在正式发布时确定。

## <a name="next-steps"></a>后续步骤

若要开始，请参阅[使用网络文件系统装载 Blob 存储 (NFS) 3.0 协议 (预览) ](network-file-system-protocol-support-how-to.md)。





