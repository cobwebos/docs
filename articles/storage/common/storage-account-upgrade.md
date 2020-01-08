---
title: 升级到常规用途 v2 存储帐户
titleSuffix: Azure Storage
description: 升级到常规用途 v2 存储帐户。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: e7228556659a7a4a9f01ea31f61e75b5e870d50c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457432"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>升级到常规用途 v2 存储帐户

常规用途 v2 存储帐户支持最新的 Azure 存储功能，并整合了常规用途 v1 和 Blob 存储帐户的所有功能。 建议将常规用途 v2 帐户用于大多数存储方案。 常规用途 v2 帐户为 Azure 存储提供最低的每 GB 容量价格，以及具有行业竞争力的事务处理价格。 常规用途 v2 帐户支持 "热"、"冷" 或 "存档" 之间的 "热"、"冷" 和 "blob" 级别分层的默认帐户访问层。

从常规用途 v1 存储帐户或 Blob 存储帐户升级到常规用途 v2 存储帐户很简单。 可以使用 Azure 门户、PowerShell 或 Azure CLI 进行升级。

> [!IMPORTANT]
> 将常规用途 v1 或 Blob 存储帐户升级为常规用途 v2 是永久性的，无法撤消。

# <a name="portaltabazure-portal"></a>[门户](#tab/azure-portal)

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 导航到存储帐户。
3. 在“设置”部分单击“配置”。
4. 在“帐户类型”下单击“升级”。
5. 在“确认升级”下键入帐户名称。
6. 单击边栏选项卡底部的“升级”。

    ![升级帐户种类](../blobs/media/storage-blob-account-upgrade/upgrade-to-gpv2-account.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

若要使用 PowerShell 将常规用途 v1 帐户升级为常规用途 v2 帐户，请先更新 PowerShell，以便使用最新版本的 **Az.Storage** 模块。 请参阅[安装和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)，了解如何安装 PowerShell。

接下来，调用以下命令来升级帐户，并替换资源组名称、存储帐户名称和所需的帐户访问层。

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2 -AccessTier <Hot/Cool>
```
# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 将常规用途 v1 帐户升级为常规用途 v2 帐户，请先安装最新版的 Azure CLI。 请参阅 [Install the Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)（安装 Azure CLI 2.0），了解如何安装 CLI。

接下来，调用以下命令来升级帐户，并替换资源组名称、存储帐户名称和所需的帐户访问层。

```cli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2 --access-tier=<Hot/Cool>
```

---

## <a name="specify-an-access-tier-for-blob-data"></a>指定 Blob 数据的访问层

常规用途 v2 帐户支持所有 Azure 存储服务和数据对象，但访问层仅适用于 Blob 存储中的块 blob。 升级到常规用途 v2 存储帐户时，可以指定 "热" 或 "冷" 的默认帐户访问层，这表示将上传 blob 数据的默认层，就像未指定各个 blob 访问层参数一样。

使用 Blob 访问层可以根据预期的使用模式选择最经济高效的存储。 块 blob 可以存储在热、冷或存档层中。 有关访问层的详细信息，请参阅 [Azure Blob 存储：热存储层、冷存储层和存档存储层](../blobs/storage-blob-storage-tiers.md)。

默认情况下，会在 "热访问" 层中创建新的存储帐户，并将常规用途 v1 存储帐户升级到 "热" 或 "冷" 帐户层。 如果升级时未指定帐户访问层，则默认情况下，它将升级到 "热"。 如果要探索用于升级的访问层，请考虑当前的数据使用方案。 可以通过两个典型的用户方案迁移到常规用途 v2 帐户：

* 已经有了一个常规用途 v1 存储帐户，想要使用适合 Blob 数据的存储访问层来评估对常规用途 v2 存储帐户所做的升级。
* 你已决定使用常规用途 v2 存储帐户，或者已经有了一个，并想要评估是否应使用适用于 blob 数据的热或冷存储访问层。

在这两种情况下，首要任务都是评估对存储在常规用途 v2 存储帐户中的数据进行存储、访问和操作所需的成本，并将该成本与当前成本进行比较。

## <a name="pricing-and-billing"></a>定价和计费

将 v1 存储帐户升级为常规用途 v2 帐户是免费的。 在升级过程中，可以指定所需的帐户层。 如果在升级时未指定帐户层，将 `Hot`升级帐户的默认帐户层。 但是，在升级后更改存储访问层可能会导致帐单更改，因此建议在升级过程中指定新的帐户层。

所有存储帐户使用的定价模型都适用于 Blob 存储，具体取决于每个 Blob 的层。 使用存储帐户时，需要考虑到以下计费因素：

* **存储成本**：除了存储的数据量，存储数据的成本因存储访问层而异。 层越冷，单 GB 成本越低。

* **数据访问成本**：层越冷，数据访问费用越高。 对于冷存储访问层和存档存储访问层中的数据，需要按 GB 支付读取方面的数据访问费用。

* **事务成本**：层越冷，每个层的按事务收费越高。

* **异地复制数据传输成本**：此费用仅适用于配置了异地复制的帐户，包括 GRS 和 RA-GRS。 异地复制数据传输会产生每 GB 费用。

* **出站数据传输成本**：出站数据传输（传出 Azure 区域的数据）会按每 GB 产生带宽使用费，与通用存储帐户一致。

* **更改存储访问层**：将帐户存储访问层从 "冷" 更改为 "热" 会产生一个费用，此费用等于读取存储帐户中的所有数据。 但是，将帐户访问层从“热”更改为“冷”产生的费用则相当于将所有数据写入冷层（仅限 GPv2 帐户）。

> [!NOTE]
> 有关存储帐户的定价模型的详细信息，请参阅 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)页。 有关出站数据传输收费的详细信息，请参阅[数据传输定价详细信息](https://azure.microsoft.com/pricing/details/data-transfers/)页。

### <a name="estimate-costs-for-your-current-usage-patterns"></a>估计当前使用模式的成本

若要估算在特定层的常规用途 v2 存储帐户中存储和访问 Blob 数据的成本，请评估现有的使用模式，或对预期的使用模式进行一个大致的估计。 一般情况下，需了解：

* Blob 存储使用量（以 GB 为单位），包括：
  * 有多少数据存储在存储帐户中？
  * 数据量每月如何变化；新数据是否不断替换旧数据？

* Blob 存储数据的主要访问模式，包括：
  * 从存储帐户读取了多少数据，向其写入了多少数据？
  * 对存储帐户中的数据执行了多少读取操作和写入操作？

若要确定最适合需求的访问层，确定 blob 数据容量以及如何使用这些数据会很有帮助。 最好通过查看帐户的监视指标来完成此操作。

### <a name="monitoring-existing-storage-accounts"></a>监视现有存储帐户

若要监视现有存储帐户并收集该数据，可以利用 Azure 存储分析进行日志记录，并为存储帐户提供指标数据。 存储分析可存储一些指标，这些指标包括有关存储服务请求的聚合事务统计信息和容量数据，适用于 GPv1、GPv2 和 Blob 存储帐户类型。 该数据存储在同一存储帐户中的已知表中。

有关详细信息，请参阅 [About Storage Analytics Metrics](https://msdn.microsoft.com/library/azure/hh343258.aspx)（关于存储分析指标）和 [Storage Analytics Metrics Table Schema](https://msdn.microsoft.com/library/azure/hh343264.aspx)（存储分析指标表架构）

> [!NOTE]
> Blob 存储帐户公开表服务终结点的目的只是为了存储和访问该帐户的指标数据。

若要监视 Blob 存储的存储消耗情况，需启用容量指标。
启用此功能后，会每天为存储帐户的 Blob 服务记录容量数据，并将容量数据记录为表条目写入到同一存储帐户中的$MetricsCapacityBlob 表。

若要监视 Blob 存储的数据访问模式，需通过 API 启用每小时事务指标。 启用每小时事务指标后，会每小时聚合按 API 进行的事务，并将这些事务记录为表条目写入到同一存储帐户中的 *$MetricsHourPrimaryTransactionsBlob* 表。 在使用 RA-GRS 存储帐户时，$MetricsHourSecondaryTransactionsBlob 表会将事务记录到辅助终结点。

> [!NOTE]
> 如果有一个常规用途存储帐户，在其中存储了页 Blob、虚拟机磁盘、队列、文件、表以及块 Blob 数据和追加 Blob 数据，则不适合使用此估算过程。 容量数据不区分块 Blob 与其他类型，因此不会给出其他数据类型的容量数据。 如果使用这些类型，则也可查看最新账单上的数量，作为一种替代方法。

若要对数据使用和访问模式进行准确的估算，建议为指标选择一个可代表日常使用情况的保留期，并进行推断。 一种选择是让指标数据保留七天，每周收集一次数据，并在月底进行分析。 另一种选择是让指标数据保留 30 天，在 30 天到期以后再收集和分析数据。

若要详细了解如何启用、收集和查看度量值数据，请参阅[存储分析度量值](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

> [!NOTE]
> 存储、访问和下载分析数据也会收费，就像使用常规用户数据一样。

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>通过使用情况度量值来估算费用

#### <a name="capacity-costs"></a>容量费用

容量度量值表 $MetricsCapacityBlob 中行键为 'data' 的最新条目显示了用户数据所占用的存储容量。 容量指标表 $MetricsCapacityBlob 中行键为 'analytics' 的最新条目显示了分析日志所占用的存储容量。

用户数据和分析日志（如果已启用）所占用的这个总容量就可以用来估算在存储帐户中存储数据的费用。 也可以使用相同方法来估算 GPv1 存储帐户中的存储成本。

#### <a name="transaction-costs"></a>事务成本

事务度量值表中某个 API 的所有条目的“ *TotalBillableRequests*”计得之和表示该特定 API 的事务总数。 例如，通过对行健为 'user;GetBlob' 的所有条目的计费请求进行求和可以算出一段给定时间中 'GetBlob' 事务的总数。

若要估算 Blob 存储帐户的事务费用，需将事务细分成三组，因为这些事务价格不一样。

* 写入事务，例如 'PutBlob'、'PutBlock'、'PutBlockList'、'AppendBlock'、'ListBlobs'、'ListContainers'、'CreateContainer'、'SnapshotBlob' 和 'CopyBlob'。
* 删除事务，例如 'DeleteBlob' 和 'DeleteContainer'。
* 所有其他事务。

若要估算 GPv1 存储帐户的事务成本，需聚合所有事务而不考虑操作/API。

#### <a name="data-access-and-geo-replication-data-transfer-costs"></a>数据访问和异地复制数据传输费用

虽然存储分析不提供从存储帐户读取以及写入到存储帐户的数据量，但该数据量可以通过查看事务度量值表来大致进行估算。 事务度量值表中某个 API 的所有条目的 “ *TotalIngress* ”计得之和表示该特定 API 的传入数据的总量。 与此类似，“ *TotalEgress* ”计得之和表示传出数据的总量，以字节为单位。

若要估算 Blob 存储帐户的数据访问费用，需将事务细分成两组。

* 从存储帐户检索的数据量可以通过查看主要为 'GetBlob' 和 'CopyBlob' 操作的 'TotalEgress' 计得之和来估算。

* 写入到存储帐户的数据量可以通过查看主要为 'PutBlob'、'PutBlock'、'CopyBlob' 和 'AppendBlock' 操作的 'TotalIngress' 计得之和来估算。

在使用 GRS 或 RA-GRS 存储帐户时，也可以通过所写入数据量的估算值来计算 Blob 存储帐户的异地复制数据传输费用。

> [!NOTE]
> 如需更详细的示例来了解如何计算热或冷存储访问层的使用费用，请参阅 *Azure 存储定价页* 中标题为“[什么是‘热’和‘冷’访问层以及如何确定应使用哪一个？](https://azure.microsoft.com/pricing/details/storage/)”的常见问题解答。

## <a name="next-steps"></a>后续步骤

* [创建存储帐户](storage-quickstart-create-account.md)
