---
title: 更改 Azure Blob 存储中的源（预览版） |Microsoft Docs
description: 了解 Azure Blob 存储中的更改源日志以及如何使用这些日志。
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: ea0b173f12a1c80f276af3ce3f6222efaad07972
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370621"
---
# <a name="change-feed-support-in-azure-blob-storage-preview"></a>Azure Blob 存储中的更改源支持（预览）

更改源的目的是提供存储帐户中 blob 和 blob 元数据发生的所有更改的事务日志。 更改源提供了这些更改的**有序、有** **保证**、**持久**、**不可变**的**只读**日志。 客户端应用程序可以在流式传输或批处理模式下随时读取这些日志。 利用更改源，你可以构建高效且可缩放的解决方案，以较低的成本处理在 Blob 存储帐户中发生的更改事件。

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

更改源将以[blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)的形式存储在存储帐户的特殊容器中，其[价格](https://azure.microsoft.com/pricing/details/storage/blobs/)为标准 blob。 您可以根据您的需求来控制这些文件的保留期（请参阅当前版本的[条件](#conditions)）。 更改事件将作为[Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html)格式规范中的记录追加到更改源：采用简洁、快速、二进制格式，该格式可通过内联架构提供丰富的数据结构。 这种格式广泛用于 Hadoop 生态系统、流分析和 Azure 数据工厂。

您可以以增量或完整方式异步处理这些日志。 任意数量的客户端应用程序都可以独立地并行读取更改源，并按自己的节奏进行。 [Apache 钻取](https://drill.apache.org/docs/querying-avro-files/)或[Apache Spark](https://spark.apache.org/docs/latest/sql-data-sources-avro.html)之类的分析应用程序可以直接将日志用作 Avro 文件，这使你可以使用较低的成本（具有高带宽）处理它们，而无需编写自定义应用程序。

更改源支持非常适合于基于已更改对象处理数据的方案。 例如，应用程序可以：

  - 更新辅助索引，与缓存、搜索引擎或任何其他内容管理方案同步。
  
  - 基于对象发生的更改、流式处理方式或批处理模式，提取业务分析见解和指标。
  
  - 针对企业数据管理的安全、合规性或智能，在任何时间段内存储、审核和分析对象的更改。

  - 构建解决方案来备份、镜像或复制帐户中的对象状态，以便进行灾难管理或符合性。

  - 基于创建的或已更改的对象，生成响应更改事件或计划执行的已连接应用程序管道。

> [!NOTE]
> 更改源提供对 blob 发生的更改的持久、有序日志模型。 更改会在更改的几分钟内写入并在更改源日志中可用。 如果你的应用程序必须比此更快地响应事件，请考虑改用[Blob 存储事件](storage-blob-event-overview.md)。 [Blob 存储事件](storage-blob-event-overview.md)提供实时的一次性事件，这些事件使你的 Azure Functions 或应用程序能够快速响应对 Blob 所做的更改。 

## <a name="enable-and-disable-the-change-feed"></a>启用和禁用更改源

必须在存储帐户上启用更改源，才能开始捕获和记录更改。 禁用更改源以停止捕获更改。 可以通过在门户或 Powershell 上使用 Azure 资源管理器模板来启用和禁用更改。

如果启用更改源，请注意以下几个事项。

- 每个存储帐户中的 blob 服务只有一个更改源，并存储在 **$blobchangefeed**容器中。

- 仅在 blob 服务级别捕获创建、更新和删除更改。

- 更改源捕获帐户上发生的所有可用事件的*所有*更改。 客户端应用程序可以根据需要筛选出事件类型。 （请参阅当前版本的[条件](#conditions)）。

- 只有 GPv2 和 Blob 存储帐户可以启用更改源。 当前不支持高级 BlockBlobStorage 帐户和已启用分层命名空间的帐户。 GPv1 存储帐户不受支持，但可以在不停机的情况下升级到 GPv2，有关详细信息，请参阅[升级到 GPv2 存储帐户](../common/storage-account-upgrade.md)。

> [!IMPORTANT]
> 更改源以公共预览版提供，并在**westcentralus**和**westus2**区域中提供。 请参阅本文的 "[条件](#conditions)" 一节。 若要注册预览版，请参阅本文的[注册订阅](#register)部分。 必须先注册你的订阅，然后才能在存储帐户上启用更改源。

### <a name="portal"></a>[门户](#tab/azure-portal)

使用 Azure 门户在存储帐户上启用更改源：

1. 在[Azure 门户](https://portal.azure.com/)中，选择存储帐户。 

2. 导航到 " **Blob 服务**" 下的 "**数据保护**" 选项。

3. 单击 " **Blob 更改源**" 下的 "**已启用**"

4. 选择 "**保存**" 按钮以确认数据保护设置

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 启用更改源：

1. 安装最新的 PowershellGet。

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. 关闭，然后重新打开 Powershell 控制台。

3. 安装**Az** preview 模块。

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.8.1-preview –AllowPrerelease –AllowClobber –Force
   ```

4. 使用 `Connect-AzAccount` 命令登录到 Azure 订阅，然后按照屏幕上的说明进行身份验证。

   ```powershell
   Connect-AzAccount
   ```

5. 为存储帐户启用 "更改源"。

   ```powershell
   Update-AzStorageBlobServiceProperty -EnableChangeFeed $true
   ```

### <a name="template"></a>[模板](#tab/template)
使用 Azure 资源管理器模板通过 Azure 门户在现有存储帐户上启用更改源：

1. 在 Azure 门户中，选择 "**创建资源**"。

2. 在“搜索市场”中键入“模板部署”，然后按 **ENTER**。

3. 选择 " **[部署自定义模板](https://portal.azure.com/#create/Microsoft.Template)** "，然后选择 **"在编辑器中生成自己的模板"** 。

4. 在模板编辑器中，粘贴以下 json。 将 `<accountName>` 占位符替换为存储帐户的名称。

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts/blobServices",
           "apiVersion": "2019-04-01",
           "name": "<accountName>/default",
           "properties": {
               "changeFeed": {
                   "enabled": true
               }
           } 
        }]
   }
   ```
    
5. 选择 "**保存**" 按钮，指定帐户的资源组，然后选择 "**购买**" 按钮部署模板并启用更改源。

---

## <a name="consume-the-change-feed"></a>使用更改源

更改源将生成多个元数据文件和日志文件。 这些文件位于存储帐户的 **$blobchangefeed**容器中。 

> [!NOTE]
> 在当前版本中， **$blobchangefeed**容器在 Azure 存储资源管理器或 Azure 门户中不可见。 在调用 ListContainers API 时，当前无法看到 $blobchangefeed 容器，但可以直接在容器上调用 ListBlobs API 来查看 blob。

你的客户端应用程序可以使用随更改源处理器 SDK 一起提供的 blob 更改源处理器库来使用更改源。 

请参阅[处理 Azure Blob 存储中的更改源日志](storage-blob-change-feed-how-to.md)。

## <a name="understand-change-feed-organization"></a>了解更改源组织

<a id="segment-index"></a>

### <a name="segments"></a>段数

更改源是组织为每**小时***段*的更改日志，但会在每隔几分钟后附加和更新。 仅当在该小时发生了 blob 更改事件时，才会创建这些段。 这样，客户端应用程序便可以使用在特定时间范围内发生的更改，而不必搜索整个日志。 若要了解详细信息，请参阅[规范](#specifications)。

更改源的可用小时段在清单文件中进行了描述，该文件指定了该段的更改源文件的路径。 `$blobchangefeed/idx/segments/` 的虚拟目录的列表显示按时间排序的这些段。 段的路径描述段所表示的每小时时间范围的开始时间。 您可以使用该列表来筛选出感兴趣的日志段。

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> 当启用更改源时，将自动创建 `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json`。 可以放心地忽略此文件。 它是一个始终为空的初始化文件。 

段清单文件（`meta.json`）在 `chunkFilePaths` 属性中显示该段的更改源文件的路径。 下面是段清单文件的示例。

```json
{
    "version": 0,
    "begin": "2019-02-22T18:10:00.000Z",
    "intervalSecs": 3600,
    "status": "Finalized",
    "config": {
        "version": 0,
        "configVersionEtag": "0x8d698f0fba563db",
        "numShards": 2,
        "recordsFormat": "avro",
        "formatSchemaVersion": 1,
        "shardDistFnVersion": 1
    },
    "chunkFilePaths": [
        "$blobchangefeed/log/00/2019/02/22/1810/",
        "$blobchangefeed/log/01/2019/02/22/1810/"
    ],
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-22T18:11:01.187Z",
        "data": {
            "aid": "55e507bf-8006-0000-00d9-ca346706b70c"
        }
    }
}
```

> [!NOTE]
> 只有在你的帐户上启用了更改源功能后，才会显示 `$blobchangefeed` 容器。 必须先等待几分钟，再启用更改源，然后才能列出容器中的 blob。 

<a id="log-files"></a>

### <a name="change-event-records"></a>更改事件记录

更改源文件包含一系列更改事件记录。 每个 change 事件记录对应于单个 blob 的一个更改。 使用[Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html)格式规范对记录进行序列化并将其写入文件。 可以使用 Avro 文件格式规范读取记录。 有几个库可用于处理该格式的文件。

更改源文件以[追加 blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs)的形式存储在 `$blobchangefeed/log/` 虚拟目录中。 每个路径下的第一个更改源文件将在文件名中具有 `00000` （例如，`00000.avro`）。 添加到该路径中的每个后续日志文件的名称将递增1（例如： `00001.avro`）。

下面是转换为 Json 的更改源文件中的 change 事件记录的示例。

```json
{
     "schemaVersion": 1,
     "topic": "/subscriptions/dd40261b-437d-43d0-86cf-ef222b78fd15/resourceGroups/sadodd/providers/Microsoft.Storage/storageAccounts/mytestaccount",
     "subject": "/blobServices/default/containers/mytestcontainer/blobs/mytestblob",
     "eventType": "BlobCreated",
     "eventTime": "2019-02-22T18:12:01.079Z",
     "id": "55e5531f-8006-0000-00da-ca3467000000",
     "data": {
         "api": "PutBlob",
         "clientRequestId": "edf598f4-e501-4750-a3ba-9752bb22df39",
         "requestId": "00000000-0000-0000-0000-000000000000",
         "etag": "0x8D698F13DCB47F6",
         "contentType": "application/octet-stream",
         "contentLength": 128,
         "blobType": "BlockBlob",
         "url": "",
         "sequencer": "000000000000000100000000000000060000000000006d8a",
         "storageDiagnostics": {
             "bid": "11cda41c-13d8-49c9-b7b6-bc55c41b3e75",
             "seq": "(6,5614,28042,28038)",
             "sid": "591651bd-8eb3-c864-1001-fcd187be3efd"
         }
  }
}
```

有关每个属性的说明，请参阅[Blob 存储的 Azure 事件网格事件架构](https://docs.microsoft.com/azure/event-grid/event-schema-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties)。

> [!NOTE]
> 段的更改源文件不会立即出现在段创建之后。 延迟的长度在更改源的发布滞后时间的正常时间间隔内，该时间段是在更改后的几分钟内。

<a id="specifications"></a>

## <a name="specifications"></a>规范

- 更改事件记录仅追加到更改源。 追加这些记录后，它们是不可变的，并且记录位置是稳定的。 客户端应用程序可以在更改源的读取位置维护其自己的检查点。

- 更改事件记录在更改后的几分钟内追加。 客户端应用程序可以选择在将记录追加到流访问时使用记录，也可以在任何其他时间批量使用记录。

- 更改事件记录按**每个 blob**的修改顺序排序。 在 Azure Blob 存储中不定义跨 blob 的更改顺序。 之前段中的所有更改都将在后续段中进行任何更改之前。

- 使用[Apache Avro 1.8.2](https://avro.apache.org/docs/1.8.2/spec.html)格式规范将更改事件记录序列化为日志文件。

- 更改 `eventType` 值为 "`Control`" 的事件记录是内部系统记录，不反映对帐户中对象的更改。 您可以放心地忽略这些记录。

- `storageDiagnonstics` 属性包中的值仅供内部使用，而不是由应用程序使用。 您的应用程序不应对该数据具有合同相关性。 您可以放心地忽略这些属性。

- 段所代表的时间是**大致**与15分钟的界限。 因此，若要确保在指定时间内所有记录的消耗，请使用连续的前一小时和后个小时段。

- 由于日志流的内部分区可管理发布吞吐量，因此每个段可以有不同数量的 `chunkFilePaths`。 每个 `chunkFilePath` 中的日志文件都保证包含互斥的 blob，并且可以并行使用和处理，而不会违反迭代期间每个 blob 的修改顺序。

- 段 `Publishing` 状态中开始。 将记录追加到段完成后，它将被 `Finalized`。 应用程序不应使用日期在 `$blobchangefeed/meta/Segments.json` 文件中 `LastConsumable` 属性日期之后的任何段中的日志文件。 下面是一个 `$blobchangefeed/meta/Segments.json` 文件中 `LastConsumable`属性的示例：

```json
{
    "version": 0,
    "lastConsumable": "2019-02-23T01:10:00.000Z",
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-23T02:24:00.556Z",
        "data": {
            "aid": "55e551e3-8006-0000-00da-ca346706bfe4",
            "lfz": "2019-02-22T19:10:00.000Z"
        }
    }
}

```

<a id="register"></a>

## <a name="register-your-subscription-preview"></a>注册订阅（预览）

由于更改源仅用于公共预览，因此你需要注册你的订阅才能使用此功能。

### <a name="register-by-using-powershell"></a>使用 PowerShell 注册

在 PowerShell 控制台中运行以下命令：

```powershell
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```
   
### <a name="register-by-using-azure-cli"></a>使用 Azure CLI 注册

在 Azure Cloud Shell 中运行以下命令：

```cli
az feature register --namespace Microsoft.Storage --name Changefeed
az provider register --namespace 'Microsoft.Storage'
```

<a id="conditions"></a>

## <a name="conditions-and-known-issues-preview"></a>条件和已知问题（预览）

本部分介绍更改源的当前公共预览版中的已知问题和条件。 
- 对于预览版，你必须先[注册你的订阅](#register)，然后才能在 westcentralus 或 westus2 区域中为你的存储帐户启用 "更改源"。 
- 更改源只捕获创建、更新、删除和复制操作。 预览中当前未捕获元数据更新。
- 更改源中每次更改的事件记录可能出现多次。
- 你尚未通过在其上设置基于时间的保留策略来管理更改源日志文件的生存期，你无法删除 blob 
- 日志文件的 `url` 属性当前始终为空。
- 段 json 文件的 `LastConsumable` 属性不列出更改源所终结的第一段。 仅在第一段完成后才会出现此问题。 第一小时之后的所有后续段都将在 `LastConsumable` 属性中精确捕获。
- 当你调用 ListContainers API 时，你当前无法看到 **$blobchangefeed**容器，并且容器未显示在 Azure 门户或存储资源管理器
- 以前启动的[帐户故障转移](../common/storage-disaster-recovery-guidance.md)的存储帐户可能在出现日志文件时出现问题。 将来的任何帐户故障转移也可能会影响预览期间的日志文件。

## <a name="faq"></a>常见问题解答

### <a name="what-is-the-difference-between-change-feed-and-storage-analytics-logging"></a>更改源和存储分析日志记录之间的区别是什么？
分析日志包含所有操作的成功和失败请求的所有读取、写入、列出和删除操作的记录。 分析日志是最大努力，不保证顺序。

"更改源" 是一种解决方案，它提供成功的突变或对帐户所做更改的事务日志，例如创建、修改和删除 blob。 更改源可保证记录所有事件并按照每个 blob 的成功更改的顺序显示这些事件，因此您无需从大量读取操作或失败请求中筛选出干扰。 更改源从根本上设计并针对需要某些保证的应用程序开发进行了优化。

### <a name="should-i-use-change-feed-or-storage-events"></a>是否应使用更改源或存储事件？
你可以利用这两种功能，因为更改源和[Blob 存储事件](storage-blob-event-overview.md)使用相同的传递可靠性保证提供相同的信息，主要区别是事件记录的延迟、排序和存储。 更改源会在更改的几分钟内将记录发布到日志，并且还保证每个 blob 的更改操作顺序。 存储事件将实时推送，并且可能不会进行排序。 更改源事件以持久存储帐户的形式存储在存储帐户中，并具有自己定义的保留期，而由事件处理程序使用的存储事件是暂时性的，除非你显式存储它们。 通过更改源，任意数量的应用程序都可以使用 blob Api 或 Sdk 自行处理日志。 

## <a name="next-steps"></a>后续步骤

- 请参阅如何使用 .NET 客户端应用程序读取更改源的示例。 请参阅[处理 Azure Blob 存储中的更改源日志](storage-blob-change-feed-how-to.md)。
- 了解如何实时响应事件。 请参阅[响应 Blob 存储事件](storage-blob-event-overview.md)
- 详细了解所有请求的成功和失败操作的详细日志记录信息。 请参阅[Azure 存储分析日志记录](../common/storage-analytics-logging.md)
