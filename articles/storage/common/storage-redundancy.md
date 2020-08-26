---
title: 数据冗余
titleSuffix: Azure Storage
description: 了解 Azure 存储中的数据冗余。 复制 Microsoft Azure 存储帐户中的数据以实现持久性和高可用性。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 30839fac6a264ad9defb565663b28a5b12b571b5
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88814512"
---
# <a name="azure-storage-redundancy"></a>Azure 存储冗余

Azure 存储始终会存储数据的多个副本，以防范各种计划内和计划外的事件，包括暂时性的硬件故障、网络中断或断电、大范围自然灾害等。 即使面临故障时，冗余也可确保存储帐户满足 [Azure 存储的服务级别协议 (SLA)](https://azure.microsoft.com/support/legal/sla/storage/) 的要求。

在确定最适合自己方案的冗余选项时，请考虑如何在较低成本与较高可用性和持久性之间做出取舍。 可帮助你确定应选择哪种冗余选项的因素包括：  

- 如何在主要区域中复制数据
- 你的数据是否已复制到地理位置远于主要区域的第二个区域，以防范地区性灾难
- 应用程序是否要求在主要区域出于任何原因而不可用时，能够对次要区域中复制的数据进行读取访问

## <a name="redundancy-in-the-primary-region"></a>主要区域中的冗余

Azure 存储帐户中的数据在主要区域中始终复制三次。 Azure 存储提供了两个有关如何在主要区域中复制数据的选项：

- **本地冗余存储 (LRS)** 在主要区域中的单个物理位置同步复制数据三次。 LRS 是成本最低的复制选项，但不建议对需要高可用性的应用程序使用此选项。
- **区域冗余存储 (ZRS)** 跨主要区域中的三个 Azure 可用性区域同步复制数据。 对于需要高可用性的应用程序，Microsoft 建议在主要区域中使用 ZRS，并复制到次要区域。

### <a name="locally-redundant-storage"></a>本地冗余存储

本地冗余存储 (LRS) 在主要区域中的单个物理位置内复制数据三次。 LRS 可在一年中提供至少 99.999999999%（11 个 9）的对象持久性。

与其他选项相比，LRS 是成本最低的冗余选项，但提供的持久性也最低。 LRS 可以保护数据，使其不受服务器机架和驱动器故障影响。 但是，如果数据中心内发生火灾或洪水等灾难，使用 LRS 的存储帐户的所有副本可能会丢失或无法恢复。 若要缓解此风险，Microsoft 建议使用[区域冗余存储](#zone-redundant-storage) (ZRS)、[异地冗余存储](#geo-redundant-storage) (GRS) 或[异地区域冗余存储](#geo-zone-redundant-storage) (GZRS)。

会同步发生一个针对使用 LRS 的存储帐户的写入请求。 只有将数据写入到所有三个副本后，写入操作才会成功返回。

对于以下场景，LRS 是不错的选项：

- 如果应用程序存储着在发生数据丢失时可轻松重构的数据，则可以选择 LRS。
- 如果由于数据治理需要，某些应用程序被限制为只能在一个国家/地区内复制数据，则可以选择 LRS。 在某些情况下，进行异地复制数据的配对区域可能在另一个国家/地区。 有关配对区域的详细信息，请参阅 [Azure 区域](https://azure.microsoft.com/regions/)。

### <a name="zone-redundant-storage"></a>区域冗余存储

区域冗余存储 (ZRS) 在主区域中的三个 Azure 可用性区域同步复制 Azure 存储数据。 每个可用性区域都是一个独立的物理位置，具有独立的电源、冷却系统和网络。 ZRS 在一年中提供至少 99.9999999999%（12 个 9）的 Azure 存储数据对象持久性。

通过使用 ZRS，即使某个区域变得不可用，也仍可访问你的数据进行读写操作。 如果区域变得不可用，则 Azure undertakes 网络更新，如 DNS 重构。 如果完成更新之前访问数据，这些更新可能会影响应用程序。 在设计用于 ZRS 的应用程序时，遵循暂时性故障的处理做法，包括结合指数退让实施重试策略。

会同步发生一个针对使用 ZRS 的存储帐户的写入请求。 写入操作仅在将数据写入三个可用性区域中的所有副本后才能成功返回。

对于需要一致性、耐用性和高可用性的方案，Microsoft 建议在主要区域中使用 ZRS。 如果要将应用程序限制为仅在国家或地区内复制数据，则我们还建议使用 ZRS，因为数据治理要求。

如果数据变为暂时不可用，ZRS 可提供出色的性能、低延迟和数据复原能力。 但是，如果多个区域永久受到影响，在发生区域性的灾难时，ZRS 本身可能无法保护数据。 为了防御区域灾难，Microsoft 建议使用[地理区域冗余存储](#geo-zone-redundant-storage) (GZRS)，该存储在主要区域中使用 ZRS，并将数据异地复制到次要区域。

下表显示了哪些类型的存储帐户支持哪些区域中的 ZRS：

| 存储帐户类型 | 支持的区域 | 支持的服务 |
|--|--|--|
| 常规用途 v2<sup>1</sup> | 亚洲东南部<br /> 澳大利亚东部<br /> 北欧<br />  西欧<br /> 法国中部<br /> 日本东部<br /> 南非北部<br /> 英国南部<br /> 美国中部<br /> 美国东部<br /> 美国东部 2<br /> 美国西部 2 | 块 Blob<br /> 页 Blob<sup>2</sup><br /> 文件共享（共享）<br /> 表<br /> 队列<br /> |
| BlockBlobStorage<sup>1</sup> | 亚洲东南部<br /> 澳大利亚东部<br /> 西欧<br /> 美国东部 <br /> 美国西部 2| 仅限高级块 blob |
| FileStorage | 亚洲东南部<br /> 澳大利亚东部<br /> 西欧<br /> 美国东部 <br /> 美国西部 2 | 仅高级文件共享 |

<sup>1</sup> ZRS 帐户当前不支持存档层。<br />
<sup>2</sup> 包含虚拟机的 Azure 托管磁盘的存储帐户始终使用 LRS。 Azure 非托管磁盘还应使用 LRS。 可以为使用 GRS 的 Azure 非托管磁盘创建存储帐户，但由于异步异地复制可能存在一致性问题，因此不建议这样做。 托管磁盘和非托管磁盘都不支持 ZRS 或 GZRS。 有关托管磁盘的详细信息，请参阅 [Azure 托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/)。

若要详细了解哪些区域支持 ZRS，请参阅[什么是 Azure 可用性区域？](../../availability-zones/az-overview.md)中的“服务支持（按区域）”。

## <a name="redundancy-in-a-secondary-region"></a>次要区域中的冗余

对于需要高可用性的应用程序，可以选择将存储帐户中的数据另外复制到距离主要区域数百英里以外的次要区域。 如果存储帐户已复制到次要区域，则即使遇到区域性服务完全中断或导致主要区域不可恢复的灾难，数据也能持久保存。

创建存储帐户时，可以为帐户选择主要区域。 配对的次要区域是根据主要区域确定的且无法更改。 有关 Azure 支持的区域的详细信息，请参阅 [Azure 区域](https://azure.microsoft.com/global-infrastructure/regions/)。

Azure 存储提供了两种将数据复制到次要区域的选项：

- **异地冗余存储 (GRS)** 使用 LRS 在主区域中的单个物理位置同步复制数据三次。 然后，它会将数据异步复制到次要区域中的单个物理位置。
- **异地区域冗余存储 (GZRS)** 使用 ZRS 在主区域中的三个 Azure 可用性区域同步复制数据。 然后，它会将数据异步复制到次要区域中的单个物理位置。

GRS 和 GZRS 之间的主要区别是在主要区域中复制数据的方式。 在次要区域中，数据始终使用 LRS 同步复制三次。 辅助区域中的 LRS 保护你的数据免受硬件故障的影响。

使用 GRS 或 GZRS 时，次要区域中的数据不可用于读取或写入访问，除非存在到次要区域的故障转移。 若要对次要区域进行读取访问，请将存储帐户配置为使用读取访问异地冗余存储 (GRS) 或读取访问地域冗余存储 (GZRS) 。 有关详细信息，请参阅[对次要区域中数据的读取访问权限](#read-access-to-data-in-the-secondary-region)。

如果主要区域变得不可用，则可以选择故障转移到次要区域。 故障转移完成后，次要区域将成为主要区域，你便可以再次读取和写入数据。 有关灾难恢复以及如何故障转移到次要区域的详细信息，请参阅[灾难恢复和存储帐户故障转移](storage-disaster-recovery-guidance.md)。

> [!IMPORTANT]
> 由于数据以异步方式复制到次要区域，因此如果无法恢复主要区域，则影响主要区域的故障可能会导致数据丢失。 最近对主要区域的写入与最近对次要区域的写入之间的时间间隔称为恢复点目标 (RPO)。 RPO 指示可以将数据恢复到的时间点。 Azure 存储的 RPO 通常小于 15 分钟，但目前没有有关将数据复制到次要区域所用的时长的 SLA。

### <a name="geo-redundant-storage"></a>异地冗余存储

异地冗余存储 (GRS) 使用 LRS 在主要区域中的单个物理位置内同步复制数据三次。 然后，它将数据异步复制到距离主要区域数百英里以外的次要区域中的单个物理位置。 GRS 在给定一年中提供至少 99.99999999999999%（16 个 9）的 Azure 存储数据对象持久性。

首先会将写入操作提交到主要位置，并使用 LRS 复制该操作。 然后会以异步方式将更新复制到次要区域。 将数据写入次要位置后，还会使用 LRS 在该位置复制数据。

### <a name="geo-zone-redundant-storage"></a>异地区域冗余存储

地域区域冗余存储 (GZRS) 将冗余跨可用性区域提供的高可用性与异地复制提供的区域中断保护相结合。 将跨主要区域中的三个 [Azure 可用性区域](../../availability-zones/az-overview.md)复制 GZRS 存储帐户中的数据，并将其复制到次要地理区域，以防御区域灾难。 Microsoft 建议对需要最大程度的一致性、耐用性和可用性、卓越性能和灾难恢复复原能力的应用程序使用 GZRS。

如果使用 GZRS 存储帐户，在可用性区域不可用或无法恢复的情况下，可以继续读取和写入数据。 此外，即使在遇到区域完全停电或导致主要区域不可恢复的灾难，数据也能持久保存。 GZRS 可在一年中提供至少 99.99999999999999%（16 个 9）的对象持久性。

仅常规用途 v2 存储帐户支持 GZRS 和 RA-GZRS。 有关存储帐户类型的详细信息，请参阅 [Azure 存储帐户概述](storage-account-overview.md)。 GZRS 和 RA-GZRS 支持块 Blob、页 Blob（但无法用于 VHD 磁盘）、文件、表和队列。

以下区域支持 GZRS 和 RA-GZRS：

- 亚洲东南部
- 北欧
- 西欧
- 日本东部
- 英国南部
- 美国中部
- 美国东部
- 美国东部 2
- 美国西部 2

若要详细了解定价，请参阅 [Blob](https://azure.microsoft.com/pricing/details/storage/blobs)、[文件](https://azure.microsoft.com/pricing/details/storage/files/)、[列队](https://azure.microsoft.com/pricing/details/storage/queues/)和[表](https://azure.microsoft.com/pricing/details/storage/tables/)的定价详细信息。

## <a name="read-access-to-data-in-the-secondary-region"></a>对次要区域中数据的读取访问权限

异地冗余存储（使用 GRS 或 GZRS）将数据复制到次要区域中的另一个物理位置，以防止区域中断。 但是，仅当客户或 Microsoft 启动了从主要区域到次要区域的故障转移时，才能读取该数据。 当你启用对辅助区域的读取访问权限时，你的数据将随时可供读取，其中包括在主要区域不可用的情况下。 若要对次要区域进行读取访问，可启用读取访问异地冗余存储 (RA-GRS) 或读取访问地理区域冗余存储 (RA-GZRS)。

> [!NOTE]
> Azure 文件不支持读取访问异地冗余存储 (GRS) 和读取访问地域冗余存储 (GZRS) 。

### <a name="design-your-applications-for-read-access-to-the-secondary"></a>设计应用程序以便能够对次要区域进行读取访问

如果存储帐户已配置为对次要区域进行读取访问，则你可以设计自己的应用程序，以便在主要区域出于任何原因而不可用时，能够无缝转换为从次要区域读取数据。 

启用 "GRS" 或 "GZRS" 后，辅助区域可用于读取访问权限，因此你可以提前测试应用程序，以确保在发生服务中断时，它将从辅助区域正确读取。 有关如何设计应用程序以实现高可用性的详细信息，请参阅[使用异地冗余设计高度可用的应用程序](geo-redundant-design.md)。

启用对次要区域的读取访问后，应用程序可以从次要终结点以及主要终结点读取数据。 辅助终结点将后缀“–secondary”追加到帐户名称。 例如，如果 Blob 存储的主终结点是 `myaccount.blob.core.windows.net`，则辅助终结点是 `myaccount-secondary.blob.core.windows.net`。 存储帐户的帐户访问密钥对于主要终结点和次要终结点是相同的。

### <a name="check-the-last-sync-time-property"></a>检查“上次同步时间”属性

由于数据以异步方式复制到次要区域，因此次要区域通常位于主要区域的后面。 如果主要区域发生故障，对主要区域的所有写入有可能尚未复制到次要区域。

若要确定哪些写入操作已复制到次要区域，应用程序可以检查存储帐户的“上次同步时间”属性。 在上次同步时间之前写入到主要区域的所有写入操作已成功复制到次要区域，这意味着，可以从次要区域读取这些操作。 在上次同步时间之后写入到主要区域的任何写入操作不一定已复制到次要区域，这意味着，它们不一定可供读取操作使用。

可以使用 Azure PowerShell、Azure CLI 或某个 Azure 存储客户端库查询“上次同步时间”属性的值。 “上次同步时间”属性是一个 GMT 日期/时间值。 有关详细信息，请参阅[检查存储帐户的“上次同步时间”属性](last-sync-time-get.md)。

## <a name="summary-of-redundancy-options"></a>冗余选项摘要

以下各部分中的表总结了可用于 Azure 存储的冗余选项

### <a name="durability-and-availability-parameters"></a>持久性和可用性参数

下表描述了每个冗余选项的关键参数：

| 参数 | LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|:-|
| 给定年份内对象的持续性百分比<sup>1</sup> | 至少为 99.999999999% (11 9's) | 至少为 99.9999999999% (12 9's) | 至少为 99.99999999999999% (16 9's) | 至少为 99.99999999999999% (16 9's) |
| 读取请求的可用性 SLA<sup>1</sup> | 至少为 99.9%（冷访问层为 99%） | 至少为 99.9%（冷访问层为 99%） | GRS 至少为 99.9%（冷访问层为 99%）<br /><br />对于 RA-GRS，至少为 99.99%（冷访问层为 99.9%） | 对于 GZRS，至少为 99.9%（冷访问层为 99%）<br /><br />对于 RA-GZRS，至少为 99.99%（冷访问层为 99.9%） |
| 写入请求的可用性 SLA<sup>1</sup> | 至少为 99.9%（冷访问层为 99%） | 至少为 99.9%（冷访问层为 99%） | 至少为 99.9%（冷访问层为 99%） | 至少为 99.9%（冷访问层为 99%） |

<sup>1</sup> 有关 Azure 存储确保持续性和可用性的信息，请参阅 [Azure 存储 SLA](https://azure.microsoft.com/support/legal/sla/storage/)。

### <a name="durability-and-availability-by-outage-scenario"></a>持久性和可用性（按中断方案）

下表指示数据是否持久且在给定方案中可用，具体取决于对存储帐户启用的冗余类型：

| 中断方案 | LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|:-|
| 数据中心内的节点变为不可用 | 是 | 是 | 是 | 是 |
| 整个数据中心（区域性或非区域性）不可用 | 否 | 是 | 是<sup>1</sup> | 是 |
| 主要区域发生区域范围的服务中断 | 否 | 否 | 是<sup>1</sup> | 是<sup>1</sup> |
| 如果主区域变得不可用，可对次要区域进行读取访问 | 否 | 否 | 是（通过 RA-GRS） | 是（通过 RA-GZRS） |

<sup>1</sup> 如果主要区域变为不可用，则需要执行帐户故障转移来恢复写入可用性。 有关详细信息，请参阅[灾难恢复和存储帐户故障转移](storage-disaster-recovery-guidance.md)。

### <a name="supported-storage-account-types"></a>支持的存储帐户类型

下表显示了每种存储帐户类型都支持哪些冗余选项。 有关存储帐户类型，请参阅[存储帐户概述](storage-account-overview.md)。

| LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|
| 常规用途 v2<br /> 常规用途 v1<br /> 块 Blob 存储<br /> Blob 存储<br /> 文件存储 | 常规用途 v2<br /> 块 Blob 存储<br /> 文件存储 | 常规用途 v2<br /> 常规用途 v1<br /> Blob 存储 | 常规用途 v2 |

所有存储帐户的所有数据的复制均基于存储帐户的冗余选项。 复制的对象包括块 Blob、追加 Blob、页 Blob、队列、表和文件。 将复制所有层（包括存档层）中的数据。 有关 blob 层的详细信息，请参阅 [Azure Blob 存储：热、冷和存档访问层](../blobs/storage-blob-storage-tiers.md)。

有关每个冗余选项的定价信息，请参阅 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)。

> [!NOTE]
> Azure 高级磁盘存储目前仅支持本地冗余存储 (LRS)。 块 Blob 存储帐户在本地支持特定区域中的本地冗余存储 (LRS) 和区域冗余存储 (ZRS)。

## <a name="data-integrity"></a>数据完整性

Azure 存储使用循环冗余检查 (CRC) 定期验证存储的数据的完整性。 如果检测到数据损坏，则使用冗余数据进行修复。 Azure 存储还对所有网络流量计算校验和，以检测存储或检索数据时数据包损坏的情况。

## <a name="see-also"></a>另请参阅

- [检查存储帐户的“上次同步时间”属性](last-sync-time-get.md)
- [更改存储帐户的冗余选项](redundancy-migration.md)
- [使用异地冗余设计高度可用的应用程序](geo-redundant-design.md)
- [灾难恢复和存储帐户故障转移](storage-disaster-recovery-guidance.md)
