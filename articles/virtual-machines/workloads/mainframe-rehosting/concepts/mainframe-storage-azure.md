---
title: 将大型机存储移动到 Azure 存储
description: 可大规模扩展的 Azure 存储资源可帮助基于大型机的组织迁移 IBM z14 应用程序并实现现代化。
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: storage
ms.openlocfilehash: 86419811cdf2c11204caae0ca5bf6f65fba063d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76288908"
---
# <a name="move-mainframe-storage-to-azure"></a>将大型机存储移动到 Azure

要在 Microsoft Azure 上运行大型机工作负荷，您需要了解大型机的功能与 Azure 的比较情况。 大规模扩展的存储资源可以帮助组织开始现代化，而不会放弃他们依赖的应用程序。

Azure 提供类似于大型机的功能和存储容量，可与基于 IBM z14 的系统（本文编写时最新的模型）相媲美。 本文介绍如何在 Azure 上获取可比较的结果。

## <a name="mainframe-storage-at-a-glance"></a>大型机存储一览

IBM 大型机以两种方式描述存储。 第一个是直接访问存储设备 （DASD）。 第二个是顺序存储。 对于管理存储，大型机提供数据设施存储管理子系统 （DFSMS）。 它管理对各种存储设备的数据访问。

[DASD](https://en.wikipedia.org/wiki/Direct-access_storage_device)是指用于辅助（非内存中）存储的单独设备，允许使用唯一地址直接访问数据。 最初，术语 DASD 应用于旋转磁盘、磁桶或数据单元。 但是，现在该术语也适用于固态存储设备 （SSD）、存储区域网络 （SAN）、网络连接存储 （NAS） 和光驱。 就本文档而言，DASD 是指旋转磁盘、SAN 和 SSD。

与 DASD 存储不同，大型机上的顺序存储是指从起点访问数据，然后以行读取或写入的设备（如磁带驱动器）。

存储设备通常使用光纤连接 （FICON） 连接，或者使用[HiperSocket](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm)直接访问主机的 IO 总线，这是一种 IBM 技术，用于使用虚拟机管理程序在服务器上的分区之间进行高速通信。

大多数大型机系统将存储分为两种类型：

- *日常操作需要在线存储*（也称为热存储）。 DASD 存储通常用于此目的。 但是，顺序存储（如每日磁带备份（逻辑备份或物理备份）也可用于此目的。

- *不能保证存档存储*（也称为冷存储）在给定时间安装。 相反，它会根据需要装载和访问。 存档存储通常使用顺序磁带备份（逻辑备份或物理备份）实现存储。

## <a name="mainframe-versus-io-latency-and-iops"></a>大型机与 IO 延迟和 IOPS

大型机通常用于需要高性能 IO 和低 IO 延迟的应用程序。 他们可以使用与 IO 设备和 HiperSocket 的 FICON 连接执行此操作。 当 HiperSockets 用于将应用程序和设备直接连接到大型机的 IO 通道时，可以达到微秒的延迟。

## <a name="azure-storage-at-a-glance"></a>Azure 存储一览

用于存储的 Azure 基础结构即服务 （[IaaS](https://azure.microsoft.com/overview/what-is-iaas/)） 选项提供了可比较的大型机容量。

Microsoft 为 Azure 中托管的应用程序提供了价值数 PB 的存储，并且有多个存储选项。 这些范围从用于高性能的 SSD 存储到用于大容量存储和归档的低成本 Blob 存储。 此外，Azure 还为存储提供了数据冗余选项，在大型机环境中需要付出更多努力。

如下表摘要，Azure 存储可作为[Azure 磁盘](/azure/virtual-machines/windows/managed-disks-overview)[、Azure 文件和](/azure/storage/files/storage-files-introduction)Azure [Blob](/azure/storage/blobs/storage-blobs-overview)提供。 详细了解[何时使用每个](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)。

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>类型</th><th>说明</th><th>使用时，你想：</th></tr>
</thead>
<tbody>
<tr><td>Azure 文件
</td>
<td>
提供 SMB 接口、客户端库和<a href="https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api">REST</a>接口，允许从任何位置访问存储的文件。
</td>
<td><ul>
<li>当应用程序使用本机文件系统 API 在应用程序与 Azure 中运行的其他应用程序之间共享数据时，将应用程序提升到云。</li>
<li>存储需要从许多 VM 访问的开发和调试工具。</li>
</ul>
</td>
</tr>
<tr><td>Azure Blob
</td>
<td>提供客户端库和<a href="https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api">REST</a>接口，允许在块 blob 中大规模存储和访问非结构化数据。 还支持 <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2</a>，用于企业大数据分析解决方案。
</td>
<td><ul>
<li>支持应用程序中的流式处理和随机访问方案。</li>
<li>可随时随地访问应用程序数据。</li>
<li>在 Azure 上构建企业数据湖并执行大数据分析。</li>
</ul></td>
</tr>
<tr><td>Azure 磁盘
</td>
<td>提供客户端库和<a href="https://docs.microsoft.com/rest/api/compute/disks">REST</a>接口，允许持续存储数据并从附加的虚拟硬盘访问数据。
</td>
<td><ul>
<li>提升和转移使用本机文件系统 API 以将数据读取和写入持久磁盘的应用程序。</li>
<li>存储不需要从磁盘附加到的 VM 外部访问的数据。</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Azure 热（联机）和冷（存档）存储

给定系统的存储类型取决于系统的要求，包括存储大小、吞吐量和 IOPS。 对于大型机上的 DASD 类型存储，Azure 上的应用程序通常使用 Azure 磁盘驱动器存储。 对于大型机存档存储，Blob 存储在 Azure 上使用。

SSD 在 Azure 上提供最高的存储性能。 以下选项可用（截至本文档编写）：

| 类型         | 大小           | IOPS                  |
|--------------|----------------|-----------------------|
| 超级 SSD    | 4 GB 到 64 TB  | 1，200 到 160，000 IOPS |
| 高级 SSD  | 32 GB 到 32 TB | 12 到 15，000 IOPS     |
| 标准 SSD | 32 GB 到 32 TB | 12 到 2，000 IOPS      |

Blob 存储在 Azure 上提供最大的存储量。 除了存储大小外，Azure 还提供托管和非托管存储。 使用托管存储，Azure 负责管理基础存储帐户。 使用非托管存储时，用户负责设置适当大小的 Azure 存储帐户以满足存储要求。

## <a name="next-steps"></a>后续步骤

- [大型机迁移](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [大型机在 Azure 虚拟机上重新托管](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [将大型机计算移动到 Azure](mainframe-compute-Azure.md)
- [确定何时使用 Azure Blob、Azure 文件或 Azure 磁盘](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)
- [适用于 Azure VM 工作负荷的标准 SSD 托管磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)

### <a name="ibm-resources"></a>IBM 资源

- [IBM Z 上的并行系统](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS 和耦合设施：超越基础知识](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Creating required users for a Db2 pureScale Feature installation](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)（创建所需用户以安装 Db2 pureScale 功能）
- [Db2icrt - Create instance command](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)（Db2icrt - 创建实例命令）
- [Db2 纯标群集数据库解决方案](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government 

- [适用于大型机应用程序的 Microsoft Azure 政府云](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [微软和FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>更多迁移资源

- [Azure Virtual Data Center Lift and Shift Guide](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)（Azure 虚拟数据中心提升和迁移指南）
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
