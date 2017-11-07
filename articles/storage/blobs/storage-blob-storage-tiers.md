---
title: "适用于 Blob 的 Azure 热、冷、存档存储 | Microsoft Docs"
description: "适用于 Azure Blob 存储帐户的热、冷、存档存储。"
services: storage
documentationcenter: 
author: michaelhauss
manager: vamshik
editor: tysonn
ms.assetid: eb33ed4f-1b17-4fd6-82e2-8d5372800eef
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/05/2017
ms.author: mihauss
ms.openlocfilehash: 544b11d74a926fe62b8ceca51570ce9d2ee7e6e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-blob-storage-hot-cool-and-archive-preview-storage-tiers"></a>Azure Blob 存储：热、冷、存档（预览版）存储层

## <a name="overview"></a>概述

Azure 存储为 Blob 对象存储提供三个存储层，因此你可以根据数据使用方式最经济有效地存储数据。 Azure **热存储层** 为存储经常访问的数据进行了优化。 Azure 冷存储层为存储不常访问且存储时间至少为一个月的数据进行了优化。 [存档存储层（预览版）](https://azure.microsoft.com/blog/announcing-the-public-preview-of-azure-archive-blob-storage-and-blob-level-tiering)为存储极少访问、存储时间至少为六个月且延迟要求（以小时计）不严格的数据进行了优化。 存档存储层只能用于 Blob 级别，不能在整个存储帐户上使用。 冷存储层中的数据可容许略低的可用性，但仍需要像热数据一样的高持续性和类似的访问时间以及吞吐量特征。 冷数据和存档数据的可用性 SLA 稍低且访问费用较高，但这是可以接受的，因为存储费用要低得多。

如今，存储在云中的数据以指数速度增长。 要为扩展的存储需求管理成本，根据属性（如访问频率和计划保留期）整理数据将很有帮助。 存储在云中的数据在其生成方式、处理方式以及在生存期内的访问方式等方面会不相同。 某些数据在其整个生存期中都会受到积极的访问和修改。 某些数据则在生存期早期会受到频繁的访问，随着数据变旧，访问会极大地减少。 某些数据在云中保持空闲状态，并且在存储后很少（如果有）被访问。

这些数据访问方案的每一个都受益于针对特定访问模式进行了优化的差异化存储层。 Azure Blob 存储采用热、冷、存档存储层，通过单独的定价模型来满足对差异化存储层的这种需要。

## <a name="blob-storage-accounts"></a>Blob 存储帐户

**Blob 存储帐户** 是将非结构化数据作为 Blob（对象）存储在 Azure 存储的专用存储帐户。 对于 Blob 存储帐户，现在可以根据访问模式在帐户级别选择热或冷存储层，或者在 Blob 级别选择热、冷或存档层。 请将极少访问的冷数据以最低存储费用进行存储，将访问频率较低的冷数据以低于热数据的存储费用进行存储，将访问频率较高的热数据以最低访问费用进行存储。 Blob 存储帐户类似于现有的通用存储帐户，并且具有现用帐户的所有卓越的耐用性、可用性、可伸缩性和性能特性，包括针对块 Blob 和追加 Blob 的 100% API 一致性。

> [!NOTE]
> Blob 存储帐户仅支持块 blob 和追加 blob，不支持页 blob。

Blob 存储帐户公开**访问层**属性，它允许用户将存储层指定为**热**或**冷**，具体取决于存储在帐户中的数据。 如果数据的使用模式有所更改，也可以随时在这些存储层之间切换。 存档层（预览版）仅适用于 Blob 级别。

> [!NOTE]
> 更改存储层可能会产生额外费用。 有关更多详细信息，请参阅[定价和计费](#pricing-and-billing)部分。

### <a name="hot-access-tier"></a>热访问层

热存储层的示例使用方案包括：

* 处于活跃使用状态或预期会频繁访问（读取和写入）的数据。
* 分阶段进行处理，并最终迁移至冷存储层的数据。

### <a name="cool-access-tier"></a>冷访问层

冷存储层的示例使用方案包括：

* 短期备份和灾难恢复数据集。
* 不再经常查看、但访问时应立即可用的较旧的媒体内容。
* 在收集更多数据以便将来处理时需要经济高效地存储的大型数据集。 （例如，长期存储的科学数据、来自制造设施的原始遥测数据）

### <a name="archive-access-tier-preview"></a>存档访问层（预览版）

与热存储和冷存储相比，[存档存储](https://azure.microsoft.com/blog/announcing-the-public-preview-of-azure-archive-blob-storage-and-blob-level-tiering)的存储费用最低，但数据检索费用较高。

不能读取、复制、覆盖或修改存档存储中的 Blob， 也不能拍摄其快照。 但是，可以通过现有操作删除、列出、获取 Blob 属性/元数据，或者更改 Blob 的层。 若要读取存档存储中的数据，必须先将 Blob 的层更改为热层或冷层。 此过程称为解除冻结，对于不到 50 GB 的 Blob，可能需要长达 15 小时才能完成。 更大型 Blob 所需的额外时间因 Blob 吞吐量限制而异。

在解除冻结期间，可以通过查看“存档状态”Blob 属性来确认该层是否已更改。 状态显示为“rehydrate-pending-to-hot”或“rehydrate-pending-to-cool”，具体取决于目标层。 完成后，“存档状态”Blob 属性会被删除，“访问层”Blob 属性会反映出该层是热层还是冷层。  

存档存储层的示例使用方案包括：

* 长期备份、存档和灾难恢复数据集
* 必须保留的原始数据，即使它已被处理为最终可用的形式。 （例如，转码为其他格式后的原始媒体文件）
* 需要长时间存储并且几乎不访问的合规性和存档数据。 （例如，安全监控录像、医保组织的旧 X 线/MRI 资料、音频记录、金融服务机构的客户呼叫记录）

### <a name="recommendations"></a>建议

有关存储帐户的详细信息，请参阅 [关于 Azure 存储帐户](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 。

对于仅需要块 blob 或追加 blob 存储的应用程序，我们建议使用 Blob 存储帐户，以利用分层存储的不同定价模型。 但是，我们知道这在某些情况（可以选择使用通用存储帐户）下是不可能的，如：

* 需要使用表、队列或文件，并且想要将 Blob 存储在同一个存储帐户中。 请注意，除了具有相同的共享密钥以外，将这些存储在同一帐户中没有技术优势。

* 仍需要使用经典部署模型。 Blob 存储帐户只有通过 Azure Resource Manager 部署模型才可用。

* 需要使用页 blob。 Blob 存储帐户不支持页 blob。 我们通常建议使用块 blob，除非对页 blob 有特定需求。

* 使用了早于 2014-02-14 的 [存储服务 REST API](https://msdn.microsoft.com/library/azure/dd894041.aspx) 的版本或使用了版本低于 4.x 的客户端库，并且无法升级应用程序。

> [!NOTE]
> 目前，所有 Azure 区域都支持 Blob 存储帐户。
 

## <a name="blob-level-tiering-feature-preview"></a>Blob 级分层功能（预览版）

现在，使用 Blob 级分层功能即可通过名为[设置 Blob 层](/rest/api/storageservices/set-blob-tier)的单一操作在对象级别更改数据的层。 可以在使用模式更改时轻松地在热、冷或存档层之间更改 Blob 的访问层，不需在帐户之间移动数据。 所有层更改都是即时发生的，Blob 正从存档中解除冻结的情况除外。 所有三个存储层中的 Blob 可以在同一帐户中共存。 如果 Blob 没有显式分配的层，则会从帐户访问层设置继承相应的层。

若要使用这些预览版功能，请按 [Azure 存档和 Blob 级分层博客公告](https://azure.microsoft.com/blog/announcing-the-public-preview-of-azure-archive-blob-storage-and-blob-level-tiering)中的说明操作。

下面列出了在 Blob 级分层预览版中适用的一些限制：

* 仅在“美国东部 2”区域中新建的 Blob 存储帐户支持存档存储，且前提是已成功注册预览版。

* 仅在公共区域中新建的 Blob 存储帐户支持 Blob 级分层，且前提是已成功注册预览版。

* Blob 级分层和存档存储仅在 [LRS] (../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#locally-redundant-storage) 存储中受支持。 将来会支持 [GRS](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#geo-redundant-storage) 和 [RA-GRS](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#read-access-geo-redundant-storage)。

* 不能更改包含快照的 Blob 的层。

* 不能对存档存储中的 Blob 执行复制或快照操作。

## <a name="comparison-of-the-storage-tiers"></a>存储层的比较

下表显示了对热和冷存储层的比较。 存档 Blob 级层为预览版，因此没有相应的 SLA。

| | 热存储层 | 冷存储层 |
| ---- | ----- | ----- |
| **可用性** | 99.9% | 99% |
| **可用性** <br> （RA-GRS 读取）| 99.99% | 99.9% |
| 使用费 | 存储费用较高，访问和事务费用较低 | 存储费用较低，访问和事务费用较高 |
| 最小对象大小 | 不适用 | 不适用 |
| 最短存储持续时间 | 不适用 | 不适用 |
| **延迟** <br> （距第一字节时间） | 毫秒 | 毫秒 |
| **可伸缩性和性能目标** | 与通用存储帐户相同 | 与通用存储帐户相同 |

> [!NOTE]
> Blob 存储帐户支持与通用存储帐户相同的性能和可伸缩性目标。 有关详细信息，请参阅 [Azure 存储的可伸缩性和性能目标](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 。


## <a name="pricing-and-billing"></a>定价和计费
Blob 存储帐户使用的定价模型适用于基于存储层的 Blob 存储。 使用 Blob 存储帐户时，请注意以下计费方式：

* **存储成本**：除了存储的数据量，存储数据的成本将因存储层而异。 冷存储层每 GB 的成本比热存储层每 GB 的成本要低一些。

* 数据访问费用：对于冷存储层中的数据，需要按 GB 支付读取和写入方面的数据访问费用。

* **事务成本**：两个层都存在按事务收取的费用。 但是，冷存储层的每个事务成本比热存储层的每个事务成本要高一些。

* **异地复制数据传输成本**：这仅适用于配置了异地复制的帐户，包括 GRS 和 RA-GRS。 异地复制数据传输会产生每 GB 费用。

* **出站数据传输成本**：出站数据传输（传出 Azure 区域的数据）会按每 GB 产生带宽使用费，与通用存储帐户一致。

* 更改存储层：将存储层从“冷”更改为“热”会产生费用，每次转换的费用等于读取存储帐户中存在的所有数据的费用。 另一方面，将存储层从“热”更改为“冷”则免费。

> [!NOTE]
> 有关 Blob 存储帐户定价模型的更多详细信息，请参阅 [Azure Storage Pricing](https://azure.microsoft.com/pricing/details/storage/)（Azure 存储定价）页。 有关出站数据传输收费的更多详细信息，请参阅[数据传输定价详细信息](https://azure.microsoft.com/pricing/details/data-transfers/)页。

## <a name="quickstart"></a>快速入门

在本部分，我们使用 Azure 门户演示以下方案：

* 如何创建 Blob 存储帐户。
* 如何管理 Blob 存储帐户。

在以下示例中，不能将访问层设置为存档，因为该设置适用于整个存储帐户。 只能在特定 Blob 上设置存档。

### <a name="create-a-blob-storage-account-using-the-azure-portal"></a>使用 Azure 门户创建 Blob 存储帐户

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在“中心”菜单上，选择“新建” > “数据 + 存储” > “存储帐户”。

3. 输入存储帐户的名称。
   
    该名称必须全局唯一；在访问存储帐户中的对象时，该名称用作所需 URL 的一部分。  

4. 选择 **Resource Manager** 作为部署模型。
   
    分层存储只能用于 Resource Manager 存储帐户；建议对新资源使用该部署模型。 有关详细信息，请参阅 [Azure Resource Manager 概述](../../azure-resource-manager/resource-group-overview.md)。  

5. 在“帐户类型”下拉列表中，选择“ **Blob 存储**”。
   
    可以在这里选择存储帐户的类型。 分层存储不适用于通用存储，只适用于 Blob 存储类型帐户。     
   
    请注意，选择此项时，性能层将设置为“标准”。 分层存储不适用于“高级”性能层。

6. 选择存储帐户的复制选项：“LRS”、“GRS”或“RA-GRS”。 默认值为“RA-GRS”。
   
    LRS = 本地冗余存储；GRS = 异地冗余存储（2 个区域）；RA-GRS 是可以进行读取访问的异地冗余存储（2 个区域，可以对第二个区域进行读取访问）。
   
    有关 Azure 存储复制选项的详细信息，请参阅 [Azure 存储复制](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

7. 根据需要选择正确的存储层：将“访问层”设置为“冷”或“热”。 默认设置为“热”。 

8. 选择想在其中创建新存储帐户的订阅。

9. 指定新资源组或选择现有资源组。 有关资源组的详细信息，请参阅 [Azure Resource Manager 概述](../../azure-resource-manager/resource-group-overview.md)。

10. 选择存储帐户的区域。

11. 单击“创建”以创建存储帐户。

### <a name="change-the-storage-tier-of-a-blob-storage-account-using-the-azure-portal"></a>使用 Azure 门户更改 Blob 存储帐户的存储层

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 要导航到存储帐户，请先选择“所有资源”，然后选择存储帐户。

3. 在“设置”边栏选项卡中，单击“ **配置** ”以查看和/或更改帐户配置。

4. 根据需要选择正确的存储层：将“访问层”设置为“冷”或“热”。

5. 单击边栏选项卡顶部的“保存”。

> [!NOTE]
> 更改存储层可能会产生额外费用。 有关更多详细信息，请参阅[定价和计费](#pricing-and-billing)部分。


## <a name="evaluating-and-migrating-to-blob-storage-accounts"></a>评估 Blob 存储帐户和迁移到 Blob 存储帐户
本部分旨在帮助用户顺利转换成使用 Blob 存储帐户。 有两个用户方案：

* 已经有了一个通用存储帐户，想要使用适当的存储层来评估对 Blob 存储帐户所做的更改。
* 已经决定使用 Blob 存储帐户，或者已经有了一个这种帐户，想要评估一下是应使用热存储层还是冷存储层。

在这两种情况下，首要任务是评估一下对存储在 Blob 存储帐户中的数据进行存储和访问操作所需的成本，并将该成功与当前成本进行比较。

## <a name="evaluating-blob-storage-account-tiers"></a>评估 Blob 存储帐户层

若要估算在 Blob 存储帐户中存储和访问数据所需的费用，需评估现有的使用模式，或对预期的使用模式进行一个大致的估计。 一般情况下，需了解：

* 存储消耗 - 正在存储的数据量以及每月如何变化？

* 存储访问模式 - 从帐户读取以及写入到帐户的数据量（包括新数据）？ 使用了多少事务来进行数据访问？这些事务是什么类型的事务？

## <a name="monitoring-existing-storage-accounts"></a>监视现有存储帐户

要监视现有存储帐户并收集该数据，可以利用 Azure 存储分析进行日志记录，并为存储帐户提供度量数据。 存储分析可以存储的度量值包括有关 Blob 存储服务请求的聚合事务统计信息和容量数据，该存储服务适用于通用存储帐户和 Blob 存储帐户。 该数据存储在同一存储帐户中的已知表中。

如需更多详细信息，请参阅 [About Storage Analytics Metrics](https://msdn.microsoft.com/library/azure/hh343258.aspx)（关于存储分析指标）和 [Storage Analytics Metrics Table Schema](https://msdn.microsoft.com/library/azure/hh343264.aspx)（存储分析指标表架构）

> [!NOTE]
> Blob 存储帐户公开表服务终结点的目的只是为了存储和访问该帐户的度量值数据。

若要监视 Blob 存储服务的存储消耗情况，需启用容量指标。
启用此功能后，会每天为存储帐户的 Blob 服务记录容量数据，并将容量数据记录为表条目写入到同一存储帐户中的 *$MetricsCapacityBlob* 表。

若要监视 Blob 存储服务的数据访问模式，需在 API 级别启用每小时事务指标。 启用此功能后，会每小时聚合按 API 进行的事务，并将这些事务记录为表条目写入到同一存储帐户中的 *$MetricsHourPrimaryTransactionsBlob* 表。 在使用 RA-GRS 存储帐户时，$MetricsHourSecondaryTransactionsBlob 表会将事务记录到辅助终结点。

> [!NOTE]
> 如果有一个通用存储帐户，并且在其中存储了页 Blob、虚拟机磁盘以及块 Blob 数据和追加 Blob 数据，则不适合使用此估算过程。 这是因为，无法根据 Blob 的类型来区分块 Blob 和追加 Blob（均可迁移到 Blob 存储帐户）的容量和事务指标。

要对数据使用和访问模式进行准确的估算，建议为指标选择一个可代表日常使用情况的保留期，并进行推断。 一种选择是让度量值数据保留 7 天，每周收集一次数据，并在月底进行分析。 另一种选择是让指标数据保留 30 天，在 30 天到期以后再收集和分析数据。

如需详细了解如何启用、收集和查看指标数据，请参阅[启用 Azure 存储指标并查看指标数据](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

> [!NOTE]
> 存储、访问和下载分析数据也会收费，就像使用常规用户数据一样。

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>通过使用情况度量值来估算费用

### <a name="storage-costs"></a>存储成本

容量度量值表 $MetricsCapacityBlob 中行键为 'data' 的最新条目显示了用户数据所占用的存储容量。 容量度量值表 $MetricsCapacityBlob 中行键为 'analytics' 的最新条目显示了分析日志所占用的存储容量。

用户数据和分析日志（如果已启用）所占用的这个总容量就可以用来估算在存储帐户中存储数据的费用。 也可以使用相同方法来估算在通用存储帐户中存储块 Blob 和追加 Blob 的存储费用。

### <a name="transaction-costs"></a>事务成本

事务度量值表中某个 API 的所有条目的“ *TotalBillableRequests*”计得之和表示该特定 API 的事务总数。 例如，通过对行健为 'user;GetBlob' 的所有条目的计费请求进行求和可以算出一段给定时间中 'GetBlob' 事务的总数。

若要估算 Blob 存储帐户的事务费用，需将事务细分成三组，因为这些事务价格不一样。

* 写入事务，例如 'PutBlob'、'PutBlock'、'PutBlockList'、'AppendBlock'、'ListBlobs'、'ListContainers'、'CreateContainer'、'SnapshotBlob' 和 'CopyBlob'。
* 删除事务，例如 'DeleteBlob' 和 'DeleteContainer'。
* 所有其他事务。

若要估算通用存储帐户的事务费用，需聚合所有事务而不考虑操作/API。

### <a name="data-access-and-geo-replication-data-transfer-costs"></a>数据访问和异地复制数据传输费用

虽然存储分析不提供从存储帐户读取以及写入到存储帐户的数据量，但该数据量可以通过查看事务度量值表来大致进行估算。 事务度量值表中某个 API 的所有条目的 “ *TotalIngress* ”计得之和表示该特定 API 的传入数据的总量。 与此类似，“ *TotalEgress* ”计得之和表示传出数据的总量，以字节为单位。

若要估算 Blob 存储帐户的数据访问费用，需将事务细分成两组。 

* 从存储帐户检索的数据量可以通过查看主要为 'GetBlob' 和 'CopyBlob' 操作的 'TotalEgress' 计得之和来估算。

* 写入到存储帐户的数据量可以通过查看主要为 'PutBlob'、'PutBlock'、'CopyBlob' 和 'AppendBlock' 操作的 'TotalIngress' 计得之和来估算。

在使用 GRS 或 RA-GRS 存储帐户时，也可以通过所写入数据量的估算值来计算 Blob 存储帐户的异地复制数据传输费用。

> [!NOTE]
> 如需更详细的示例来了解如何计算热或冷存储层的使用费用，请参阅 *Azure Storage Pricing Page*（Azure 存储定价）页 中标题为“[什么是‘热’和‘冷’访问层以及如何确定应使用哪一个？](https://azure.microsoft.com/pricing/details/storage/)”的常见问题解答。
 
## <a name="migrating-existing-data"></a>迁移现有数据

Blob 存储帐户专用于仅存储块 blob 和追加 blob。 现有的通用存储帐户（用于存储表、队列、文件和磁盘以及 Blob）无法转换为 Blob 存储帐户。 若要使用存储层，需创建新的 Blob 存储帐户并将现有的数据迁移到新创建的帐户中。

可以使用以下方法从本地存储设备、第三方云存储提供程序或 Azure 中现有的通用存储帐户将现有数据迁移到 Blob 存储帐户：

### <a name="azcopy"></a>AzCopy

AzCopy 是一个 Windows 命令行实用程序，旨在实现高性能地将数据复制到 Azure 存储和从 Azure 存储中复制。 可以使用 AzCopy 从现有的通用存储帐户将数据复制到 Blob 存储帐户，或从本地存储设备将数据上传到 Blob 存储帐户。

有关更多详细信息，请参阅 [使用 AzCopy 命令行实用工具传输数据](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

### <a name="data-movement-library"></a>数据移动库

适用于 .NET 的 Azure 存储数据移动库基于为 AzCopy 提供技术支持的核心数据移动框架。 库旨在实现类似于 AzCopy 的高性能、可靠且简单的数据传输操作。 这样即可以本机模式充分利用应用程序中 AzCopy 提供的功能，无需运行和监视 AzCopy 的外部实例。

有关更多详细信息，请参阅 [适用于 .Net 的 Azure 存储数据移动库](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>REST API 或客户端库

可以创建自定义应用程序以使用其中一个 Azure 客户端库或 Azure 存储服务 REST API 将数据迁移到 Blob 存储帐户。 Azure 存储对多种语言和平台（如 .NET、Java、C++、Node.JS、PHP、Ruby 和 Python）提供了内容丰富的客户端库。 客户端库提供高级功能，如重试逻辑、日志记录和并行上传。 也可以直接针对可以由发出 HTTP/HTTPS 请求的任何语言调用的 REST API 进行开发。

有关更多详细信息，请参阅 [开始使用 Azure Blob 存储](storage-dotnet-how-to-use-blobs.md)。

> [!NOTE]
> 使用客户端加密进行加密的 blob 将存储与 blob 一起存储的加密相关元数据。 任何复制机制应确保保留 blob 元数据，尤其是与加密相关的元数据，这一点非常重要。 如果复制不包含此元数据的 Blob，则不能再次检索 Blob 内容。 有关加密相关元数据的详细信息，请参阅 [Azure 存储客户端加密](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。
 
## <a name="faq"></a>常见问题

1. **现有存储帐户是否仍然可用？**
   
    是，现有存储帐户仍然可用，并且定价或功能不变。  它们不能选择存储层，并且在将来也不会有分层功能。

2. **为什么以及何时应开始使用 Blob 存储帐户？**
   
    Blob 存储帐户专用于存储 blob，并且使我们能够引入新的以 blob 为中心的功能。 今后，Blob 存储帐户将是用于存储 blob 的推荐方法，因为未来的功能（如分级存储和分层）将基于此帐户类型引入。 但是，想要何时基于业务需求进行迁移，这由你决定。

3. **是否可以将现有存储帐户转换成 Blob 存储帐户？**
   
    不能。 Blob 存储帐户是一种不同的存储帐户，必须新建它并如上文所述迁移数据。

4. **是否可以将对象存储在同一帐户中的两个存储层中？**
   
    “访问层”属性指示在帐户级别设置的存储层的值，适用于该帐户中的所有对象。 但是，Blob 级分层（预览版）功能可以用于在特定 Blob 上设置访问层，且会替代帐户的访问层设置。 

5. **是否可以更改我的 Blob 存储帐户的存储层？**
   
    是的。 可以通过设置存储帐户上的“访问层”属性来更改存储层。 更改存储层适用于存储在帐户中的所有对象。 将存储层从“热”更改为“冷”不会产生任何费用，但如果从“冷”更改为“热”，则会产生读取帐户中所有数据所需的费用，按 GB 计算。

6. **我可以多频繁地更改我的 Blob 存储帐户的存储层？**
   
    虽然我们不会强制限制更改存储层的频率，但请注意，将存储层从“冷”更改为“热”可能产生大量费用。 我们不建议频繁地更改存储层。

7. 冷存储层中 Blob 的行为方式是否与热存储层中的不同？
   
    热存储层中的 Blob 具有与通用存储帐户中的 Blob 相同的延迟。 冷存储层中的 Blob 具有与通用存储帐户中的 Blob 类似的延迟（以毫秒为单位）。
   
    冷存储层中的 Blob 具有的可用性服务级别 (SLA) 比存储在热存储层中的 Blob 略低。 有关更多详细信息，请参阅 [存储的 SLA](https://azure.microsoft.com/support/legal/sla/storage)。

8. **是否可以将页 blob 和虚拟机磁盘存储在 Blob 存储帐户中？**
   
    Blob 存储帐户仅支持块 blob 和追加 blob，不支持页 blob。 Azure 虚拟机磁盘由页 blob 支持，因此不能使用 Blob 存储帐户来存储虚拟机磁盘。 但是，可以在 Blob 存储帐户中将虚拟机磁盘的备份存储为块 blob。

9. 是否需要更改现有应用程序才能使用 Blob 存储帐户？
   
    Blob 存储帐户与用于存储块 blob 和追加 blob 的通用存储帐户 100% API 一致。 只要应用程序使用的是块 Blob 或追加 Blob，并且使用[存储服务 REST API](https://msdn.microsoft.com/library/azure/dd894041.aspx) 的 2014-02-14 版或更高版本，则应用程序应该可以正常运行。 如果使用的协议版本较旧，则必须更新应用程序才能使用新版本，否则无法正常使用这两类存储帐户。 一般情况下，无论所使用的存储帐户类型，我们通常均建议使用最新版本。

10. 用户体验是否有变化？
    
    Blob 存储帐户非常类似于用于存储块 blob 和追加 blob 的通用存储帐户，并支持 Azure 存储的所有主要功能，包括高持续性和可用性、可伸缩性、性能和安全。 除特定于 Blob 存储帐户及其以上已列出的存储层的功能和限制以外，其余全部内容将保持不变。

## <a name="next-steps"></a>后续步骤

### <a name="evaluate-blob-storage-accounts"></a>评估 Blob 存储帐户

[按区域检查 Blob 存储帐户的可用性](https://azure.microsoft.com/regions/#services)

[通过启用 Azure 存储度量值来评估当前存储帐户的使用情况](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[按区域检查 Blob 存储定价](https://azure.microsoft.com/pricing/details/storage/)

[检查数据传输定价](https://azure.microsoft.com/pricing/details/data-transfers/)

### <a name="start-using-blob-storage-accounts"></a>开始使用 Blob 存储帐户

[开始使用 Azure Blob 存储](storage-dotnet-how-to-use-blobs.md)

[将数据移动到和移出 Azure 存储](../common/storage-moving-data.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[使用 AzCopy 命令行实用程序传输数据](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[浏览和了解存储帐户](http://storageexplorer.com/)