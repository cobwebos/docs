---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: jeking
ms.custom: include file
ms.openlocfilehash: f5b6e0e74bbab33b9ae6fbacca5c55ea434d3e41
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399951"
---
异地冗余存储 (GRS) 通过将数据复制到距主要区域数百英里以外的次要区域，用于在给定的一年内至少为对象提供 99.99999999999999%（16 个 9）的持久性。 如果存储帐户启用了 GRS，则即使遇到区域完全停电或导致主区域不可恢复的灾难，数据也能持久保存。

如果选择启用 GRS，可从两个相关选项中进行选择：

* GRS 将数据复制到次要区域中的另一个数据中心，但仅当 Microsoft 发起了从主要区域到次要区域的故障转移时，才可读取这些数据。 
* 读取访问异地冗余存储 (RA-GRS) 基于 GRS。 RA-GRS 将数据复制到次要区域中的另一个数据中心，同时还提供从次要区域读取数据的选项。 如果使用 RA-GRS，无论 Microsoft 是否发起从主要区域到次要区域的故障转移，都能从次要区域读取数据。 

对于已启用 GRS 或 RA-GRS 的存储帐户，首先会使用本地冗余存储 (LRS) 复制所有数据。 首先将更新提交到主要位置，并使用 LRS 复制更新。 然后，使用 GRS 以异步方式将更新复制到次要区域。 将数据写入次要位置后，还会使用 LRS 在该位置复制数据。 

主要和次要区域在一个存储缩放单元内管理跨单独的容错域和升级域管理副本。 存储缩放单元是数据中心内的基本复制单元。 此级别的复制由 LRS 提供；有关详细信息，请参阅[本地冗余存储 (LRS)：Azure 存储的低成本数据冗余](../articles/storage/common/storage-redundancy-lrs.md)。

确定要使用哪个复制选项时，请记住以下几点：

* 区域冗余存储 (ZRS) 提供高可用性和同步复制，在某些情况下可能比 GRS 或 RA-GRS 更有利。 有关 ZRS 的详细信息，请参阅 [ZRS](../articles/storage/common/storage-redundancy-zrs.md)。
* 由于异步复制涉及延迟，因此在遇到区域性灾难时，如果无法将数据从主要区域中恢复，则尚未复制到次要区域的更改会丢失。
* 使用 GRS 时，在 Microsoft 启动故障转移到次要区域之前，该副本不可用。 如果 Microsoft 启动了故障转移到次要区域，那么在故障转移完成后，就可以读取和编写该数据的访问。 有关详细信息，请参阅[灾难恢复指南](../articles/storage/common/storage-disaster-recovery-guidance.md)。
* 如果应用程序需要从次要区域读取数据，请启用 RA-GRS。

## <a name="read-access-geo-redundant-storage"></a>读取访问异地冗余存储

读取访问异地冗余存储 (RA-GRS) 可最大程度提高存储帐户的可用性。 除了跨两个区域的异地复制之外，RA-GRS 提供对次要位置中的数据的只读访问。

当启用对次要区域中的数据的只读访问权限时，可以在存储帐户的辅助终结点以及主终结点上获取数据。 辅助终结点与主终结点类似，但会在帐户名称后面追加后缀 `–secondary`。 例如，如果 Blob 服务的主终结点是 `myaccount.blob.core.windows.net`，辅助终结点则是 `myaccount-secondary.blob.core.windows.net`。 存储帐户的访问密钥对于主终结点和辅助终结点是相同的。

使用 RA-GRS 时需要注意一些注意事项：

* 应用程序必须管理在使用 RA-GRS 时与之交互的终结点。
* 由于异步复制涉及延迟，因此如果无法将数据从主要区域中恢复（例如在遇到区域性灾难时），则尚未复制到次要区域的更改可能会丢失。
* 可以检查存储帐户的上次同步时间。 上次同步时间是 GMT 日期/时间值。 在上次同步时间之前主要位置的写入内容已成功写入次要位置，这意味着可以从次要位置读取这些内容。 在上次同步时间之后主要位置的写入内容不一定可供读取。 可以使用 [Azure 门户](https://portal.azure.com/)、[Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md) 或通过 Azure 存储客户端库之一查询此值。
* 如果 Microsoft 启动了故障转移到次要区域，那么在故障转移完成后，便可以读取和编写该数据。 有关详细信息，请参阅[灾难恢复指南](../articles/storage/common/storage-disaster-recovery-guidance.md)。
* 有关如何切换到次要区域的信息，请参阅[在 Azure 存储中断时该怎么办](../articles/storage/common/storage-disaster-recovery-guidance.md)。
* RA-GRS 的用途是实现高可用性。 有关可伸缩性的指南，请查看[性能清单](../articles/storage/common/storage-performance-checklist.md)。
* 有关如何使用 RA-GRS 进行高可用性设计的建议，请参阅[使用 RA-GRS 存储设计高可用性应用程序](../articles/storage/common/storage-designing-ha-apps-with-ragrs.md)。

## <a name="what-is-the-rpo-and-rto-with-grs"></a>GRS 的 RPO 和 RTO 是什么意思？
**恢复点目标 (RPO)：** 在 GRS 和 RA-GRS 中，存储服务以异步方式将数据从主要位置异地复制到次要位置。 主要区域中发生重大区域灾难时，Microsoft 会故障转移到次要区域。 如果进行故障转移，则尚未进行异地复制的最近更改可能会丢失。 潜在的数据丢失分钟数称为 RPO，它表示可将数据恢复到的时间点。 Azure 存储的 RPO 通常小于 15 分钟，但目前没有 SLA 规定异地复制所用时长。

**恢复时间目标 (RTO)：** RTO 用于度量执行故障转移以及将存储帐户恢复联机所花费的时间。 执行故障转移的时间包括以下操作：

   * Microsoft 确定是否可以在主要位置恢复数据或是否需要故障转移所需的时间。
   * 通过将主要 DNS 条目更改为指向次要位置来执行存储帐户故障转移的时间。

   Microsoft 会认真负责保留数据。 如果有任何机会在主要区域中恢复数据，则 Microsoft 会延迟故障转移并专注于恢复数据。 该服务的未来版本将允许在帐户级别触发故障转移，从而可以自己控制 RTO。

## <a name="paired-regions"></a>配对区域 

创建存储帐户时，可以为帐户选择主要区域。 配对的次要区域是根据主要区域确定的且无法更改。 有关 Azure 支持的区域的最新信息，请参阅[业务连续性和灾难恢复 (BCDR)：Azure 配对区域](../articles/best-practices-availability-paired-regions.md)。
