---
title: "Azure 存储中的数据复制 | Microsoft Docs"
description: "复制 Microsoft Azure 存储帐户中的数据以实现持久性和高可用性。 复制选项包括本地冗余存储 (LRS)、区域冗余存储 (ZRS)、异地冗余存储 (GRS) 和读取访问异地冗余存储 (RA-GRS)。"
services: storage
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 86bdb6d4-da59-4337-8375-2527b6bdf73f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: marsma
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: 6a5ba89d8b17e0646cd8a6185da6d1094fd64d12
ms.contentlocale: zh-cn
ms.lasthandoff: 05/16/2017

---
# <a name="azure-storage-replication"></a>Azure 存储复制

始终复制 Microsoft Azure 存储帐户中的数据以确保持久性和高可用性。 复制可在同一数据中心内或向第二个数据中心复制你的数据，具体取决于所选的复制选项。 复制可保护数据，并在发生暂时性硬件故障时保留应用程序正常运行时间。 如果数据复制到第二个数据中心，还可以保护数据，以免主要位置发生灾难性故障。

即使面临故障时，复制也可确保存储帐户满足[存储的服务级别协议 (SLA)](https://azure.microsoft.com/support/legal/sla/storage/)的要求。 请参阅 SLA，了解有关 Azure 存储确保持续性和可用性的信息。

创建存储帐户时，可以选择以下复制选项之一：

* [本地冗余存储 (LRS)](#locally-redundant-storage)
* [区域冗余存储空间 (ZRS)](#zone-redundant-storage)
* [异地冗余存储 (GRS)](#geo-redundant-storage)
* [读取访问异地冗余存储 (RA-GRS)](#read-access-geo-redundant-storage)

创建存储帐户时，默认选项为读取访问异地冗余存储 (RA-GRS)。

下表简要概述了 LRS、ZRS、GRS 和 RA-GRS 之间的差异，而后续章节将详细介绍每种类型的复制。

| 复制策略 | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| 数据在多个数据中心之间进行复制。 |否 |是 |是 |是 |
| 可以从辅助位置和主位置读取数据。 |否 |否 |否 |是 |
| 在单独的节点上维护的数据副本数。 |3 |3 |6 |6 |

有关不同冗余选项的定价信息，请参阅 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)。

> [!NOTE]
> 高级存储仅支持本地冗余存储 (LRS)。 有关高级存储的信息，请参阅[高级存储：适用于 Azure 虚拟机工作负荷的高性能存储](storage-premium-storage.md)。
>

## <a name="locally-redundant-storage"></a>本地冗余存储
本地冗余存储 (LRS) 在存储缩放单位中复制数据三次，该缩放单位托管在创建存储帐户的区域中的数据中心内。 仅在写入所有三个副本后，才成功返回写入请求。 这三个副本各驻留在同一存储缩放单位中的不同容错域和升级域中。

存储缩放单位是存储节点的机架的集合。 容错域 (FD) 是一组代表出错的物理单元的节点，可将其视为属于同一物理机架的节点。 升级域 (UD) 是一组在服务升级（推出）过程中一起升级的节点。 三个副本将分布在同一存储缩放单位中的 UD 和 FD 上，以确保即使在硬件故障影响单个机架时，或在推出期间升级节点时，数据也可用。

LRS 是成本最低的选项，与其他选项相比，提供最小的持久性。 如果发生数据中心级灾难（火灾、洪灾等），所有三个副本可能丢失或无法恢复。 为了减小此风险，建议大多数应用程序使用异地冗余存储 (GRS)。

在某些情况下，可能仍需要使用本地冗余存储：

* 提供 Azure 存储复制选项最高的最大带宽。
* 如果应用程序存储可轻松重构的数据，则可以选择 LRS。
* 由于数据治理需要，某些应用程序被限制为只能在一个国家/地区内复制数据。 配对区域可以在另一个国家/地区中。 有关区域对的详细信息，请参阅 [Azure 区域](https://azure.microsoft.com/regions/)。

## <a name="zone-redundant-storage"></a>区域冗余存储
区域冗余存储空间 (ZRS) 除了存储类似于 LRS 的三个副本外，还在一个或两个区域内的数据中心之间异步复制数据，从而提供比 LRS 更高的持久性。 即使主数据中心不可用或不可恢复，存储在 ZRS 中的数据也是持久的。
计划使用 ZRS 的客户应了解：

* ZRS 仅可用于通用存储帐户中的块 blob，并且仅在存储服务版本 2014-02-14 及更高版本中受支持。
* 由于异步复制涉及延迟，因此在发生本地灾难时，如果无法将数据从主要区域恢复，则尚未复制到次要区域的更改将会丢失。
* 在 Microsoft 启动故障转移到次要区域之前，该副本可能不可用。
* ZRS 帐户之后将无法转换为 LRS 或 GRS。 类似地，现有的 LRS 或 GRS 帐户也无法转换为 ZRS 帐户。
* ZRS 帐户没有度量值或日志记录功能。

## <a name="geo-redundant-storage"></a>异地冗余存储
异地冗余存储 (GRS) 将数据复制到距主区域数百英里以外的辅助区域。 如果存储帐户启用了 GRS，即使在遇到区域完全停电或导致主要区域不可恢复的灾难时，数据也能持久保存。

对于启用了 GRS 的存储帐户，更新将首先提交到主要区域，并在其中复制三次。 然后，更新将异步复制到次要区域（也是在其中复制三次）。

使用 GRS 时，如 LRS 所述，主要和次要区域在一个存储缩放单位内管理跨单独容错域和升级域的副本。

注意事项：

* 由于异步复制涉及延迟，因此在遇到区域性灾难时，如果无法将数据从主要区域中恢复，则尚未复制到次要区域的更改将会丢失。
* 在 Microsoft 启动故障转移到次要区域之前，该副本不可用。 如果 Microsoft 启动了故障转移到次要区域，那么在故障转移完成后，就可以读取和编写该数据的访问。 有关详细信息，请参阅[灾难恢复指南](storage-disaster-recovery-guidance.md)。 
* 如果应用程序想要从次要区域中进行读取，用户应启用 RA-GRS。

创建存储帐户时，可以为帐户选择主要区域。 次要区域是根据主要区域确定的且无法更改。 下表显示了配对的主要区域和次要区域。

| 主要 | 辅助 |
| --- | --- |
| 美国中北部 |美国中南部 |
| 美国中南部 |美国中北部 |
| 美国东部 |美国西部 |
| 美国西部 |美国东部 |
| 美国东部 2 |美国中部 |
| 美国中部 |美国东部 2 |
| 欧洲北部 |欧洲西部 |
| 欧洲西部 |欧洲北部 |
| 东南亚 |东亚 |
| 东亚 |东南亚 |
| 中国东部 |中国北部 |
| 中国北部 |中国东部 |
| 日本东部 |日本西部 |
| 日本西部 |日本东部 |
| 巴西南部 |美国中南部 |
| 澳大利亚东部 |澳大利亚东南部 |
| 澳大利亚东南部 |澳大利亚东部 |
| 印度南部 |印度中部 |
| 印度中部 |印度南部 |
| 印度西部 |印度南部 |
| 美国政府爱荷华州 |美国政府弗吉尼亚州 |
| 美国政府弗吉尼亚州 |美国政府爱荷华州 |
| 加拿大中部 |加拿大东部 |
| 加拿大东部 |加拿大中部 |
| 英国西部 |英国南部 |
| 英国南部 |英国西部 |
| 德国中部 |德国东北部 |
| 德国东北部 |德国中部 |
| 美国西部 2 |美国中西部 |
| 美国中西部 |美国西部 2 |

有关 Azure 支持的区域的最新信息，请参阅 [Azure 区域](https://azure.microsoft.com/regions/)。

## <a name="read-access-geo-redundant-storage"></a>读取访问异地冗余存储
除了 GRS 所提供的在两个区域之间进行复制外，读取访问异地冗余存储 (RA-GRS) 还提供对辅助位置中的数据的只读访问权限，从而最大限度地提高了存储帐户的可用性。

当启用对次要区域中的数据的只读访问权限时，除了存储帐户的主终结点外，还可以在辅助终结点上获取数据。 辅助终结点与主终结点类似，但会在帐户名称后面追加后缀 `–secondary`。 例如，如果 Blob 服务的主终结点是 `myaccount.blob.core.windows.net`，辅助终结点则是 `myaccount-secondary.blob.core.windows.net`。 存储帐户的访问密钥对于主终结点和辅助终结点是相同的。

注意事项：

* 应用程序必须管理在使用 RA-GRS 时与之交互的终结点。
* 由于异步复制涉及延迟，因此在遇到区域性灾难时，如果无法将数据从主要区域中恢复，则尚未复制到次要区域的更改将会丢失。
* 如果 Microsoft 启动了故障转移到次要区域，那么在故障转移完成后，你就可以读取和编写该数据。 有关详细信息，请参阅[灾难恢复指南](storage-disaster-recovery-guidance.md)。 
* RA-GRS 的用途是实现高可用性。 有关可伸缩性的指南，请查看[性能清单](storage-performance-checklist.md)。

## <a name="next-steps"></a>后续步骤
* [使用 RA-GRS 存储设计高度可用的应用程序](storage-designing-ha-apps-with-ragrs.md)
* [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)
* [有关 Azure 存储帐户](storage-create-storage-account.md)
* [Azure 存储可伸缩性和性能目标](storage-scalability-targets.md)
* [Microsoft Azure Storage redundancy options and read access geo redundant storage ](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)（Microsoft Azure 存储冗余选项和读取访问异地冗余存储）
* [SOSP Paper - Azure Storage: A highly available cloud storage service with strong consistency](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)（SOSP 论文 - Azure 存储：具有高度一致性的高可用性云存储服务）


