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
ms.openlocfilehash: 4b5d2de2e9ccd44517e083a435e127bd5678f002
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564336"
---
# <a name="ultra-disks-preview-managed-disks-for-azure-virtual-machine-workloads"></a>超高磁盘 （预览） 托管的 Azure 虚拟机工作负荷的磁盘

超高的 azure 磁盘 （预览版） 提供高吞吐量、 高 IOPS 和一致的低延迟磁盘存储为 Azure IaaS Vm。 此新产品提供出类拔萃的性能，其可用性级别与我们的现有磁盘产品相同。 超高的磁盘的其他好处包括能够动态地更改而无需重新启动虚拟机工作负荷以及磁盘的性能。 超高磁盘适合数据密集型工作负荷，例如 SAP HANA、 顶层数据库和事务密集型工作负荷。

## <a name="ultra-disk-features"></a>超高磁盘功能

托管磁盘：超高的磁盘才可作为托管磁盘。 超高的磁盘不能部署为非托管的磁盘或页 blob。 在创建托管的磁盘时，指定磁盘 sku 类型为 UltraSSD_LRS 并指示磁盘，IOPS 的大小和所需的吞吐量和 Azure 创建和管理磁盘。  

**虚拟机**：超高磁盘用于处理所有高级 SSD 启用了的 Azure 虚拟机 Sku;但是，因为它目前处于预览状态，Vm 的大小都为 ES/DS v3。

**动态性能配置**：超高的磁盘，可动态更改而无需重新启动虚拟机的工作负荷需求以及磁盘的性能 （IOPS 和吞吐量）。

## <a name="scalability-and-performance-targets"></a>可伸缩性和性能目标

在设置超高磁盘时，将可以选择单独配置容量和磁盘的性能。 超高磁盘从最多 64 TiB 4 GiB 有多个固定大小和功能，可单独配置的 IOPS 和吞吐量的灵活的性能配置模型。 超高磁盘仅可用作数据磁盘。 我们建议将高级 SSD 用作 OS 磁盘。

超高的磁盘的一些主要功能包括：

- 磁盘容量：超高磁盘提供最多 64 TiB 4 GiB 从一系列不同的磁盘大小。
- 磁盘 IOPS：超高的磁盘支持 300 IOPS/GiB，最多 160 K IOPS 的每个磁盘的 IOPS 的限制。 若要实现预配的 IOPS，请确保选择的磁盘 IOPS 小于 VM IOPS。 最小磁盘 IOPS 为 100 IOPS。
- 磁盘吞吐量：使用超高磁盘时，单个磁盘的吞吐量限制是 256 KiB/秒，对于每个预配 IOPS 的最大为每个磁盘的 2000 MBps (其中 MBps = 10 ^6 字节 / 秒)。 最小磁盘吞吐量为 1 MiB。

下表汇总了不同磁盘大小支持的不同配置：  

### <a name="ultra-disks-managed-disk-offerings"></a>超高磁盘托管磁盘产品/服务

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

使用超高磁盘时，将应用以下计费事项：

- 托管磁盘大小
- 托管磁盘的预配 IOPS
- 托管磁盘的预配吞吐量
- 超高磁盘 VM 预订费用

### <a name="managed-disk-size"></a>托管磁盘大小

当预配新的 Azure VM 时，托管磁盘根据你选择的 VM 大小进行计费。 Azure 将预配大小映射（向上舍入）到最接近的磁盘大小。 有关提供的磁盘大小的详细信息，请参阅前面的“可伸缩性和性能目标”部分的表格。 每个磁盘映射到一种受支持的预配大小并相应地按小时计费。 例如，如果预配 200 GiB 超高磁盘和 20 个小时后删除它，它将映射到 256 GiB 的磁盘大小产品/服务，则要支付 256 GiB 为 20 个小时。 此计费基于计算时数消耗，与实际写入到磁盘的数据量无关。

### <a name="managed-disk-provisioned-iops"></a>预配 IOPS 的托管的磁盘

IOPS 是指应用程序每秒发送到磁盘的请求数。 输入/输出操作可以是有序或随机的读取或写入。 根据附加到 VM 的磁盘大小或磁盘数，每小时根据平均 IOPS 数进行计费。 有关提供的磁盘 IOPS 的详细信息，请参阅前面的“可伸缩性和性能目标”部分的表格。

### <a name="managed-disk-provisioned-throughput"></a>托管磁盘预配的吞吐量

吞吐量是指应用程序在指定时间间隔内发送到磁盘的数据量，计量单位为字节/秒。 如果应用程序正在执行大规模的输入/输出操作，则需要高吞吐量。  

吞吐量和 IOPS 之间存在一个关系，如以下公式所示：IOPS x IO 大小 = 吞吐量

因此，必须确定应用程序所需的最佳吞吐量和 IOPS 值。 尝试优化其中一个值时，另一个值也会受影响。 我们建议从对应于 16 KiB IO 大小的吞吐量着手，并在需要更高吞吐量时进行调整。

超高的磁盘上的受支持的磁盘吞吐量的详细信息，请参阅上述的可伸缩性和性能目标部分中的表。 与磁盘大小和 IOPS 一样，预配吞吐量根据预配的 MBps 按小时计费。

### <a name="ultra-disk-vm-reservation-fee"></a>超高磁盘 VM 预订费用

我们引入了指示 VM 兼容的超高磁盘的 VM 上的功能。 与超级磁盘兼容的 VM 在计算 VM 实例与块存储缩放单元之间分配专用的带宽容量，以优化性能并降低延迟。 在 VM 上添加此功能会导致预留费用，不过，这笔费用是仅当在 VM 上启用了超级磁盘功能但未将超级磁盘附加到 VM 时才产生的。 当超高磁盘附加到超高兼容的 VM 时，不会收取此费用。 此费用根据 VM 上预配的每个 vCPU 计收。

请参阅[定价页的 Azure 磁盘](https://azure.microsoft.com/pricing/details/managed-disks/)为新超高磁盘价格的详细信息在有限预览版中可用。

### <a name="ultra-disk-preview-scope-and-limitations"></a>超高磁盘预览作用域和限制

适用于预览版，超高的磁盘：

- 最初在美国东部 2 区的单个可用性区域中受支持  
- 仅可用于可用性区域 （可用性集和单个 VM 部署外部区域将不能将附加的超高磁盘）
- 仅在 ES/DS v3 VM 上受支持
- 只可用作数据磁盘，并且仅支持 4k 物理扇区大小  
- 只能以空磁盘的形式创建  
- 目前只能使用资源管理器模板、CLI 和 Python SDK 进行部署。
- （目前）不支持磁盘快照、VM 映像、可用性集、虚拟机规模集和 Azure 磁盘加密。
- （目前）不支持与 Azure 备份或 Azure Site Recovery 集成。
- 与 [大多数预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)一样，在正式版 (GA) 发布之前，不应将此功能用于生产工作负荷。
