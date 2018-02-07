---
title: "Azure 存储帐户选项 | Microsoft Docs"
description: "了解使用 Azure 存储的选项。"
services: storage
author: jirwin
manager: jwillis
ms.service: storage
ms.workload: storage
ms.topic: get-started-article
ms.date: 01/17/2018
ms.author: jirwin
ms.openlocfilehash: bdbcdc7d46d5395b28cf9ba7066703ce5da900a5
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2018
---
# <a name="azure-storage-account-options"></a>Azure 存储帐户选项

## <a name="overview"></a>概述
Azure 存储提供三个不同的帐户选项，支持不同的定价和功能。 在创建存储帐户之前，需考虑到这些差异，以便确定最适合应用程序的选项。 三个不同的存储帐户选项为：

* **常规用途 v2 (GPv2)** 帐户 
* **常规用途 v1 (GPv1)** 帐户
* **Blob 存储**帐户

以下部分更详细地介绍了每类帐户：

## <a name="storage-account-options"></a>存储帐户选项

### <a name="general-purpose-v2"></a>常规用途 v2

常规用途 v2 (GPv2) 帐户是存储帐户，支持适用于 Blob、文件、队列和表的所有最新功能。 GPv2 帐户支持在 GPv1 和 Blob 存储帐户中受支持的所有 API 和功能， 同时还支持在这些帐户类型中提供的持续性、可用性、可伸缩性和性能。 设计 GPv2 帐户定价的目的是为了提供最低的单 GB 价格和具有行业竞争优势的事务价格。

可以使用 PowerShell 或 Azure CLI 将 GPv1 帐户升级为 GPv2 帐户。 

对于 GPv2 存储帐户中的块 Blob，可以根据访问模式在帐户级别选择热或冷存储层，或者在 Blob 级别选择热、冷或存档层。 可以将经常访问的、不常访问的和很少访问的数据分别存储在热、冷和存档存储层中，以便优化成本。 

GPv2 存储帐户在帐户级别公开“访问层”属性，可以将默认存储帐户层指定为“热”或“冷”。 默认存储帐户层可以应用到任何 Blob，前提是该 Blob 没有在 Blob 级别设置任何显式层。 如果数据的使用模式有所更改，也可以随时在这些存储层之间切换。 **存档层**仅适用于 Blob 级别。

