---
title: Azure 存储中的数据复制 | Microsoft Docs
description: 复制 Microsoft Azure 存储帐户中的数据以实现持久性和高可用性。 复制选项包括本地冗余存储 (LRS)、区域冗余存储 (ZRS)、异地冗余存储 (GRS) 和读取访问异地冗余存储 (RA-GRS)。
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.workload: storage
ms.topic: article
ms.date: 01/21/2018
ms.author: tamram
ms.openlocfilehash: 600b66af3b7da24c5a40d09d5cdf76f2d5be67ac
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="azure-storage-replication"></a>Azure 存储复制

始终复制 Microsoft Azure 存储帐户中的数据以确保持久性和高可用性。 复制功能会复制数据，以便可防范临时硬件故障，从而使应用程序保持正常运行。 

可以选择在同一数据中心中、跨同一区域中的数据中心或者跨区域复制数据。 如果跨多个数据中心或者跨区域复制数据，则还可防范单一位置的灾难性故障。

即使面临故障时，复制也可确保存储帐户满足[存储的服务级别协议 (SLA)](https://azure.microsoft.com/support/legal/sla/storage/)的要求。 请参阅 SLA，了解有关 Azure 存储确保持续性和可用性的信息。

创建存储帐户时，可以选择以下复制选项之一：

* [本地冗余存储 (LRS)](#locally-redundant-storage)
* [区域冗余存储空间 (ZRS)](#zone-redundant-storage)
* [异地冗余存储 (GRS)](#geo-redundant-storage)
* [读取访问异地冗余存储 (RA-GRS)](#read-access-geo-redundant-storage)

创建存储帐户时，默认选项为本地冗余存储 (LRS)。

下表提供 LRS、ZRS、GRS 和 RA-GRS 之间差异的简要概述。 本文的后续各部分更加详细地介绍了每种类型的复制。

| 复制策略 | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| 数据在多个数据中心之间进行复制。 |否 |是 |是 |是 |
| 可以从辅助位置和主位置读取数据。 |否 |否 |否 |是 |
| 旨在给定年份为对象提供 ___ 的持续性。 |至少为 99.999999999% (11 9's)|至少为 99.9999999999% (12 9's)|至少为 99.99999999999999% (16 9's)|至少为 99.99999999999999% (16 9's)|

有关不同冗余选项的定价信息，请参阅 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)。

> [!NOTE]
> 高级存储仅支持本地冗余存储 (LRS)。 有关高级存储的信息，请参阅[高级存储：适用于 Azure 虚拟机工作负荷的高性能存储](../../virtual-machines/windows/premium-storage.md)。
>

## <a name="locally-redundant-storage"></a>本地冗余存储
[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

## <a name="zone-redundant-storage"></a>区域冗余存储
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="zrs-classic-accounts"></a>ZRS 经典版帐户

现有 ZRS 功能现在称为 ZRS 经典版。 ZRS 经典版帐户仅适用于常规用途 V1 存储帐户中的块 Blob。 

ZRS 经典版跨一到两个区域中的数据中心以异步方式复制数据。 除非 Microsoft 启动了到次要区域的故障转移，否则副本可能不可用。 

ZRS 经典版帐户无法与 LRS、GRS 或 RA-GRS 相互转换。 ZRS 经典版帐户也不支持指标或日志记录。   

ZRS 在某个区域中正式发布之后，你在该区域中将无法再通过门户创建 ZRS 经典版帐户，不过可以通过其他方式创建一个帐户。  
将来会提供从 ZRS 经典版到 ZRS 的自动迁移过程。

可以手动从 LRS、ZRS 经典版、GRS 或 RAGRS 帐户迁移 ZRS 帐户数据。 可以使用 AzCopy、Azure 存储资源管理器、Azure PowerShell、Azure CLI 或 Azure 存储客户端库之一执行此手动迁移。

> [!NOTE]
> ZRS 经典版帐户计划于 2021 年 3 月 31 日弃用并需要迁移。 Microsoft 会在弃用之前向 ZRS 经典版客户发送更多详细信息。

其他问题在[常见问题](#frequently-asked-questions)部分中进行了讨论。 

## <a name="geo-redundant-storage"></a>异地冗余存储
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-GRS.md)]

## <a name="read-access-geo-redundant-storage"></a>读取访问异地冗余存储
读取访问异地冗余存储 (RA-GRS) 可最大程度提高存储帐户的可用性。 除了跨两个区域的异地复制之外，RA-GRS 提供对次要位置中的数据的只读访问。

当启用对次要区域中的数据的只读访问权限时，可以在存储帐户的辅助终结点以及主终结点上获取数据。 辅助终结点与主终结点类似，但会在帐户名称后面追加后缀 `–secondary`。 例如，如果 Blob 服务的主终结点是 `myaccount.blob.core.windows.net`，辅助终结点则是 `myaccount-secondary.blob.core.windows.net`。 存储帐户的访问密钥对于主终结点和辅助终结点是相同的。

使用 RA-GRS 时需要注意一些注意事项：

* 应用程序必须管理在使用 RA-GRS 时与之交互的终结点。
* 由于异步复制涉及延迟，因此如果无法将数据从主要区域中恢复（例如在遇到区域性灾难时），则尚未复制到次要区域的更改可能会丢失。
* 如果 Microsoft 启动了故障转移到次要区域，那么在故障转移完成后，便可以读取和编写该数据。 有关详细信息，请参阅[灾难恢复指南](../storage-disaster-recovery-guidance.md)。
* RA-GRS 的用途是实现高可用性。 有关可伸缩性的指南，请查看[性能清单](../storage-performance-checklist.md)。

## <a name="frequently-asked-questions"></a>常见问题

<a id="howtochange"></a>
#### <a name="1-how-can-i-change-the-geo-replication-type-of-my-storage-account"></a>1.如何更改存储帐户的异地复制类型？

   可以使用 [Azure 门户](https://portal.azure.com/)、[Azure Powershell](storage-powershell-guide-full.md) 或者 Azure 存储客户端之一更改存储帐户的异地复制类型。

   > [!NOTE]
   > ZRS 帐户无法转换为 LRS 或 GRS。 类似地，现有的 LRS 或 GRS 帐户也无法转换为 ZRS 帐户。
    
<a id="changedowntime"></a>
#### <a name="2-does-changing-the-replication-type-of-my-storage-account-result-in-down-time"></a>2.更改存储帐户的复制类型是否会导致停机？

   不会，更改存储帐户的复制类型不会导致停机。

<a id="changecost"></a>
#### <a name="3-are-there-additional-costs-to-changing-the-replication-type-of-my-storage-account"></a>3.更改存储帐户的复制类型是否会产生额外费用？

   是的。 如果将存储帐户的复制类型从 LRS 更改为 GRS（或 RA-GRS），则会产生额外的出口流量费用，该费用与将现有数据从主要位置复制到次要位置相关。 复制初始数据后，从主要位置到次要位置进行异地复制不会进一步产生额外的出口流量费用。 有关带宽费用的详细信息，请参阅 [Azure 存储定价页面](https://azure.microsoft.com/pricing/details/storage/blobs/)。

   如果从 GRS 更改为 LRS，则不会产生额外的费用，但会从次要位置删除你的数据。

<a id="ragrsbenefits"></a>
#### <a name="4-how-can-ra-grs-help-me"></a>4.RA-GRS 有什么作用？

   GRS 存储可将数据从主要区域复制到数百英里以外的次要区域。 使用 GRS 时，即使在遇到区域完全停电或导致主要区域不可恢复的灾难，数据也能持久保存。 RA-GRS 存储提供 GRS 复制，还能从次要位置读取数据。 有关如何为实现高可用性进行设计的建议，请参阅[使用 RA-GRS 存储设计高度可用的应用程序](../storage-designing-ha-apps-with-ragrs.md)。

<a id="lastsynctime"></a>
#### <a name="5-is-there-a-way-to-figure-out-how-long-it-takes-to-replicate-my-data-from-the-primary-to-the-secondary-region"></a>5.是否可以估算出将数据从主要区域复制到次要区域需要花费多长时间？

   如果使用 RA-GRS 存储，则可以检查存储帐户的上次同步时间。 上次同步时间是 GMT 日期/时间值。 在上次同步时间之前主要位置的写入内容已成功写入次要位置，这意味着可以从次要位置读取这些内容。 在上次同步时间之后主要位置的写入内容不一定可供读取。 可以使用 [Azure 门户](https://portal.azure.com/)、[Azure PowerShell](storage-powershell-guide-full.md) 或通过 Azure 存储客户端库之一查询此值。

<a id="outage"></a>
#### <a name="6-if-there-is-an-outage-in-the-primary-region-how-do-i-switch-to-the-secondary-region"></a>6.如果主要区域发生中断，如何切换到次要区域？

   有关详细信息，请参阅[在 Azure 存储中断时该怎么办](../storage-disaster-recovery-guidance.md)。

<a id="rpo-rto"></a>
#### <a name="7-what-is-the-rpo-and-rto-with-grs"></a>7.GRS 的 RPO 和 RTO 是什么意思？

   **恢复点目标 (RPO)：**在 GRS 和 RA-GRS 中，存储服务以异步方式将数据从主要位置异地复制到次要位置。 主要区域中发生重大区域灾难时，Microsoft 会故障转移到次要区域。 如果进行故障转移，则尚未进行异地复制的最近更改可能会丢失。 潜在的数据丢失分钟数称为 RPO，它表示可将数据恢复到的时间点。 Azure 存储的 RPO 通常小于 15 分钟，但目前没有 SLA 规定异地复制所用时长。

   **恢复时间目标 (RTO)：**RTO 用于度量执行故障转移以及将存储帐户恢复联机所花费的时间。 执行故障转移的时间包括以下操作：

   * Microsoft 确定是否可以在主要位置恢复数据或是否需要故障转移所需的时间。
   * 通过将主要 DNS 条目更改为指向次要位置来执行存储帐户故障转移的时间。

   Microsoft 会认真负责保留数据。 如果有任何机会在主要区域中恢复数据，则 Microsoft 会延迟故障转移并专注于恢复数据。 该服务的未来版本将允许在帐户级别触发故障转移，从而可以自己控制 RTO。

#### <a name="8-what-azure-storage-objects-does-zrs-support"></a>8.ZRS 支持哪些 Azure 存储对象？ 
块 blob、页 blob（支持 VM 磁盘的除外）、表、文件和队列。 

#### <a name="9-does-zrs-also-include-geo-replication"></a>9.ZRS 是否也包括异地复制？ 
当前 ZRS 不支持异地复制。 如果你的方案需要跨区域复制以用于灾难恢复目的，请改用 GRS 或 RA-GRS 存储帐户。   

#### <a name="10-what-happens-when-one-or-more-zrs-zones-go-down"></a>10.当一个或多个 ZRS 区域中断时，会发生什么情况？ 
当第一个区域中断时，ZRS 会继续跨该区域中的两个剩余区域写入数据副本。 如果第二个区域中断，则读取和写入访问会不可用，直到再次至少有两个区域可用。 

## <a name="next-steps"></a>后续步骤
* [使用 RA-GRS 存储设计高度可用的应用程序](../storage-designing-ha-apps-with-ragrs.md)
* [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)
* [关于 Azure 存储帐户](../storage-create-storage-account.md)
* [Azure 存储可伸缩性和性能目标](storage-scalability-targets.md)
* [Microsoft Azure Storage redundancy options and read access geo redundant storage ](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)（Microsoft Azure 存储冗余选项和读取访问异地冗余存储）
* [SOSP Paper - Azure Storage: A highly available cloud storage service with strong consistency](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)（SOSP 论文 - Azure 存储：具有高度一致性的高可用性云存储服务）
