---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/14/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: da46687517dbfe189571286087d4ef29d50d1246
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2019
ms.locfileid: "54906277"
---
# <a name="standard-ssd-managed-disks-for-azure-virtual-machine-workloads"></a>Azure 虚拟机工作负荷的标准 SSD 托管磁盘

Azure 标准固态硬盘 (SSD) 托管磁盘是经济高效的存储选项，已针对需要一致性能和较低 IOPS 级别的工作负荷进行优化。 对于想要迁移到云的用户而言，标准 SSD 提供良好的入门级体验，尤其是在本地 HDD 解决方案中运行各种工作负荷遇到问题时。 相比 HDD 磁盘，标准 SSD 提供更好的可用性、一致性、可靠性和延迟，非常适合 Web 服务器、低 IOPS 应用程序服务器、不常使用的企业应用程序和开发/测试工作负荷。

## <a name="standard-ssd-features"></a>标准 SSD 的功能

**托管磁盘**：标准 SSD 只可用作托管磁盘。 标准 SSD 不支持非托管磁盘和页 Blob。 创建托管磁盘时，请指定“标准 SSD”作为磁盘类型，并指明所需的磁盘大小，然后，Azure 将自动创建并管理该磁盘。
标准 SSD 支持托管磁盘提供的所有经典部署模型操作。 例如，可以像处理托管磁盘一样创建、复制标准 SSD 托管磁盘或生成其快照。

**虚拟机**：标准 SSD 可用于所有 Azure VM，包括不支持高级磁盘的 VM 类型。 例如，如果使用 A 系列、N 系列、DS 系列 VM 或其他任何 Azure VM 系列，可对该 VM 使用标准 SSD。 随着标准 SSD 的推出，以前使用基于 HDD 的磁盘的各种工作负荷可以过渡到基于 SSD 的磁盘，从而获得 SSD 具有的一致性能、更高可用性、更低的延迟和总体更佳的体验。

**高持久性和可用性**：标准 SSD 构建在相同的 Azure 磁盘平台基础之上，持续为磁盘提供高可用性和持久性。 Azure 磁盘提供 99.999% 的可用性。 与所有托管磁盘一样，标准 SSD 也提供本地冗余存储 (LRS)。 借助 LRS，平台能够保留每个磁盘的多个数据副本，为 IaaS 磁盘提供企业级的持久性，年度故障率为 0%，在行业中出类拔萃。

**快照**：与所有托管磁盘一样，标准 SSD 也支持创建快照。 快照类型可以是“标准 (HDD)”或“高级 (SSD)”。 为了节约成本，我们建议对所有 Azure 磁盘类型创建“标准 (HDD)”快照。 这是因为，在从快照创建托管磁盘时，始终能够选择更高的层，例如标准 SSD 或高级 SSD。

## <a name="scalability-and-performance-targets"></a>可伸缩性和性能目标

下表包含标准 SSD 目前提供的磁盘大小。 用星号表示的大小当前处于预览阶段。

|标准 SSD 磁盘类型  |磁盘大小  |每个磁盘的 IOPS  |每个磁盘的吞吐量  |
|---------|---------|---------|---------|
|E4     |32 GiB         |最多 120         |最高每秒 25 MiB         |
|E6     |64 GiB         |最多 240         |最高每秒 50 MiB         |
|E10     |128 GiB         |最多 500         |最高每秒 60 MiB         |
|E15     |256 GiB         |最多 500         |最高每秒 60 MiB         |
|E20     |512 GiB         |最多 500         |最高每秒 60 MiB         |
|E30     |1,024 GiB       |最多 500         |最高每秒 60 MiB         |
|E40     |2,048 GiB       |最多 500         |最高每秒 60 MiB         |
|E50     |4,095 GiB       |最多 500         |最高每秒 60 MiB         |
|E60 *     |8,192 GiB       |最多 1,300       |最高每秒 300 MiB        |
|E70 *    |16,384 GiB      |最多 2,000       |最高每秒 500 MiB        |
|E80 *    |32,767 GiB      |最多 2,000       |最高每秒 500 MiB        |

标准 SSD 可为大多数 IO 操作提供 10 毫秒以下的延迟，并在 99% 的时间内提供不超过上表中所述限制的 IOPS 和吞吐量。 实际 IOPS 和吞吐量有时根据流量模式而异。 相比 HDD 磁盘，标准 SSD 提供更加一致的性能，并且延迟更低。

另一方面，高级 SSD 的性能比标准 SSD 更优，具有低延迟、高 IOPS/吞吐量，预配磁盘性能的一致性也更好。 对于关键的生产工作负荷，建议使用“高级 SSD”磁盘类型。 如果工作负荷要求高性能、低延迟磁盘支持，则应考虑使用高级存储。

与高级 SSD 一样，标准 SSD 也使用 256 KiB 的 IO 单位大小。 如果要传输的数据小于 256 KiB，该数据被视为 1 个 I/O 单位。 更大的 I/O 大小被视为多个 256 KiB 大小的 I/O。 例如，1,100 KiB I/O 被视为 5 个 I/O 单位。

## <a name="pricing-and-billing"></a>定价和计费

使用标准 SSD 时，请注意以下计费方式：

- 托管磁盘大小
- 快照
- 出站数据传输
- 事务

**托管磁盘大小**：托管磁盘按预配大小计费。 Azure 将预配大小映射（向上舍入）到最接近的磁盘大小。 有关提供的磁盘大小的详细信息，请参阅前面的“可伸缩性和性能目标”部分的表格。 每个磁盘将映射到一种受支持的预配大小并相应地计费。 例如，如果预配了 200 GiB 的标准 SSD，它将映射到 E15 的磁盘大小 (256 GiB)。 任何预配的磁盘根据每月的高级存储优惠价格按小时计费。 例如，如果在预配 E10 磁盘的 20 小时后删除它，则会以 20 小时计算 E10 产品/服务的费用。 这与写入磁盘的实际数据量无关。

**快照**：托管磁盘的快照根据快照在目标和源端使用的容量（如果有）计费。 有关快照的详细信息，请参阅[托管磁盘快照](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#managed-disk-snapshots)。

**出站数据传输**：[出站数据传输](https://azure.microsoft.com/pricing/details/bandwidth/)（传出 Azure 数据中心的数据）会产生带宽使用费。

**事务**：类似于标准 HDD，标准 SSD 上的事务会产生费用。 事务包括磁盘上的读取和写入操作。 用于标准 SSD 事务计费的 I/O 单位大小为 256 KiB。 更大的 I/O 大小被视为多个 256 KiB 大小的 I/O。

有关虚拟机和托管磁盘定价的详细信息，请参阅：

- [虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)
- [托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="next-steps"></a>后续步骤

若要详细了解如何创建标准 SSD，请参阅演示如何创建包含多个标准 SSD 的 VM 的示例。

> [!div class="nextstepaction"]
> [创建包含多个标准 SSD 的 VM](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/)