> [!NOTE]
> 更改存储层可能会产生额外费用。 有关详细信息，请参阅[定价和计费](#pricing-and-billing)部分。
>
> 大多数情况下，Microsoft 建议使用常规用途 v2 存储帐户而不是 Blob 存储帐户。

### <a name="upgrade-a-storage-account-to-gpv2"></a>将存储帐户升级为 GPv2

用户随时可以使用 PowerShell 或 Azure CLI 将 GPv1 帐户升级为 GPv2 帐户。 此更改无法撤消，不允许其他更改。

#### <a name="upgrade-with-powershell"></a>使用 PowerShell 进行升级

若要使用 PowerShell 将 GPv1 帐户升级为 GPv2 帐户，请先更新 PowerShell，以便使用最新版本的 **AzureRm.Storage** 模块。 请参阅[安装和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)，了解如何安装 PowerShell。 然后调用以下命令来升级帐户，使用自己的资源组和存储帐户的名称来代替相应项：

```powershell
Set-AzureRmStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2
```

#### <a name="upgrade-with-azure-cli"></a>使用 Azure CLI 进行升级

若要使用 Azure CLI 将 GPv1 帐户升级为 GPv2 帐户，请先安装最新版的 Azure CLI。 请参阅 [Install the Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)（安装 Azure CLI 2.0），了解如何安装 CLI。 然后调用以下命令来升级帐户，使用自己的资源组和存储帐户的名称来代替相应项：

```cli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2
```` 

### <a name="general-purpose-v1"></a>常规用途 v1

常规用途 v1 (GPv1) 帐户可以访问所有 Azure 存储服务，但可能没有最新功能，其单 GB 定价也可能不是最低的。 例如，GPv1 不支持冷存储和存档存储。 GPv1 事务定价较低，因此改动率或读取率高的工作负荷适合此帐户类型。

常规用途 v1 (GPv1) 存储帐户是最老式的存储帐户，也是能够在经典部署模型中使用的唯一帐户类型。 

### <a name="blob-storage-accounts"></a>Blob 存储帐户

Blob 存储帐户支持 GPv2 帐户所支持的所有块 Blob 功能，但其局限性是只支持块 Blob。 定价大部分与常规用途 v2 帐户的定价类似。 客户应先了解 Blob 存储帐户和 GPv2 帐户的定价差异，然后再考虑是否升级到 GPv2。 此升级不能撤消。

将 Blob 存储帐户升级为 GPv2 的功能即将推出。

> [!NOTE]
> Blob 存储帐户仅支持块 blob 和追加 blob，不支持页 blob。

## <a name="recommendations"></a>建议

有关存储帐户的详细信息，请参阅[关于 Azure 存储帐户](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

对于仅需块 Blob 或追加 Blob 存储的应用程序，建议使用 GPv2 存储帐户，充分利用分层存储的差异化定价模型。 但是，在某些情况下可能需要使用 GPv1，例如：

* 仍需使用经典部署模型。 Blob 存储帐户只有通过 Azure 资源管理器部署模型才可用。

* 所使用的事务量较大或异地复制带宽较高，这二者在 GPv2 和 Blob 存储帐户中的成本比 GPv1 中的要高，并且存储不够大，无法充分利用单 GB 存储成本较低这一优势。

* 使用了早于 2014-02-14 的 [存储服务 REST API](https://msdn.microsoft.com/library/azure/dd894041.aspx) 的版本或使用了版本低于 4.x 的客户端库，并且无法升级应用程序。

> [!NOTE]
> 目前，所有 Azure 区域都支持 Blob 存储帐户。

## <a name="pricing-and-billing"></a>定价和计费
所有存储帐户使用的定价模型都适用于 Blob 存储，具体取决于每个 Blob 的层。 使用存储帐户时，需要考虑到以下计费因素：

* **存储成本**：除了存储的数据量，存储数据的成本将因存储层而异。 层越冷，单 GB 成本越低。

* **数据访问成本**：层越冷，数据访问费用越高。 对于冷存储层和存档存储层中的数据，需要按 GB 支付读取方面的数据访问费用。

* **事务成本**：层越冷，每个层的按事务收费越高。

* **异地复制数据传输成本**：此费用仅适用于配置了异地复制的帐户，包括 GRS 和 RA-GRS。 异地复制数据传输会产生每 GB 费用。

* **出站数据传输成本**：出站数据传输（传出 Azure 区域的数据）会按每 GB 产生带宽使用费，与通用存储帐户一致。

* **更改存储层**：将帐户存储层从“冷”更改为“热”会产生费用，费用等于读取存储帐户中存在的所有数据的费用。 但是，将帐户存储层从热更改为冷产生的费用则相当于将所有数据写入冷层（仅限 GPv2 帐户）。

> [!NOTE]
> 有关 Blob 存储帐户定价模型的详细信息，请参阅 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)页。 有关出站数据传输收费的详细信息，请参阅[数据传输定价详细信息](https://azure.microsoft.com/pricing/details/data-transfers/)页。

## <a name="quickstart-scenarios"></a>快速入门方案

本部分使用 Azure 门户演示以下方案：

* 如何创建 GPv2 存储帐户。
* 如何将 GPv1 或 Blob 存储帐户转换为 GPv2 存储帐户。
* 如何在 GPv2 存储帐户中设置帐户和 Blob 层。

在以下示例中，不能将访问层设置为存档，因为该设置适用于整个存储帐户。 只能在特定 Blob 上设置存档。

### <a name="create-a-gpv2-storage-account-using-the-azure-portal"></a>使用 Azure 门户创建 GPv2 存储帐户

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在“中心”菜单上，选择“新建” > “数据 + 存储” > “存储帐户”。

3. 输入存储帐户的名称。

    该名称必须全局唯一；在访问存储帐户中的对象时，该名称用作所需 URL 的一部分。  

4. 选择 **Resource Manager** 作为部署模型。

    分层存储只能用于资源管理器存储帐户；建议对新资源使用资源管理器部署模型。 有关详细信息，请参阅 [Azure 资源管理器概述](../../azure-resource-manager/resource-group-overview.md)。  

5. 在“帐户类型”下拉列表中，选择“常规用途 v2”。

    选择 GPv2 时，性能层设置为“标准”。 分层存储不适用于“高级”性能层。

6. 选择存储帐户的复制选项：**LRS**、**ZRS**、**GRS** 或 **RA-GRS**。 默认值为“RA-GRS”。

    LRS = 本地冗余存储；ZRS = 区域冗余存储；GRS = 异地冗余存储（两个区域）；RA-GRS 是可以进行读取访问的异地冗余存储（两个区域，可以对第二个区域进行读取访问）。

    有关 Azure 存储复制选项的更多详细信息，请参阅 [Azure 存储复制](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

7. 根据需要选择正确的存储层：将“访问层”设置为“冷”或“热”。 默认设置为“热”。

8. 选择想在其中创建新存储帐户的订阅。

9. 指定新资源组或选择现有资源组。 有关资源组的详细信息，请参阅 [Azure 资源管理器概述](../../azure-resource-manager/resource-group-overview.md)。

10. 选择存储帐户的区域。

11. 单击“创建”以创建存储帐户。

### <a name="convert-a-gpv1-account-to-a-gpv2-storage-account-using-the-azure-portal"></a>使用 Azure 门户将 GPv1 转换为 GPv2 存储帐户

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 要导航到存储帐户，请先选择“所有资源”，然后选择存储帐户。

3. 在“设置”部分单击“配置”。

4. 在“帐户类型”下单击“升级”。

5. 在“确认升级”下键入帐户名称。 

5. 单击边栏选项卡底部的“升级”。

### <a name="change-the-storage-tier-of-a-gpv2-storage-account-using-the-azure-portal"></a>使用 Azure 门户更改 GPv2 存储帐户的存储层

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 要导航到存储帐户，请先选择“所有资源”，然后选择存储帐户。

3. 在“设置”边栏选项卡中，单击“ **配置** ”以查看和/或更改帐户配置。

4. 根据需要选择正确的存储层：将“访问层”设置为“冷”或“热”。

5. 单击边栏选项卡顶部的“保存”。

### <a name="change-the-storage-tier-of-a-blob-using-the-azure-portal"></a>使用 Azure 门户更改 Blob 的存储层

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 若要导航到存储帐户中的 Blob，请依次选择“所有资源”、存储帐户、容器、Blob。

3. 在 Blob 属性边栏选项卡中单击“访问层”下拉菜单，选择“热”、“冷”或“存档”存储层。

5. 单击边栏选项卡顶部的“保存”。

> [!NOTE]
> 更改存储层可能会产生额外费用。 有关详细信息，请参阅[定价和计费](#pricing-and-billing)部分。


## <a name="evaluating-and-migrating-to-gpv2-storage-accounts"></a>评估 GPv2 存储帐户和迁移到 GPv2 存储帐户
本部分旨在帮助用户顺利地从使用 GPv1 存储帐户转换成使用 GPv2 存储帐户。 有两个用户方案：

* 已经有了一个 GPv1 存储帐户，想要使用适当的存储层来评估对 GPv2 存储帐户所做的更改。
* 已经决定使用 GPv2 存储帐户，或者已经有了一个这种帐户，想要评估一下是应使用热存储层还是冷存储层。

在这两种情况下，首要任务是评估一下对存储在 GPv2 存储帐户中的数据进行存储和访问操作所需的成本，并将该成本与当前成本进行比较。

## <a name="evaluating-gpv2-storage-account-tiers"></a>评估 GPv2 存储帐户层

若要估算在 GPv2 存储帐户中存储和访问数据所需的成本，需评估现有的使用模式，或对预期的使用模式进行一个大致的估计。 一般情况下，需了解：

* 存储消耗 - 正在存储的数据量以及每月如何变化？

* 存储访问模式 - 从帐户读取以及写入到帐户的数据量（包括新数据）？ 使用了多少事务来进行数据访问？这些事务是什么类型的事务？

## <a name="monitoring-existing-storage-accounts"></a>监视现有存储帐户

若要监视现有存储帐户并收集该数据，可以利用 Azure 存储分析进行日志记录，并为存储帐户提供指标数据。 存储分析可存储一些指标，这些指标包括有关存储服务请求的聚合事务统计信息和容量数据，适用于 GPv1、GPv2 和 Blob 存储帐户类型。 该数据存储在同一存储帐户中的已知表中。

有关详细信息，请参阅 [About Storage Analytics Metrics](https://msdn.microsoft.com/library/azure/hh343258.aspx)（关于存储分析指标）和 [Storage Analytics Metrics Table Schema](https://msdn.microsoft.com/library/azure/hh343264.aspx)（存储分析指标表架构）

> [!NOTE]
> Blob 存储帐户公开表服务终结点的目的只是为了存储和访问该帐户的指标数据。 区域冗余存储 (ZRS) 帐户支持收集指标数据，而 ZRS 经典存储帐户则不支持。 有关 ZRS 的详细信息，请参阅[区域冗余存储](storage-redundancy.md#zone-redundant-storage)。 

若要监视 Blob 存储的存储消耗情况，需启用容量指标。
启用此功能后，会每天为存储帐户的 Blob 服务记录容量数据，并将容量数据记录为表条目写入到同一存储帐户中的 $MetricsCapacityBlob 表。

若要监视 Blob 存储的数据访问模式，需通过 API 启用每小时事务指标。 启用每小时事务指标后，会每小时聚合按 API 进行的事务，并将这些事务记录为表条目写入到同一存储帐户中的 *$MetricsHourPrimaryTransactionsBlob* 表。 在使用 RA-GRS 存储帐户时，$MetricsHourSecondaryTransactionsBlob 表会将事务记录到辅助终结点。

> [!NOTE]
> 如果有一个常规用途存储帐户，在其中存储了页 Blob、虚拟机磁盘、队列、文件、表以及块 Blob 数据和追加 Blob 数据，则不适合使用此估算过程。 容量数据不区分块 Blob 与其他类型，因此不会给出其他数据类型的容量数据。 如果使用这些类型，则也可查看最新账单上的数量，作为一种替代方法。

若要对数据使用和访问模式进行准确的估算，建议为指标选择一个可代表日常使用情况的保留期，并进行推断。 一种选择是让指标数据保留七天，每周收集一次数据，并在月底进行分析。 另一种选择是让指标数据保留 30 天，在 30 天到期以后再收集和分析数据。

如需详细了解如何启用、收集和查看指标数据，请参阅[启用 Azure 存储指标并查看指标数据](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

> [!NOTE]
> 存储、访问和下载分析数据也会收费，就像使用常规用户数据一样。

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>通过使用情况度量值来估算费用

#### <a name="storage-costs"></a>存储成本

容量度量值表 $MetricsCapacityBlob 中行键为 'data' 的最新条目显示了用户数据所占用的存储容量。 容量度量值表 $MetricsCapacityBlob 中行键为 'analytics' 的最新条目显示了分析日志所占用的存储容量。

用户数据和分析日志（如果已启用）所占用的这个总容量就可以用来估算在存储帐户中存储数据的费用。 也可以使用相同方法来估算 GPv1 存储帐户中的存储成本。

#### <a name="transaction-costs"></a>事务成本

事务度量值表中某个 API 的所有条目的“ *TotalBillableRequests*”计得之和表示该特定 API 的事务总数。 例如，通过对行健为 'user;GetBlob' 的所有条目的计费请求进行求和可以算出一段给定时间中 'GetBlob' 事务的总数。

若要估算 Blob 存储帐户的事务费用，需将事务细分成三组，因为这些事务价格不一样。

* 写入事务，例如 'PutBlob'、'PutBlock'、'PutBlockList'、'AppendBlock'、'ListBlobs'、'ListContainers'、'CreateContainer'、'SnapshotBlob' 和 'CopyBlob'。
* 删除事务，例如 'DeleteBlob' 和 'DeleteContainer'。
* 所有其他事务。

若要估算 GPv1 存储帐户的事务成本，需聚合所有事务而不考虑操作/API。

### <a name="data-access-and-geo-replication-data-transfer-costs"></a>数据访问和异地复制数据传输费用

虽然存储分析不提供从存储帐户读取以及写入到存储帐户的数据量，但该数据量可以通过查看事务度量值表来大致进行估算。 事务度量值表中某个 API 的所有条目的 “ *TotalIngress* ”计得之和表示该特定 API 的传入数据的总量。 与此类似，“ *TotalEgress* ”计得之和表示传出数据的总量，以字节为单位。

若要估算 Blob 存储帐户的数据访问费用，需将事务细分成两组。

* 从存储帐户检索的数据量可以通过查看主要为 'GetBlob' 和 'CopyBlob' 操作的 'TotalEgress' 计得之和来估算。

* 写入到存储帐户的数据量可以通过查看主要为 'PutBlob'、'PutBlock'、'CopyBlob' 和 'AppendBlock' 操作的 'TotalIngress' 计得之和来估算。

在使用 GRS 或 RA-GRS 存储帐户时，也可以通过所写入数据量的估算值来计算 Blob 存储帐户的异地复制数据传输费用。

> [!NOTE]
> 如需更详细的示例来了解如何计算热或冷存储层的使用费用，请参阅 *Azure Storage Pricing Page*（Azure 存储定价）页 中标题为“[什么是‘热’和‘冷’访问层以及如何确定应使用哪一个？](https://azure.microsoft.com/pricing/details/storage/)”的常见问题解答。

## <a name="migrating-existing-data"></a>迁移现有数据

可以轻松地将 GPv1 帐户升级为 GPv2，不会造成停机或 API 更改，也不需迁移数据。 因此，建议将 GPv1 帐户迁移到 GPv2 帐户而非 Blob 存储帐户。

但是，如果需要迁移到 Blob 存储帐户，可以按以下说明操作。

Blob 存储帐户专用于仅存储块 blob 和追加 blob。 现有的通用存储帐户（用于存储表、队列、文件和磁盘以及 Blob）无法转换为 Blob 存储帐户。 若要使用存储层，需创建新的 Blob 存储帐户并将现有的数据迁移到新创建的帐户中。

可以使用以下方法从本地存储设备、第三方云存储提供程序或 Azure 中现有的通用存储帐户将现有数据迁移到 Blob 存储帐户：

### <a name="azcopy"></a>AzCopy

AzCopy 是一个 Windows 命令行实用程序，旨在实现高性能地将数据复制到 Azure 存储和从 Azure 存储中复制。 可以使用 AzCopy 从现有的通用存储帐户将数据复制到 Blob 存储帐户，或从本地存储设备将数据上传到 Blob 存储帐户。

有关详细信息，请参阅[使用 AzCopy 命令行实用工具传输数据](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

### <a name="data-movement-library"></a>数据移动库

适用于 .NET 的 Azure 存储数据移动库基于为 AzCopy 提供技术支持的核心数据移动框架。 库旨在实现类似于 AzCopy 的高性能、可靠且简单的数据传输操作。 可以通过它以本机模式充分利用应用程序中 AzCopy 提供的功能，无需运行和监视 AzCopy 的外部实例。

有关详细信息，请参阅[适用于 .Net 的 Azure 存储数据移动库](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>REST API 或客户端库

可以创建自定义应用程序以使用其中一个 Azure 客户端库或 Azure 存储服务 REST API 将数据迁移到 Blob 存储帐户。 Azure 存储对多种语言和平台（如 .NET、Java、C++、Node.JS、PHP、Ruby 和 Python）提供了内容丰富的客户端库。 客户端库提供高级功能，如重试逻辑、日志记录和并行上传。 也可以直接针对可以由发出 HTTP/HTTPS 请求的任何语言调用的 REST API 进行开发。

有关详细信息，请参阅 [Azure Blob 存储入门](../blobs/storage-dotnet-how-to-use-blobs.md)。

> [!NOTE]
> 使用客户端加密进行加密的 blob 将存储与 blob 一起存储的加密相关元数据。 任何复制机制应确保保留 Blob 元数据，尤其是与加密相关的元数据，这一点很重要。 如果复制不包含此元数据的 Blob，则不能再次检索 Blob 内容。 有关加密相关元数据的详细信息，请参阅 [Azure 存储客户端加密](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

## <a name="faq"></a>常见问题

**现有存储帐户是否仍然可用？**

是，现有存储帐户 (GPv1) 仍然可用，并且定价或功能不变。 GPv1 帐户不能选择存储层，并且在将来也不会有分层功能。

**为何应使用 GPv2 存储帐户？何时开始使用？**

GPv2 存储帐户的特点是，单 GB 存储成本最低，而且事务和数据访问成本极具行业竞争力。 今后，GPv2 存储帐户将是用于存储 Blob 的推荐方法，因为未来的功能（如更改通知）将基于此帐户类型引入。 但是，何时根据业务需求进行升级取决于你自己。 例如，可以选择在升级前先优化事务模式。

不支持从 GPv2 降级，因此，在将帐户升级为 GPv2 之前，请考虑所有价格方面的影响。

**是否可以将现有存储帐户升级为 GPv2 存储帐户？**

是的。 可以在门户中或者使用 PowerShell 或 CLI 轻松地将 GPv1 升级为 GPv2。 可以使用 PowerShell 或 CLI 将 Blob 存储帐户升级为 GPv2。 在门户中将 Blob 存储帐户升级为 GPv2 的功能即将推出。

不支持从 GPv2 降级，因此，在将帐户升级为 GPv2 之前，请考虑所有价格方面的影响。

**是否可以将对象存储在同一帐户中的两个存储层中？**

是的。 在帐户级别设置的“访问层”属性是一个默认层，适用于该帐户中没有显式设置层的所有对象。 但是，Blob 级别分层允许在对象级别设置访问层，不管该帐户上的访问层设置如何。 这三个存储层（热、冷或存档）中任何一层的 Blob 都可以存在于同一帐户中。

**是否可以更改 GPv2 存储帐户的存储层？**

是的，可以通过设置存储帐户上的“访问层”属性来更改帐户存储层。 更改帐户存储层适用于存储在帐户中的所有对象，前提是该帐户没有显式设置层。 将存储层从热更改为冷会产生写入操作次数（以 10,000 次为单位）收费（仅限 GPv2 存储帐户），而从冷更改为热则会产生读取操作次数（以 10,000 次为单位）和数据检索量（以 GB 为单位）收费，因为需要读取帐户中的所有数据。

**我可以多频繁地更改我的 Blob 存储帐户的存储层？**

虽然我们不会强制限制更改存储层的频率，但请注意，将存储层从“冷”更改为“热”可能产生大量费用。 不建议频繁地更改存储层。

冷存储层中 Blob 的行为方式是否与热存储层中的不同？

GPv2 和 Blob 存储帐户的热存储层中 Blob 的延迟与 GPv1 存储帐户中 Blob 的延迟相同。 冷存储层中 Blob 的延迟（以毫秒为单位）与热层中 Blob 的延迟类似。 存档存储层中的 Blob 有数小时的延迟。

冷存储层中的 Blob 具有的可用性服务级别 (SLA) 比存储在热存储层中的 Blob 略低。 有关详细信息，请参阅[存储的 SLA](https://azure.microsoft.com/support/legal/sla/storage)。

**是否可以将页 blob 和虚拟机磁盘存储在 Blob 存储帐户中？**

不会。 Blob 存储帐户仅支持块 blob 和追加 blob，不支持页 blob。 Azure 虚拟机磁盘由页 blob 支持，因此不能使用 Blob 存储帐户来存储虚拟机磁盘。 但是，可以在 Blob 存储帐户中将虚拟机磁盘的备份存储为块 blob。 如果需要考虑使用 GPv2 来代替 Blob 存储帐户，则这是原因之一。

**是否需要更改现有应用程序才能使用 GPv2 存储帐户？**

GPv2 存储帐户与 GPv1 和 Blob存储帐户具有 100% 的 API 一致性。 只要应用程序使用的是块 Blob 或追加 Blob，并且使用[存储服务 REST API](https://msdn.microsoft.com/library/azure/dd894041.aspx) 的 2014-02-14 版或更高版本，则应用程序应该可以正常运行。 如果使用的协议版本较旧，则必须更新应用程序才能使用新版本，否则无法正常使用这两类存储帐户。 一般情况下，无论所使用的存储帐户类型，我们通常均建议使用最新版本。

GPv2 在事务和带宽方面的价格通常高于 GPv1。 因此，在升级之前可能需要优化事务模式，避免总费用提高。

**用户体验是否有变化？**

GPv2 存储帐户非常类似于 GPv1 存储帐户，并支持 Azure 存储的所有主要功能，包括高持续性和可用性、可伸缩性、性能和安全。 升级到 GPv2 或 Blob 存储时，除特定于 Blob 存储帐户及其存储层（见上）的功能和限制以外，其余内容保持不变。

## <a name="next-steps"></a>后续步骤

### <a name="evaluate-blob-storage-accounts"></a>评估 Blob 存储帐户

[按区域检查 Blob 存储帐户的可用性](https://azure.microsoft.com/regions/#services)

[通过启用 Azure 存储度量值来评估当前存储帐户的使用情况](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[按区域检查 Blob 存储定价](https://azure.microsoft.com/pricing/details/storage/)

[检查数据传输定价](https://azure.microsoft.com/pricing/details/data-transfers/)

### <a name="start-using-gpv2-storage-accounts"></a>开始使用 GPv2 存储帐户

[开始使用 Azure Blob 存储](../blobs/storage-dotnet-how-to-use-blobs.md)

[将数据移动到和移出 Azure 存储](../common/storage-moving-data.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[使用 AzCopy 命令行实用程序传输数据](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[浏览和了解存储帐户](http://storageexplorer.com/)
