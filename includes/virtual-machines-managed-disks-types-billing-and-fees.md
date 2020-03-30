---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 2303d36e93cecfca03894a8b0e55458c03b13d78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "73412944"
---
**出站数据传输**：[出站数据传输](https://azure.microsoft.com/pricing/details/bandwidth/)（Azure 数据中心送出的数据）会产生带宽使用费。

**事务**：根据在标准托管磁盘上执行的事务数计费。 对于标准 SSD，每个小于或等于 256 KiB 吞吐量的 I/O 操作被视为单个 I/O 操作。 大于 256 KiB 吞吐量的 I/O 操作被视为大小为 256 KiB 的多个 I/O。 对于标准 HDD，每个 IO 操作会被视为单个事务，无论 I/O 大小如何。

有关托管磁盘定价的详细信息（包括事务成本），请参阅[托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks)。

### <a name="ultra-disk-vm-reservation-fee"></a>超磁盘 VM 预订费

Azure VM 能够指示它们是否与超级磁盘兼容。 与超级磁盘兼容的 VM 在计算 VM 实例与块存储缩放单元之间分配专用的带宽容量，以优化性能并降低延迟。 在 VM 上添加此功能会导致预留费用，不过，这笔费用是仅当在 VM 上启用了超级磁盘功能但未将超级磁盘附加到 VM 时才产生的。 如果将超级磁盘附加到与超级磁盘兼容的 VM，则不会收取此费用。 此费用根据 VM 上预配的每个 vCPU 计收。 

> [!Note]
> 对于[受限的核心 VM 大小](../articles/virtual-machines/linux/constrained-vcpu.md)，保留费用基于实际 vCPU 数，而不是受限内核。 对于Standard_E328s_v3，预订费将基于32个核心。 

有关超级磁盘定价的详细信息，请参阅[Azure 磁盘定价页](https://azure.microsoft.com/pricing/details/managed-disks/)。

### <a name="azure-disk-reservation"></a>Azure 磁盘预留

磁盘预留是提前购买一年的磁盘存储以折扣的选项，从而降低了您的总成本。 购买磁盘预留时，您可以在目标区域中选择特定的磁盘 SKU，例如，在美国东部 2 区域选择 10 P30 （1TiB） 高级 SSD，期限为一年。 预留体验类似于预留虚拟机 （VM） 实例。 您可以捆绑 VM 和磁盘预留，以最大限度地节省成本。 目前，Azure 磁盘预留为所有生产区域的优质 SSD SKU 提供了一年承诺计划，从 P30 （1TiB） 到 P80 （32 TiB）。 有关预留磁盘定价的更多详细信息，请参阅 Azure[磁盘定价页](https://azure.microsoft.com/pricing/details/managed-disks/)。