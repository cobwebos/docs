---
title: 将大型机存储移到 Azure 存储
description: 可大规模缩放的 Azure 存储资源可帮助基于大型机的组织迁移和现代化 IBM z14 应用程序。
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: storage
ms.openlocfilehash: 86419811cdf2c11204caae0ca5bf6f65fba063d2
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2020
ms.locfileid: "76288908"
---
# <a name="move-mainframe-storage-to-azure"></a>将大型机存储移到 Azure

若要在 Microsoft Azure 上运行大型机工作负荷，需要知道大型机的功能与 Azure 的比较情况。 可大规模缩放的存储资源可帮助组织开始实现现代化，而不会放弃所依赖的应用程序。

Azure 提供类似于大型机的功能和存储容量，相当于基于 IBM z14 的系统（在撰写本文时最新的模型）。 本文介绍如何在 Azure 上获得可比较的结果。

## <a name="mainframe-storage-at-a-glance"></a>大型机存储概览

IBM 大型机的存储方式有两种。 第一种是直接访问存储设备（DASD）。 第二个是顺序存储。 对于管理存储，大型机提供了数据设施存储管理子系统（DFSMS）。 它管理对各种存储设备的数据访问。

[DASD](https://en.wikipedia.org/wiki/Direct-access_storage_device)指的是辅助（而非内存中）存储的单独设备，该设备允许使用唯一地址直接访问数据。 最初，术语 "DASD" 应用于旋转磁盘、磁鼓或数据单元。 但是，这一术语现在也适用于固态存储设备（Ssd）、存储区域网络（San）、网络连接存储（NAS）和光驱。 出于本文档的目的，DASD 指的是旋转磁盘、San 和 Ssd。

与 DASD 存储不同的是，大型机上的顺序存储是指磁带驱动器，如磁带驱动器，其中的数据是从起始点访问的，然后读取或写入一行。

通常使用光纤连接（FICON）连接存储设备，或使用[HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm)直接在大型机的 IO 总线上访问存储设备，这是一种 IBM 技术，用于服务器上的分区与虚拟机监控程序之间的高速通信。

大多数大型机系统将存储分为两种类型：

- 每日操作都需要*联机存储*（也称为热存储）。 DASD 存储通常用于此目的。 但是，有序存储（例如每日磁带备份（逻辑或物理））也可用于实现此目的。

- 不保证在给定时间装入*存档存储*（也称为冷存储）。 相反，它会根据需要进行装载和访问。 存档存储通常是使用顺序磁带备份（逻辑或物理）来实现的。

## <a name="mainframe-versus-io-latency-and-iops"></a>大型机与 IO 延迟和 IOPS

大型机通常用于需要高性能 IO 和低 IO 延迟的应用程序。 他们可以使用连接到 IO 设备和 HiperSockets 的 FICON 连接来执行此操作。 当使用 HiperSockets 将应用程序和设备直接连接到大型机的 IO 通道时，可以实现以微秒为单位的延迟。

## <a name="azure-storage-at-a-glance"></a>Azure 存储概览

适用于存储的 Azure 基础结构即服务（[IaaS](https://azure.microsoft.com/overview/what-is-iaas/)）选项提供了类似的大型机容量。

Microsoft 为 Azure 中托管的应用程序提供了 pb 数量的存储空间，并提供了多个存储选项。 这些范围包括从 SSD 存储到高性能，以及用于大容量存储和存档的低成本 blob 存储。 此外，Azure 还为存储提供了一个数据冗余选项，这需要在大型机环境中进行更多的工作。

Azure 存储可用作[Azure 磁盘](/azure/virtual-machines/windows/managed-disks-overview)、 [Azure 文件](/azure/storage/files/storage-files-introduction)和[azure blob](/azure/storage/blobs/storage-blobs-overview) ，如下表所示。 详细了解[何时使用每个](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)。

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>类型</th><th>Description</th><th>在下列情况下使用：</th></tr>
</thead>
<tbody>
<tr><td>Azure 文件
</td>
<td>
提供 SMB 接口、客户端库和允许从任意位置访问存储文件的<a href="https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api">REST</a>接口。
</td>
<td><ul>
<li>当应用程序使用本机文件系统 Api 在 Azure 中运行的应用程序与其他应用程序之间共享数据时，将应用程序直接迁移到云。</li>
<li>存储需要从多个虚拟机访问的开发和调试工具。</li>
</ul>
</td>
</tr>
<tr><td>Azure Blob
</td>
<td>提供客户端库和<a href="https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api">REST</a>接口，该接口允许在块 blob 中大规模存储和访问非结构化数据。 还支持 <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2</a>，用于企业大数据分析解决方案。
</td>
<td><ul>
<li>在应用程序中支持流式处理和随机访问方案。</li>
<li>可以从任何位置访问应用程序数据。</li>
<li>在 Azure 上构建企业数据 lake 并执行大数据分析。</li>
</ul></td>
</tr>
<tr><td>Azure 磁盘
</td>
<td>提供客户端库和<a href="https://docs.microsoft.com/rest/api/compute/disks">REST</a>接口，该接口允许从附加的虚拟硬盘持久存储和访问数据。
</td>
<td><ul>
<li>使用本机文件系统 Api 的直接移动应用程序将数据读写到永久性磁盘。</li>
<li>存储不需要从磁盘附加到的 VM 外部访问的数据。</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Azure 热（联机）和冷（存档）存储

给定系统的存储类型取决于系统的要求，其中包括存储大小、吞吐量和 IOPS。 对于大型机上的 DASD 类型存储，Azure 上的应用程序通常使用 Azure 磁盘驱动器存储。 对于大型机存档存储，请在 Azure 上使用 blob 存储。

Ssd 在 Azure 上提供最高的存储性能。 提供以下选项（在撰写本文档时）：

| 类型         | 大小           | IOPS                  |
|--------------|----------------|-----------------------|
| 超级 SSD    | 4 GB 到 64 TB  | 1200到 160000 IOPS |
| 高级·SSD  | 32 GB 到 32 TB | 12到 15000 IOPS     |
| 标准 SSD | 32 GB 到 32 TB | 12到 2000 IOPS      |

Blob 存储提供了 Azure 上最大的存储量。 除了存储大小以外，Azure 还提供托管和非托管存储。 使用托管存储，Azure 负责管理底层存储帐户。 使用非托管存储时，用户需要负责设置适当大小的 Azure 存储帐户，以满足存储要求。

## <a name="next-steps"></a>后续步骤

- [大型机迁移](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Azure 虚拟机上的大型机重新承载](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [将大型机计算移到 Azure](mainframe-compute-Azure.md)
- [确定何时使用 Azure Blob、Azure 文件或 Azure 磁盘](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)
- [标准 SSD Azure VM 工作负荷的托管磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)

### <a name="ibm-resources"></a>IBM 资源

- [IBM Z 的并行 Sysplex](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS 和耦合设施：除基础知识之外](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Creating required users for a Db2 pureScale Feature installation](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)（创建所需用户以安装 Db2 pureScale 功能）
- [Db2icrt - Create instance command](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)（Db2icrt - 创建实例命令）
- [Db2 pureScale 群集数据库解决方案](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [适用于大型机应用程序的 Microsoft Azure 政府云](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft 和 FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>更多迁移资源

- [Azure Virtual Data Center Lift and Shift Guide](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)（Azure 虚拟数据中心提升和迁移指南）
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
