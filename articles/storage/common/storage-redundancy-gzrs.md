---
title: 使用地理区域冗余存储（GZRS）构建高度可用的 Azure 存储应用程序（预览版） |Microsoft Docs
description: 异地冗余存储（GZRS）结婚时会区域冗余存储（ZRS）的高可用性，并可防止异地冗余存储（GRS）提供的区域性中断。 GZRS 存储帐户中的数据将在主要区域中的 Azure 可用性区域之间进行复制，并且还会复制到辅助地理区域，以防止区域灾难。
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/13/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 321866279e076bfa77d1892e64deaf4b16c08366
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300650"
---
# <a name="build-highly-available-azure-storage-applications-with-geo-zone-redundant-storage-gzrs-preview"></a>使用地理区域冗余存储（GZRS）构建高度可用的 Azure 存储应用程序（预览版）

区域冗余存储（GZRS）（预览版）结婚时会[区域冗余存储（ZRS）](storage-redundancy-zrs.md)的高可用性，通过[异地冗余存储（GRS）](storage-redundancy-grs.md)提供的区域中断保护。 将 GZRS 存储帐户中的数据复制到主要区域中的三个[Azure 可用性区域](../../availability-zones/az-overview.md)，并将其复制到辅助地理区域，以防止区域灾难。 每个 Azure 区域与同一地理位置中另一个区域配对。 有关更多详细信息和异常，请参阅[文档](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。

使用 GZRS 存储帐户，可以在可用性区域变得不可用或无法恢复时继续读取和写入数据。 此外，在发生整个区域中断或发生主要区域无法恢复的灾难时，数据也是持久的。 GZRS 设计为在给定的一年内提供至少 99.99999999999999% （16个9）的对象持久性。 GZRS 还提供与 LRS、ZRS、GRS 或 RA-GRS 相同的 [可伸缩性目标](storage-scalability-targets.md)。 如果你的应用程序需要能够在主要区域发生灾难时读取数据，则可以选择启用对辅助区域中的数据的读取访问（使用读取访问权限异地冗余存储（GZRS））。

Microsoft 建议对需要一致性、持续性、高可用性、优秀性能和灾难恢复能力的应用程序使用 GZRS。 对于发生区域性灾难时对辅助区域的 "读取" 访问权限的其他安全，请为你的存储帐户启用 GZRS。

## <a name="about-the-preview"></a>关于此预览版

仅常规用途 v2 存储帐户支持 GZRS 和 GZRS。 有关存储帐户类型的详细信息，请参阅 [Azure 存储帐户概述](storage-account-overview.md)。 GZRS 和 GZRS 支持块 blob，不是 VHD 磁盘、文件、表和队列的页 blob。

GZRS 和 RA-GZRS 目前可用于以下区域：

- 北欧
- 西欧
- 美国东部
- 美国东部 2
- 美国中部

Microsoft 继续在其他 Azure 区域启用 GZRS 和 GZRS。 请定期查看 [Azure 服务更新](https://azure.microsoft.com/updates/) 页，以了解有关支持的区域的信息。

有关预览版定价的信息，请参阅 GZRS preview 定价 for [blob](https://azure.microsoft.com/pricing/details/storage/blobs)、 [文件](https://azure.microsoft.com/pricing/details/storage/files/)、 [队列](https://azure.microsoft.com/pricing/details/storage/queues/)和 [表](https://azure.microsoft.com/pricing/details/storage/tables/)。

> [!IMPORTANT]
> Microsoft 建议不要对生产工作负荷使用预览功能。

## <a name="how-gzrs-and-ra-gzrs-work"></a>GZRS 和 RA-GZRS 的工作方式

当数据写入到启用了 GZRS 或 GZRS 的存储帐户时，将首先在主区域跨三个可用性区域同步复制数据。 然后，将数据异步复制到数百英里之外的第二个区域。 向次要区域写入数据时，它会使用[本地冗余存储（LRS）](storage-redundancy-lrs.md)在该区域中以同步方式同步复制三次。

> [!IMPORTANT]
> 异步复制涉及到将数据写入主要区域和将数据复制到次要区域之间的延迟时间。 发生区域性灾难时，如果无法从主要区域中恢复数据，则尚未复制到次要区域的更改可能会丢失。

创建存储帐户时，可以指定如何复制该帐户中的数据，还可以指定该帐户的主要区域。 异地复制帐户的成对次要区域是根据主要区域确定的，不能更改。 有关 Azure 支持的区域的最新信息，请参阅 [业务连续性和灾难恢复（BCDR）：Azure 配对区域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。 有关使用 GZRS 或 GZRS 创建存储帐户的信息，请参阅[创建存储帐户](storage-quickstart-create-account.md)。

### <a name="use-ra-gzrs-for-high-availability"></a>使用 GZRS 实现高可用性

如果为存储帐户启用了 GZRS，则可以从辅助终结点以及存储帐户的主终结点读取数据。 辅助终结点将后缀 *–辅助* 点追加到帐户名称。 例如，如果 Blob 服务的主终结点是 `myaccount.blob.core.windows.net`，则辅助终结点是。 `myaccount-secondary.blob.core.windows.net` 存储帐户的访问密钥对于主终结点和辅助终结点是相同的。

若要在发生区域性服务中断时充分利用 GZRS，必须事先设计应用程序来处理这种情况。 您的应用程序应在主终结点上进行读取和写入，但当主要区域不可用时，将切换到使用辅助终结点。 有关使用 GZRS 进行高可用性设计的指南，请参阅 [使用 GZRS 或 GRS 设计高度可用的应用程序](https://docs.microsoft.com/en-us/azure/storage/common/storage-designing-ha-apps-with-ragrs)。

由于数据是以异步方式复制到辅助区域，因此次要区域通常位于主要区域后面。 若要确定哪些写入操作已复制到次要区域，应用程序需要检查存储帐户的上次同步时间。 在上次同步时间之前写入主要区域的所有写入操作都已成功复制到次要区域，这意味着它们可以从辅助区域读取。 在上次同步时间之后写入主要区域的任何写入操作都可能或尚未复制到次要区域，这意味着它们可能不可用于读取操作。

你可以使用 Azure PowerShell、Azure CLI 或 Azure 存储客户端库之一查询 "**上次同步时间**" 属性的值。 "**上次同步时间**" 属性是 GMT 日期/时间值。

有关 GZRS 的性能和可伸缩性的其他指导，请参阅[Microsoft Azure 存储性能和可伸缩性清单](storage-performance-checklist.md)。

### <a name="availability-zone-outages"></a>可用性区域中断

如果发生影响主要区域中的可用性区域的故障，应用程序可以使用该区域的其他可用性区域无缝地继续读取和写入存储帐户。 Microsoft 建议你在使用 GZRS 或 ZRS 时继续遵循暂时性故障处理的做法。 这些做法包括结合指数退让实施重试策略。

当某个可用性区域变得不可用时，Azure undertakes 网络更新（如 DNS 重定位）。 如果在更新完成之前访问数据，这些更新可能会影响你的应用程序。

### <a name="regional-outages"></a>区域性服务中断

如果故障影响整个主要区域，Microsoft 将首先尝试还原主要区域。 如果无法进行还原，Microsoft 将故障转移到次要区域，以便次要区域成为新的主要区域。 如果存储帐户启用了 GZRS，则为此方案设计的应用程序可在等待故障转移时从次要区域读取。 如果存储帐户未启用 GZRS，则在故障转移完成之前，应用程序将无法从辅助数据库读取。

> [!NOTE]
> GZRS 和 RA-GZRS 目前仅在美国东部地区处于预览阶段。 美国东部2尚不提供客户管理的帐户故障转移（预览版），因此，客户当前无法使用 GZRS 和 RA GZRS 帐户来管理帐户故障转移事件。 在预览期间，Microsoft 将管理影响 GZRS 和 GZRS 帐户的任何故障转移事件。

由于数据是以异步方式复制到辅助区域的，因此如果无法恢复主要区域，则影响主要区域的故障可能会导致数据丢失。 最近写入主要区域和最后一次写入次要区域之间的时间间隔称为恢复点目标（RPO）。 RPO 指示可以将数据恢复到的时间点。 Azure 存储的 RPO 通常小于15分钟，但目前没有 SLA 向次要区域复制数据所需的时间。

恢复时间目标（RTO）是衡量执行故障转移并使存储帐户恢复联机所花费的时间。 此度量值指示 Azure 通过将主要 DNS 条目更改为指向辅助位置来执行故障转移所需的时间。

## <a name="migrate-a-storage-account-to-gzrs-or-ra-gzrs"></a>将存储帐户迁移到 GZRS 或 GZRS

可以将任何现有存储帐户迁移到 GZRS 或 GZRS。 从现有的 ZRS 帐户迁移到 GZRS 或 RA-GZRS 非常简单，同时从 LRS、GRS 或 RA-GRS 帐户进行迁移更为简单。 以下各节介绍了如何在这两种情况下进行迁移。

### <a name="migrating-from-a-zrs-account"></a>从 ZRS 帐户迁移

若要将现有 ZRS 帐户转换为 GZRS，请使用[AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) cmdlet 更改帐户的 SKU。 请务必将占位符值替换为你自己的值：

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -SkuName "Standard_RAGZRS"
```

### <a name="migrating-from-an-lrs-grs-or-ra-grs-account"></a>从 LRS、GRS 或 GRS 帐户迁移

有两个选项可用于从 LRS、GRS 或 GRS 帐户迁移到 GZRS 或 GZRS：

- 可以手动将数据复制或移动到现有帐户中的新 GZRS 或 GZRS 帐户。
- 可以请求实时迁移。

#### <a name="perform-a-manual-migration"></a>执行手动迁移

如果需要在特定日期完成迁移，请考虑执行手动迁移。 手动迁移比实时迁移更灵活。 使用手动迁移可以控制时间。

若要将数据从现有帐户手动迁移到 GZRS 或 GZRS 帐户，请使用可以有效地复制数据的工具。 示例包括：

- 使用实用程序，如 AzCopy 或可靠的第三方工具。 有关 AzCopy 的信息，请参阅[AzCopy 入门](storage-use-azcopy-v10.md)。
- 如果你熟悉 Hadoop 或 HDInsight，请将源和目标存储帐户附加到群集。 接下来，使用 DistCp 等工具并行处理数据复制过程。
- 使用某个 Azure 存储客户端库生成自己的工具。

#### <a name="perform-a-live-migration"></a>执行实时迁移

手动迁移可能导致应用程序关闭。 如果应用程序需要高可用性，则还可以使用 Microsoft 提供的实时迁移选项。 实时迁移属于就地迁移，不停机。

在实时迁移期间，你可以在源存储帐户和目标存储帐户之间迁移数据时使用存储帐户。 在实时迁移过程中，你的帐户将继续满足其持久性和可用性的 SLA。 实时迁移不会造成停机或数据丢失。

只有常规用途 v2 帐户支持 GZRS/GZRS，因此，在将请求提交到 GZRS/RA-GZRS 之前，你必须将你的帐户升级到常规用途 v2。 有关详细信息，请参阅 [Azure 存储帐户概述](https://docs.microsoft.com/azure/storage/common/storage-account-overview) 和 [升级到常规用途 v2 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)。

迁移完成后，存储帐户的复制设置将更新为**异地区域冗余存储（GZRS）** 或**读取访问区域冗余存储（GZRS）** 。 服务终结点、访问密钥、共享访问签名（SAS）和任何其他帐户配置选项保持不变并保持不变。

请注意实时迁移的以下限制：

- 尽管 Microsoft 会尽快处理实时迁移的请求，但无法保证实时迁移何时完成。 如果需要将数据迁移到 GZRS 或 GZRS 的特定日期，Microsoft 建议改为执行手动迁移。 通常，帐户中的数据越多，迁移这些数据所需的时间就越长。
- 帐户必须包含数据。
- 只能在同一区域中迁移数据。
- 只有标准存储帐户类型才支持实时迁移。 高级存储帐户必须手动迁移。
- 不支持从 GZRS 或 GZRS 帐户实时迁移到 LRS、GRS 或 RA-GRS 帐户。 需要手动将数据移动到新的或现有的存储帐户。
- 可以请求从 GRS 到 GZRS 的实时迁移。 但是，不支持从 GRS 迁移到 GZRS。 在这种情况下，你必须请求将实时迁移到 GZRS，然后手动转换存储帐户以使用 GZRS。
- 托管磁盘仅支持 LRS，不能迁移到 GZRS 或 GZRS。 有关与可用性集的集成，请参阅 [Azure 托管磁盘简介](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets)。
- 可以将标准 SSD 托管磁盘的快照和映像存储在标准 HDD 存储上，并在 [LRS、ZRS、GZRS 和 RA-GZRS 选项之间进行选择](https://azure.microsoft.com/pricing/details/managed-disks/)。
- GZRS 不支持包含大型文件共享的帐户。

若要请求实时迁移，请使用 [Azure 门户](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。 从门户中，选择要迁移到 GZRS 或 GZRS 的存储帐户，并按照以下说明操作：

1. 选择 " **新建支持请求**"。
2. 根据帐户信息完成 **基本** 操作。 在 " **服务** " 部分中，选择 " **存储帐户管理** " 并指定要迁移的帐户。
3. 选择“下一步”。 ****
4. 在 " **问题** " 部分指定以下值：
    - **严重性**：保留默认值。
    - **问题类型**：选择 " **数据迁移**"。
    - **类别**：选择 " **迁移到区域内的（RA-） GZRS**"。
    - **标题**：键入描述性标题，例如 " **（RA-） GZRS 帐户迁移**"。
    - **详细信息**：在 " **详细信息** " 框中键入其他详细信息，例如，"我想要迁移到\_区域中\_的 GZRS [LRS，GRS]"。 或 "我想要从\_ \_区域中的 [LRS，ra-GRS] 迁移到 GZRS。"
5. 选择“下一步”。 ****
6. 验证 " **联系人信息** " 边栏选项卡上的联系人信息是否正确。
7. 选择“创建” **** 。

支持代表将与你联系以提供帮助。

## <a name="see-also"></a>请参阅

- [Azure 存储复制](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
- [本地冗余存储 (LRS)：Azure 存储的低成本数据冗余](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)
- [区域冗余存储 (ZRS)：高度可用的 Azure 存储应用程序](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) 
