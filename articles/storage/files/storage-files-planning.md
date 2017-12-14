---
title: "规划 Azure 文件部署 | Microsoft Docs"
description: "了解规划 Azure 文件部署时应考虑的问题。"
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: c28f341fb64271e2173cd377fa06c567e0e054a6
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2017
---
# <a name="planning-for-an-azure-files-deployment"></a>规划 Azure 文件部署
[Azure 文件](storage-files-introduction.md)在云中提供完全托管的文件共享，这些共享项可通过行业标准 SMB 协议进行访问。 由于 Azure 文件是完全托管的，因此在生产方案中对其进行部署比部署和管理文件服务器或 NAS 设备简单得多。 本文介绍在组织内部署 Azure 文件共享以供生产使用时应考虑的主题。

## <a name="management-concepts"></a>管理概念
 下图说明了 Azure 文件管理构造：

![文件结构](./media/storage-files-introduction/files-concepts.png)

* **存储帐户**：对 Azure 存储服务的所有访问都要通过存储帐户来完成。 有关存储帐户容量的详细信息，请参阅[可伸缩性和性能目标](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

* 共享：文件存储共享是 Azure 中的 SMB 文件共享。 所有目录和文件都必须在父共享中创建。 一个帐户可以包含无限数量的共享，一个共享可以存储无限数量的文件，直到达到文件共享的 5TiB 总容量为止。

* 目录：可选的目录层次结构。

* 文件：共享中的文件。 文件大小最大可以为 1 TiB。

* URL 格式：对于使用文件 REST 协议提出的 Azure 文件共享请求，可采用以下 URL 格式对文件进行寻址：

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/directories>/<file>
    ```

## <a name="data-access-method"></a>数据访问方法
Azure 文件提供两个内置的简便数据访问方法，用户可单独使用或结合使用这些方法来访问数据：

1. 直接云访问：可使用行业标准服务器消息块 (SMB) 协议或通过文件 REST API 在 [Windows](storage-how-to-use-files-windows.md)、[macOS](storage-how-to-use-files-mac.md) 和/或 [Linux](storage-how-to-use-files-linux.md) 上装载任意 Azure 文件共享。 使用 SMB，可直接在 Azure 中的文件共享上读取和写入共享文件。 若要装载在 Azure VM 上，操作系统中的 SMB 客户端必须至少支持 SMB 2.1。 若要装载在本地（例如用户工作站），工作站支持的 SMB 客户端必须至少支持 SMB 3.0（已加密）。 除 SMB 以外，新应用程序或服务可通过文件 REST 直接访问文件共享，该文件 REST 为软件开发提供简单可缩放的应用程序编程接口。
2. Azure 文件同步（预览版）：可使用 Azure 文件同步将共享复制到本地或 Azure 中的 Windows Server。 用户可通过 SMB 或 NFS 共享等 Windows Server 访问文件共享。 这适用于要在远离 Azure 数据中心的位置访问和修改数据的方案，例如分支机构方案。 可在多个 Windows Server 终结点（例如多个分支机构）之间复制数据。 最后，可将数据分层到 Azure 文件，以便所有数据仍可通过 Server 进行访问，但 Server 没有完整的数据副本。 相反，数据由用户打开时会被无缝召回。

下表说明了用户和应用程序如何访问 Azure 文件共享：

| | 直接云访问 | Azure 文件同步 |
|------------------------|------------|-----------------|
| 需使用哪些协议？ | Azure 文件支持 SMB 2.1、SMB 3.0 和文件 REST API。 | 通过 Windows Server 上支持的任意协议（SMB、NFS、FTPS 等）访问 Azure 文件共享 |  
| 在何处运行工作负荷？ | 在 Azure 中：Azure 文件支持直接访问数据。 | 网络速度较慢的本地：Windows、Linux 和macOS 客户端可将本地 Windows 文件共享作为 Azure 文件共享的快速缓存进行装载。 |
| 需要何种级别的 ACL？ | 共享和文件级别。 | 共享、文件和用户级别。 |

## <a name="data-security"></a>数据安全
Azure 文件提供可确保数据安全的几个内置选项：

* 支持以下两种在线协议的加密：SMB 3.0 加密和通过 HTTPS 的文件 REST。 默认情况下： 
    * 支持 SMB 3.0 加密的客户端通过加密通道发送和接收数据。
    * 不支持 SMB 3.0 的客户端可通过未加密的 SMB 2.1 或 SMB 3.0 进行数据中心内通信。 请注意，不允许客户端通过未加密的 SMB 2.1 或 SMB 3.0 进行数据中心内通信。
    * 客户端可以通过 HTTP 或 HTTPS 与文件 REST 通信。
* 静态加密（[Azure 存储服务加密](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)）：我们正在底层的 Azure 存储平台上启用存储服务加密 (SSE)。 这意味着默认情况下将为所有存储帐户启用加密。 如果要在默认启用静态加密的区域中创建新的存储帐户，则无需执行任何操作即可启用加密。 静态数据使用完全托管的密钥进行加密。 静态加密不会增加存储成本，也不会降低性能。 
* 加密数据在传输中的可选要求：选定后，Azure 文件不允许通过未加密通道访问数据。 具体而言，仅允许具有加密连接的 HTTPS 和 SMB 3.0。 

    > [!Important]  
    > 要求安全传输数据将导致较早的 SMB 客户端无法与 SMB 3.0 通信，进而造成加密失败。 有关详细信息，请参阅[在 Windows 上装载](storage-how-to-use-files-windows.md)、[在 Linux 上装载](storage-how-to-use-files-linux.md)和[在 macOS 上装载](storage-how-to-use-files-mac.md)。

为了实现最大安全性，强烈建议始终启用这两个静态加密功能，并在使用新式客户端访问数据时启用数据传输加密。 例如，如需在仅支持 SMB 2.1 的 Windows Server 2008 R2 VM 上装载共享，则需要允许存储帐户接受未加密的流量，因为 SMB 2.1 不支持加密。

如果使用 Azure 文件同步访问 Azure 文件共享，我们将始终使用 HTTPS 和加密的 SMB 3.0 将数据同步到 Windows Server，而不考虑是否需要对静态数据加密。

## <a name="data-redundancy"></a>数据冗余
Azure 文件支持两个数据冗余选项：本地冗余存储 (LRS) 和异地冗余存储 (GRS)。 以下部分描述了本地冗余存储和异地冗余存储之间的差异：

### <a name="locally-redundant-storage"></a>本地冗余存储
[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="geo-redundant-storage"></a>异地冗余存储
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-GRS.md)]

## <a name="data-growth-pattern"></a>数据增长模式
现在，Azure 文件共享的最大大小为 5 TiB，包括共享快照。 鉴于此当前限制，必须考虑部署 Azure 文件共享时的预期数据增长。 请注意，一个 Azure 存储帐户可以存储多个共享，存储的所有共享总容量为 500 TiB。

可使用 Azure 文件同步将多个 Azure 文件共享同步到单个 Windows 文件服务器。这可确保本地的较旧、超大文件共享能够导入 Azure 文件同步。有关详细信息，请参阅[规划 Azure 文件同步部署](storage-files-planning.md)。

## <a name="data-transfer-method"></a>数据传输方法
可通过多种简单的选项将数据从现有文件共享（例如本地文件共享）批量传输到 Azure 文件。 几种常用选项包括（非详尽列表）：

* Azure 文件同步：作为 Azure 文件共享（“云终结点”）和 Windows 目录命名空间（“服务器终结点”）之间首个同步的一部分，Azure 文件同步可将现有文件共享中的所有数据复制到 Azure 文件。
* [Azure 导入/导出](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)：使用 Azure 导入/导出服务，可将硬盘驱动器寄送到 Azure 数据中心，从而安全地将大量数据传输到 Azure 文件共享。 
* [Robocopy](https://technet.microsoft.com/library/cc733145.aspx)：Robocopy 是 Windows 和 Windows Server 自带的一款知名复制工具。 Robocopy 可用于将数据传输到 Azure 文件，方法是在本地装载文件共享，然后使用装载位置作为 Robocopy 命令的目标位置。
* [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#upload-files-to-an-azure-file-share)：AzCopy 是一个命令行实用工具，专用于使用具有优化性能的简单命令在 Azure 文件和 Azure Blob 存储中复制/粘贴数据。 AzCopy 适用于 Windows 和 Linux。

## <a name="next-steps"></a>后续步骤
* [规划 Azure 文件同步部署](storage-sync-files-planning.md)
* [部署 Azure 文件](storage-files-deployment-guide.md)
* [部署 Azure 文件同步](storage-sync-files-deployment-guide.md)