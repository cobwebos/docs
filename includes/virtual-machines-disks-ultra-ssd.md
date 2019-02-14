---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 212506667a56befb4e3926dec7a9e3eb9772ebed
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2019
ms.locfileid: "55736252"
---
# <a name="ultra-ssd-preview-managed-disks-for-azure-virtual-machine-workloads"></a>Azure 虚拟机工作负荷的超级 SSD（预览版）托管磁盘

Azure 超级 SSD（预览版）为 Azure IaaS VM 提供高吞吐量、高 IOPS 和一贯低延迟的磁盘存储。 此新产品提供出类拔萃的性能，其可用性级别与我们的现有磁盘产品相同。 超级 SSD 的其他优势包括能够动态改变工作负荷以及磁盘的性能，而无需重启虚拟机。 超级 SSD 适用于 SAP HANA、顶层数据库等数据密集型工作负荷，以及事务密集型工作负荷。

## <a name="ultra-ssd-features"></a>超级 SSD 的功能

**托管磁盘**：超级 SSD 只可用作托管磁盘。 超级 SSD 无法部署为非托管磁盘或页 Blob。 创建托管磁盘时，请指定 UltraSSD_LRS 作为磁盘 SKU，并指明所需的磁盘大小、IOPS 和吞吐量，然后，Azure 将自动创建并管理该磁盘。  

**虚拟机**：超级 SSD 旨在与启用了高级 SSD 的所有 Azure 虚拟机 SKU 配合使用；但是，因为它当前为预览版，所以 VM 大小设定为 ES/DS v3。

**动态性能配置**：超级 SSD 允许动态改变磁盘的性能（IOPS 和吞吐量）以及工作负荷的需求，而无需重启虚拟机。

## <a name="scalability-and-performance-targets"></a>可伸缩性和性能目标

预配超级 SSD 时，可以使用相应的选项来单独配置磁盘的容量和性能。 超级 SSD 提供从 4 GiB 到 64 TiB 的多种固定大小，并提供一个灵活的性能配置模型用于单独配置 IOPS 和吞吐量。 超级 SSD 只可用作数据磁盘。 我们建议将高级 SSD 用作 OS 磁盘。

超级 SSD 的一些重要功能包括：

- 磁盘容量：超级 SSD 提供从 4 GiB 到 64 TiB 的不同磁盘大小。
- 磁盘 IOPS：超级 SSD 支持 300 IOPS/GiB 的 IOPS 限制，每个磁盘最高可达 160 K IOPS。 若要实现预配的 IOPS，请确保选择的磁盘 IOPS 小于 VM IOPS。 最小磁盘 IOPS 为 100 IOPS。
- 磁盘吞吐量：使用超级 SSD 时，单个磁盘对应于每个预配 IOPS 的吞吐量限制为 256 KiB/秒，每个磁盘的最大吞吐量为 2000 MBps（其中，MBps = 每秒 10^6 字节）。 最小磁盘吞吐量为 1 MiB。

下表汇总了不同磁盘大小支持的不同配置：  

### <a name="ultra-ssd-managed-disk-offerings"></a>超级 SSD 托管磁盘产品

|磁盘大小 (GiB)  |IOPS 上限  |吞吐量上限 (MBps)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9,600         |2,000         |
|64     |19,200         |2,000         |
|128     |38,400         |2,000         |
|256     |76,800         |2,000         |
|512     |80,000         |2,000         |
|1,024 - 65,536（此范围内的大小以 1 TiB 为增量递增）     |160,000         |2,000         |

## <a name="pricing-and-billing"></a>定价和计费

使用超级 SSD 时，请注意以下计费注意事项：

- 托管磁盘大小
- 托管磁盘的预配 IOPS
- 托管磁盘的预配吞吐量
- 超级 SSD VM 预留费用

### <a name="managed-disk-size"></a>托管磁盘大小

当预配新的 Azure VM 时，托管磁盘根据你选择的 VM 大小进行计费。 Azure 将预配大小映射（向上舍入）到最接近的磁盘大小。 有关提供的磁盘大小的详细信息，请参阅前面的“可伸缩性和性能目标”部分的表格。 每个磁盘映射到一种受支持的预配大小并相应地按小时计费。 例如，如果预配了 200 GiB 的超级 SSD 磁盘，并在 20 小时后将其删除，则它会映射到 256 GiB 的磁盘大小，并且将按 256 GiB 大小收取 20 个小时的费用。 此计费基于计算时数消耗，与实际写入到磁盘的数据量无关。

### <a name="managed-disk-provisioned-iops"></a>托管磁盘的预配 IOPS

IOPS 是指应用程序每秒发送到磁盘的请求数。 输入/输出操作可以是有序或随机的读取或写入。 根据附加到 VM 的磁盘大小或磁盘数，每小时根据平均 IOPS 数进行计费。 有关提供的磁盘 IOPS 的详细信息，请参阅前面的“可伸缩性和性能目标”部分的表格。

### <a name="managed-disk-provisioned-throughput"></a>托管磁盘的预配吞吐量

吞吐量是指应用程序在指定时间间隔内发送到磁盘的数据量，计量单位为字节/秒。 如果应用程序正在执行大规模的输入/输出操作，则需要高吞吐量。  

吞吐量和 IOPS 之间存在一个关系，如以下公式所示：IOPS x IO 大小 = 吞吐量

因此，必须确定应用程序所需的最佳吞吐量和 IOPS 值。 尝试优化其中一个值时，另一个值也会受影响。 我们建议从对应于 16 KiB IO 大小的吞吐量着手，并在需要更高吞吐量时进行调整。

有关超级 SSD 支持的磁盘吞吐量的详细信息，请参阅前面的“可伸缩性和性能目标”部分中的表格。 与磁盘大小和 IOPS 一样，预配吞吐量根据预配的 MBps 按小时计费。

### <a name="ultra-ssd-vm-reservation-fee"></a>超级 SSD VM 预留费用

我们即将在 VM 上引入一项功能，用于指示 VM 与超级 SSD 兼容。 与超级 SSD 兼容的 VM 在计算 VM 实例与块存储缩放单元之间分配专用的带宽容量，以优化性能并降低延迟。 在 VM 上添加此功能会导致预留费用，不过，这笔费用是仅当在 VM 上启用了超级 SSD 功能但未将超级 SSD 磁盘附加到 VM 时才产生的。 如果将超级 SSD 磁盘附加到与超级 SSD 兼容的 VM，则不会收取此费用。 此费用根据 VM 上预配的每个 vCPU 计收。

请参阅 [Azure 磁盘定价页](https://azure.microsoft.com/pricing/details/managed-disks/)，了解受限预览版中可用的新超级 SSD 磁盘价格详细信息。

### <a name="ultra-ssd-preview-scope-and-limitations"></a>超级 SSD 预览版的范围和限制

在预览期，超级 SSD 磁盘：

- 最初在美国东部 2 区的单个可用性区域中受支持  
- 只能在可用性区域中使用（区域外部的可用性集和单一 VM 部署无法附加超级 SSD 磁盘）
- 仅在 ES/DS v3 VM 上受支持
- 只可用作数据磁盘，并且仅支持 4k 物理扇区大小  
- 只能以空磁盘的形式创建  
- 目前只能使用资源管理器模板、CLI 和 Python SDK 进行部署。
- （目前）不支持磁盘快照、VM 映像、可用性集、虚拟机规模集和 Azure 磁盘加密。
- （目前）不支持与 Azure 备份或 Azure Site Recovery 集成。
- 与 [大多数预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)一样，在正式版 (GA) 发布之前，不应将此功能用于生产工作负荷。
