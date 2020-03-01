---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e145cf481dd165144b188e6205e4b78cc61359fd
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202547"
---
## <a name="premium-ssd"></a>高级·SSD

Azure 高级 SSD 为运行输入/输出 (IO) 密集型工作负荷的虚拟机 (VM) 提供高性能、低延迟的磁盘支持。 若要利用高级存储磁盘的速度和性能优势，可将现有的 VM 磁盘迁移到高级 SSD。 高级 SSD 适用于任务关键型生产应用程序。 高级 Ssd 只能与与高级存储兼容的 VM 系列一起使用。

若要详细了解 Azure 中适用于 Windows 的 VM 类型和大小，包括与高级存储兼容的大小，请参阅[WINDOWS VM 大小](../articles/virtual-machines/windows/sizes.md)。 若要详细了解 Azure 中适用于 Linux 的 VM 类型和大小，包括与高级存储兼容的大小，请参阅[LINUX VM 大小](../articles/virtual-machines/linux/sizes.md)。 在这些文章中，需要检查每个 VM 大小的文章，以确定其是否与高级存储兼容。

### <a name="disk-size"></a>磁盘大小
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

预配高级存储磁盘时，可以获得该磁盘的容量、IOPS 和吞吐量保证，这与标准存储不同。 例如，如果创建 P50 磁盘，Azure 将为此磁盘预配 4,095-GB 存储容量、7,500 IOPS 和 250-MB/秒的吞吐量。 应用程序可以使用全部或部分容量与性能。 高级 SSD 磁盘旨在提供低的一位数毫秒的延迟，以及上表99.9% 时间所述的目标 IOPS 和吞吐量。

## <a name="bursting-preview"></a>突发（预览）

小于 P30 的高级 SSD 大小现在提供磁盘突发（预览版），并可以将每个磁盘的 IOPS 突发为3500，并将其带宽提高到 170 Mbps。 突发是自动进行的，并基于信用系统运行。 当磁盘流量低于预配的性能目标时，信用额度会自动累计，而当流量超出目标值时，将自动消耗信用，直到达到最大突发限制。 最大猝发限制定义了磁盘 IOPS 的上限 & 带宽，即使你有要从其消耗的突发信用额度也是如此。 磁盘突发对 IO 模式的不可预测更改提供更好的容差。 你可以使用它来最大程度地利用它来进行操作系统磁盘启动，并使用高峰的流量。    

默认情况下，默认情况下，将在[预览区域](https://docs.microsoft.com/azure/virtual-machines/linux/disk-bursting#regional-availability)中为新部署的适用磁盘大小启用磁盘突发支持。无需用户操作。 对于适用大小的现有磁盘，可以使用以下两个选项中的任一选项启用突发：分离并重新附加磁盘，或停止并重新启动连接的 VM。 如果磁盘连接到的虚拟机的最大持续时间超过30分钟，则所有突发适用的磁盘大小都将以完整的突发信用额度开头。 若要详细了解如何在 Azure 磁盘上使用突发，请参阅[高级 SSD 突发](../articles/virtual-machines/linux/disk-bursting.md)。 

### <a name="transactions"></a>事务

对于高级 Ssd，小于或等于 256 KiB 吞吐量的每个 i/o 操作都被视为单个 i/o 操作。 大于 256 KiB 的 i/o 操作会被视为大小为 256 KiB 的多个 i/o 操作。

## <a name="standard-ssd"></a>标准 SSD

Azure 标准 SSD 是经济高效的存储选项，已针对需要一致性能和较低 IOPS 级别的工作负荷进行优化。 对于想要迁移到云的用户而言，标准 SSD 提供良好的入门级体验，尤其是在本地 HDD 解决方案中运行各种工作负荷遇到问题时。 与标准 Hdd 相比，标准 Ssd 提供更好的可用性、一致性、可靠性和延迟。 标准 SSD 适用于 Web 服务器、低 IOPS 应用程序服务器、较少使用的企业应用程序和开发/测试工作负荷。 与标准 Hdd 一样，标准 Ssd 适用于所有 Azure Vm。

### <a name="disk-size"></a>磁盘大小
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

标准 Ssd 旨在提供一位数毫秒的延迟，以及 IOPS 和吞吐量，最高可达上表99% 中所述的限制。 实际的 IOPS 和吞吐量可能因流量模式而有所不同。 相比 HDD 磁盘，标准 SSD 提供更加一致的性能，并且延迟更低。

### <a name="transactions"></a>事务

对于标准 Ssd，小于或等于 256 KiB 吞吐量的每个 i/o 操作都被视为单个 i/o 操作。 大于 256 KiB 的 i/o 操作会被视为大小为 256 KiB 的多个 i/o 操作。 这些事务会对计费产生影响。

## <a name="standard-hdd"></a>标准 HDD

Azure 标准 HDD 为运行不区分延迟的工作负荷提供可靠、低成本的磁盘支持。 使用标准存储，将数据存储在硬盘驱动器 (HDD)。 与基于 SSD 的磁盘相比，标准 HDD 磁盘的延迟、IOPS 和吞吐量可能会略有不同。 标准 HDD 磁盘旨在在10ms 下提供写延迟，并在大多数 IO 操作下的20毫秒下读取延迟，但实际性能可能因 IO 大小和工作负荷模式而异。 使用 Vm 时，可以将标准 HDD 磁盘用于开发/测试方案和不太重要的工作负荷。 标准 Hdd 可用于所有 Azure 区域，并且可用于所有 Azure Vm。

### <a name="disk-size"></a>磁盘大小
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>事务

对于标准 Hdd，无论 i/o 大小如何，每个 IO 操作都视为单个事务。 这些事务会对计费产生影响。

## <a name="billing"></a>计费

使用托管磁盘时，将考虑以下计费事项：

- 磁盘类型
- 托管磁盘大小
- 快照数量
- 出站数据传输
- 事务数

**托管磁盘大小**：托管磁盘按预配大小计费。 Azure 将预配大小映射（向上舍入）到所提供的最接近的磁盘大小。 有关所提供的磁盘大小的详细信息，请参阅前面的表。 每个磁盘将映射到一种受支持的预配磁盘大小套餐并相应地计费。 例如，如果预配了 200 GiB 的标准 SSD，它会映射到 E15 的磁盘大小 (256 GiB)。 任何预配的磁盘根据每月的高级存储优惠价格按小时计费。 例如，如果在预配 E10 磁盘的 20 小时后删除它，则会以 20 小时计算 E10 产品/服务的费用。 这与写入磁盘的实际数据量无关。

**快照**：根据使用的大小对快照进行计费。 例如，如果创建预配容量为 64 GiB 且实际使用数据大小为 10 GiB 的托管磁盘的快照，则仅针对已用数据大小 10 GiB 对该快照计费。
