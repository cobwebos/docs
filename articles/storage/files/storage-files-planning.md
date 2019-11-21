---
title: 规划 Azure 文件部署 | Microsoft Docs
description: 了解规划 Azure 文件部署时应考虑的问题。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7d11dc70a78fcec62032c2a6af168bd306c9d416
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227863"
---
# <a name="planning-for-an-azure-files-deployment"></a>规划 Azure 文件部署

[Azure 文件](storage-files-introduction.md)在云中提供完全托管的文件共享，这些共享项可通过行业标准 SMB 协议进行访问。 由于 Azure 文件是完全托管的，因此在生产方案中对其进行部署比部署和管理文件服务器或 NAS 设备简单得多。 本文介绍在组织内部署 Azure 文件共享以供生产使用时应考虑的主题。

## <a name="management-concepts"></a>管理概念

 下图说明了 Azure 文件管理构造：

![文件结构](./media/storage-files-introduction/files-concepts.png)

* **存储帐户**：对 Azure 存储服务的所有访问都要通过存储帐户来完成。 有关存储帐户容量的详细信息，请参阅[可伸缩性和性能目标](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

* 共享：文件存储共享是 Azure 中的 SMB 文件共享。 所有目录和文件都必须在父共享中创建。 An account can contain an unlimited number of shares and a share can store an unlimited number of files, up to the total capacity of the file share. The total capacity for premium and standard file shares is 100 TiB.

* 目录：可选的目录层次结构。

* 文件：共享中的文件。 文件大小最大可以为 1 TiB。

* URL 格式：对于使用文件 REST 协议提出的 Azure 文件共享请求，可采用以下 URL 格式对文件进行寻址：

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/<file>
    ```

## <a name="data-access-method"></a>数据访问方法

Azure 文件提供两个内置的简便数据访问方法，用户可单独使用或结合使用这些方法来访问数据：

1. 直接云访问：可使用行业标准服务器消息块 (SMB) 协议或通过文件 REST API 在 [Windows](storage-how-to-use-files-windows.md)、[macOS](storage-how-to-use-files-mac.md) 和/或 [Linux](storage-how-to-use-files-linux.md) 上装载任意 Azure 文件共享。 使用 SMB，可直接在 Azure 中的文件共享上读取和写入共享文件。 若要装载在 Azure VM 上，操作系统中的 SMB 客户端必须至少支持 SMB 2.1。 若要装载在本地（例如用户工作站），工作站支持的 SMB 客户端必须至少支持 SMB 3.0（已加密）。 除 SMB 以外，新应用程序或服务可通过文件 REST 直接访问文件共享，该文件 REST 为软件开发提供简单可缩放的应用程序编程接口。
2. **Azure 文件同步**：可使用 Azure 文件同步将共享复制到本地或 Azure 中的 Windows Server。 用户可通过 SMB 或 NFS 共享等 Windows Server 访问文件共享。 这适用于要在远离 Azure 数据中心的位置访问和修改数据的方案，例如分支机构方案。 可在多个 Windows Server 终结点（例如多个分支机构）之间复制数据。 最后，可将数据分层到 Azure 文件，以便所有数据仍可通过 Server 进行访问，但 Server 没有完整的数据副本。 相反，数据由用户打开时会被无缝召回。

下表说明了用户和应用程序如何访问 Azure 文件共享：

| | 直接云访问 | Azure 文件同步 |
|------------------------|------------|-----------------|
| 需使用哪些协议？ | Azure 文件支持 SMB 2.1、SMB 3.0 和文件 REST API。 | 通过 Windows Server 上支持的任意协议（SMB、NFS、FTPS 等）访问 Azure 文件共享 |  
| 在何处运行工作负荷？ | 在 Azure 中：Azure 文件支持直接访问数据。 | 网络速度较慢的本地：Windows、Linux 和macOS 客户端可将本地 Windows 文件共享作为 Azure 文件共享的快速缓存进行装载。 |
| 需要何种级别的 ACL？ | 共享和文件级别。 | 共享、文件和用户级别。 |

## <a name="data-security"></a>数据安全

Azure 文件提供可确保数据安全的几个内置选项：

* 支持以下两种在线协议的加密：SMB 3.0 加密和通过 HTTPS 的文件 REST。 默认情况下： 
    * Clients that support SMB 3.0 encryption send and receive data over an encrypted channel.
    * Clients that do not support SMB 3.0 with encryption can communicate intra-datacenter over SMB 2.1 or SMB 3.0 without encryption. 不允许 SMB 客户端通过无加密功能的 SMB 2.1 或 SMB 3.0 进行数据中心内通信。
    * 客户端可以通过 HTTP 或 HTTPS 与文件 REST 通信。
* 静态加密（[Azure 存储服务加密](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)）：为所有存储帐户启用存储服务加密 (SSE)。 静态数据使用完全托管的密钥进行加密。 静态加密不会增加存储成本，也不会降低性能。 
* Optional requirement of encrypted data in-transit: when selected, Azure Files rejects access to the data over unencrypted channels. 具体而言，仅允许具有加密连接的 HTTPS 和 SMB 3.0。

    > [!Important]  
    > 要求安全传输数据将导致较早的 SMB 客户端无法与 SMB 3.0 通信，进而造成加密失败。 有关详细信息，请参阅[在 Windows 上装载](storage-how-to-use-files-windows.md)、[在 Linux 上装载](storage-how-to-use-files-linux.md)和[在 macOS 上装载](storage-how-to-use-files-mac.md)。

为了实现最大安全性，强烈建议始终启用这两个静态加密功能，并在使用新式客户端访问数据时启用数据传输加密。 例如，如需在仅支持 SMB 2.1 的 Windows Server 2008 R2 VM 上装载共享，则需要允许存储帐户接受未加密的流量，因为 SMB 2.1 不支持加密。

如果使用 Azure 文件同步访问 Azure 文件共享，我们将始终使用 HTTPS 和加密的 SMB 3.0 将数据同步到 Windows Server，而不考虑是否需要对静态数据加密。

## <a name="file-share-performance-tiers"></a>文件共享性能层

Azure Files offers two performance tiers: standard and premium.

### <a name="standard-file-shares"></a>标准文件共享

Standard file shares are backed by hard disk drives (HDDs). Standard file shares provide reliable performance for IO workloads that are less sensitive to performance variability such as general-purpose file shares and dev/test environments. 标准文件共享只能在即用即付计费模型下使用。

> [!IMPORTANT]
> If you want to use file shares larger than 5 TiB, see the [Onboard to larger file shares (standard tier)](#onboard-to-larger-file-shares-standard-tier) section for steps to onboard, as well as regional availability and restrictions.

### <a name="premium-file-shares"></a>Premium file shares

Premium file shares are backed by solid-state drives (SSDs). Premium file shares provide consistent high performance and low latency, within single-digit milliseconds for most IO operations, for IO-intensive workloads. This makes them suitable for a wide variety of workloads like databases, web site hosting, and development environments. 高级文件共享只能在预配的计费模型下使用。 Premium file shares use a deployment model separate from standard file shares.

Azure Backup is available for premium file shares and Azure Kubernetes Service supports premium file shares in version 1.13 and above.

If you'd like to learn how to create a premium file share, see our article on the subject: [How to create an Azure premium file storage account](storage-how-to-create-premium-fileshare.md).

Currently, you cannot directly convert between a standard file share and a premium file share. If you would like to switch to either tier, you must create a new file share in that tier and manually copy the data from your original share to the new share you created. You can do this using any of the Azure Files supported copy tools, such as Robocopy or AzCopy.

> [!IMPORTANT]
> Premium file shares are available with LRS in most regions that offer storage accounts and with ZRS in a smaller subset of regions. To find out if premium file shares are currently available in your region, see the [products available by region](https://azure.microsoft.com/global-infrastructure/services/?products=storage) page for Azure. To find out what regions support ZRS, see [Support coverage and regional availability](../common/storage-redundancy-zrs.md#support-coverage-and-regional-availability).
>
> To help us prioritize new regions and premium tier features, please fill out this [survey](https://aka.ms/pfsfeedback).

#### <a name="provisioned-shares"></a>预配的共享

高级文件共享是基于固定的 GiB/IOPS/吞吐量比率预配的。 对于预配的每个 GiB，将向该共享分配 1 IOPS 和 0.1 MiB/秒的吞吐量，最多可达每个共享的最大限制。 允许的最小预配为 100 GiB 以及最小的 IOPS/吞吐量。

最大限度地提供服务时，对于预配的存储，所有共享都可以突增到每 GiB 3 IOPS，持续 60 分钟或更长时间，具体取决于共享大小。 新共享将根据预配的容量以完全突增额度开始。

Shares must be provisioned in 1 GiB increments. Minimum size is 100 GiB, next size is 101 GiB and so on.

> [!TIP]
> Baseline IOPS = 1 * provisioned GiB. (Up to a max of 100,000 IOPS).
>
> Burst Limit = 3 * Baseline IOPS. (Up to a max of 100,000 IOPS).
>
> egress rate = 60 MiB/s + 0.06 * provisioned GiB
>
> ingress rate = 40 MiB/s + 0.04 * provisioned GiB

Provisioned share size is specified by share quota. Share quota can be increased at any time but can be decreased only after 24 hours since the last increase. After waiting for 24 hours without a quota increase, you can decrease the share quota as many times as you like, until you increase it again. IOPS/Throughput scale changes will be effective within a few minutes after the size change.

It is possible to decrease the size of your provisioned share below your used GiB. If you do this, you will not lose data but, you will still be billed for the size used and receive the performance (baseline IOPS, throughput, and burst IOPS) of the provisioned share, not the size used.

The following table illustrates a few examples of these formulae for the provisioned share sizes:

|Capacity (GiB) | 基线 IOPS | Burst IOPS | Egress (MiB/s) | Ingress (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | 最多 300     | 66   | 44   |
|500         | 500     | Up to 1,500   | 90   | 60   |
|1,024       | 1,024   | Up to 3,072   | 122   | 81   |
|5,120       | 5,120   | Up to 15,360  | 368   | 245   |
|10,240      | 10,240  | Up to 30,720  | 675 | 450   |
|33,792      | 33,792  | Up to 100,000 | 2,088 | 1,392   |
|51,200      | 51,200  | Up to 100,000 | 3,132 | 2,088   |
|102,400     | 100,000 | Up to 100,000 | 6,204 | 4,136   |

> [!NOTE]
> File shares performance is subject to machine network limits, available network bandwidth, IO sizes, parallelism, among many other factors. For example, based on internal testing with 8 KiB read/write IO sizes, a single Windows virtual machine, *Standard F16s_v2*, connected to premium file share over SMB could achieve 20K read IOPS and 15K write IOPS. With 512 MiB read/write IO sizes, the same VM could achieve 1.1 GiB/s egress and 370 MiB/s ingress throughput. To achieve maximum performance scale, spread the load across multiple VMs. Please refer [troubleshooting guide](storage-troubleshooting-files-performance.md) for some common performance issues and workarounds.

#### <a name="bursting"></a>Bursting

Premium file shares can burst their IOPS up to a factor of three. Bursting is automated and operates based on a credit system. Bursting works on a best effort basis and the burst limit is not a guarantee, file shares can burst *up to* the limit.

Credits accumulate in a burst bucket whenever traffic for your file share is below baseline IOPS. For example, a 100 GiB share has 100 baseline IOPS. If actual traffic on the share was 40 IOPS for a specific 1-second interval, then the 60 unused IOPS are credited to a burst bucket. These credits will then be used later when operations would exceed the baseline IOPs.

> [!TIP]
> Size of the burst bucket = Baseline IOPS * 2 * 3600.

Whenever a share exceeds the baseline IOPS and has credits in a burst bucket, it will burst. Shares can continue to burst as long as credits are remaining, though shares smaller than 50 TiB will only stay at the burst limit for up to an hour. Shares larger than 50 TiB can technically exceed this one hour limit, up to two hours but, this is based on the number of burst credits accrued. Each IO beyond baseline IOPS consumes one credit and once all credits are consumed the share would return to baseline IOPS.

Share credits have three states:

- Accruing, when the file share is using less than the baseline IOPS.
- Declining, when the file share is bursting.
- Remaining constant, when there are either no credits or baseline IOPS are in use.

New file shares start with the full number of credits in its burst bucket. Burst credits will not be accrued if the share IOPS fall below baseline IOPS due to throttling by the server.

## <a name="file-share-redundancy"></a>文件共享冗余

Azure Files standard shares supports four data redundancy options: locally redundant storage (LRS), zone redundant storage (ZRS), geo-redundant storage (GRS), and geo-zone-redundant storage (GZRS) (preview).

Azure Files premium shares support both LRS and ZRS, ZRS is currently available in a smaller subset of regions.

以下部分介绍了不同的冗余选项之间的差异：

### <a name="locally-redundant-storage"></a>本地冗余存储

[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="zone-redundant-storage"></a>区域冗余存储

[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="geo-redundant-storage"></a>地域冗余存储

> [!Warning]  
> 如果在 GRS 存储帐户中使用 Azure 文件共享作为云终结点，则不应启动存储帐户故障转移。 否则，将会导致同步停止，并且可能还会在有新分层文件的情况下导致意外数据丢失。 对于 Azure 区域丢失，Microsoft 会以与 Azure 文件同步兼容的方式触发存储帐户故障转移。

异地冗余存储 (GRS) 通过将数据复制到距主要区域数百英里以外的次要区域，用于在给定的一年内至少为对象提供 99.99999999999999%（16 个 9）的持久性。 如果存储帐户启用了 GRS，则即使遇到区域完全停电或导致主区域不可恢复的灾难，数据也能持久保存。

If you opt for read-access geo-redundant storage (RA-GRS), you should know that Azure File does not support read-access geo-redundant storage (RA-GRS) in any region at this time. File shares in the RA-GRS storage account work like they would in GRS accounts and are charged GRS prices.

GRS 将数据复制到次要区域中的另一个数据中心，但仅当 Microsoft 发起了从主要区域到次要区域的故障转移时，才可读取这些数据。

For a storage account with GRS enabled, all data is first replicated with locally redundant storage (LRS). 首先将更新提交到主要位置，并使用 LRS 复制更新。 然后，使用 GRS 以异步方式将更新复制到次要区域。 将数据写入次要位置后，还会使用 LRS 在该位置复制数据。

主要和次要区域在一个存储缩放单元内管理跨单独的容错域和升级域管理副本。 存储缩放单元是数据中心内的基本复制单元。 此级别的复制由 LRS 提供；有关详细信息，请参阅[本地冗余存储 (LRS)：Azure 存储的低成本数据冗余](../common/storage-redundancy-lrs.md)。

确定要使用哪个复制选项时，请记住以下几点：

* Geo-zone-redundant storage (GZRS) (preview) provides high availability together with maximum durability by replicating data synchronously across three Azure availability zones and then replicating data asynchronously to the secondary region. You can also enable read access to the secondary region. GZRS is designed to provide at least 99.99999999999999% (16 9's) durability of objects over a given year. For more information on GZRS, see [Geo-zone-redundant storage for highly availability and maximum durability (preview)](../common/storage-redundancy-gzrs.md).
* Zone-redundant storage (ZRS) provides highly availability with synchronous replication and may be a better choice for some scenarios than GRS. 有关 ZRS 的详细信息，请参阅 [ZRS](../common/storage-redundancy-zrs.md)。
* 对于异步复制，从数据写入到主要区域到数据复制到次要区域，这之间存在延迟。 发生区域性灾难时，如果无法从主要区域中恢复数据，则尚未复制到次要区域的更改可能会丢失。
* 使用 GRS 时，副本不可用于读取或写入访问，除非 Microsoft 启动到次要区域的故障转移。 如果发生故障转移，则在故障转移完成后，你将具有对该数据的读取和写入访问权限。 有关详细信息，请参阅[灾难恢复指南](../common/storage-disaster-recovery-guidance.md)。

## <a name="onboard-to-larger-file-shares-standard-tier"></a>Onboard to larger file shares (standard tier)

This section only applies to the standard file shares. All premium file shares are available with 100 TiB capacity.

### <a name="restrictions"></a>限制

- LRS/ZRS to GRS/GZRS account conversion will not be possible for any storage account with large file shares enabled.

### <a name="regional-availability"></a>区域可用性

Standard file shares are available in all regions up to 5 TiB. In certain regions, they are available with a 100 TiB limit, those regions are listed in the following table:

|地区 |Supported redundancy |
|-------|---------|
|澳大利亚东部 |LRS     |
|澳大利亚东南部|LRS |
|加拿大中部  |LRS     |
|加拿大东部     |LRS     |
|印度中部  |LRS     |
|Central US*   |LRS     |
|亚洲东部      |LRS     |
|East US*        |LRS     |
|East US 2*      |LRS     |
|法国中部 |LRS, ZRS|
|法国南部   |LRS     |
|北欧   |LRS     |
|印度南部    |LRS     |
|亚洲东南部 |LRS, ZRS|
|美国中西部|LRS     |
|West Europe*    |LRS, ZRS|
|West US*        |LRS     |
|美国西部 2      |LRS, ZRS|

\* Supported for new accounts, not all existing accounts have completed the upgrade process. You can check if your existing storage accounts have completed the upgrade process by attempting to [Enable large file shares](storage-files-how-to-create-large-file-share.md).

To help us prioritize new regions and features, please fill out this [survey](https://aka.ms/azurefilesatscalesurvey).

### <a name="enable-and-create-larger-file-shares"></a>Enable and create larger file shares

To begin using larger file shares, see our article [How to enable and create large file shares](storage-files-how-to-create-large-file-share.md).

## <a name="data-growth-pattern"></a>数据增长模式

Today, the maximum size for an Azure file share is 100 TiB. 鉴于此当前限制，必须考虑部署 Azure 文件共享时的预期数据增长。

It is possible to sync multiple Azure file shares to a single Windows File Server with Azure File Sync. This allows you to ensure that older, large file shares that you may have on-premises can be brought into Azure File Sync. For more information, see [Planning for an Azure File Sync Deployment](storage-files-planning.md).

## <a name="data-transfer-method"></a>数据传输方法

可通过多种简单的选项将数据从现有文件共享（例如本地文件共享）批量传输到 Azure 文件。 几种常用选项包括（非详尽列表）：

* Azure 文件同步：作为 Azure 文件共享（“云终结点”）和 Windows 目录命名空间（“服务器终结点”）之间首个同步的一部分，Azure 文件同步可将现有文件共享中的所有数据复制到 Azure 文件。
* [Azure 导入/导出](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)：使用 Azure 导入/导出服务，可将硬盘驱动器寄送到 Azure 数据中心，从而安全地将大量数据传输到 Azure 文件共享。 
* [Robocopy](https://technet.microsoft.com/library/cc733145.aspx)：Robocopy 是 Windows 和 Windows Server 自带的一款知名复制工具。 Robocopy 可用于将数据传输到 Azure 文件，方法是在本地装载文件共享，然后使用装载位置作为 Robocopy 命令的目标位置。
* [AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)：AzCopy 是一个命令行实用工具，专用于使用具有优化性能的简单命令在 Azure 文件和 Azure Blob 存储中复制/粘贴数据。

## <a name="next-steps"></a>后续步骤
* [规划 Azure 文件同步部署](storage-sync-files-planning.md)
* [部署 Azure 文件](storage-files-deployment-guide.md)
* [部署 Azure 文件同步](storage-sync-files-deployment-guide.md)
