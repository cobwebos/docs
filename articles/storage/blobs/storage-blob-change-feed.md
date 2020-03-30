---
title: 更改 Azure Blob 存储中的源（预览） |微软文档
description: 了解 Azure Blob 存储中的更改源日志以及如何使用它们。
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: ac111b06d578a0e9af8581ef2e8caeccfc4a291e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536881"
---
# <a name="change-feed-support-in-azure-blob-storage-preview"></a>更改 Azure Blob 存储中的源支持（预览）

更改源的目的是提供存储帐户中 Blob 和 Blob 元数据发生的所有更改的事务日志。 更改源提供这些更改的**有序**、**保证**、**耐用**、**不可变**、**只读**日志。 客户端应用程序可以随时读取这些日志，无论是在流式处理还是批处理模式下。 更改源使您能够构建高效且可扩展的解决方案，以低成本处理 Blob 存储帐户中发生的更改事件。

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

更改源以标准[Blob 定价](https://azure.microsoft.com/pricing/details/storage/blobs/)成本作为[blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)存储在存储帐户中的特殊容器中。 您可以根据您的要求控制这些文件的保留期（请参阅当前版本的[条件](#conditions)）。 更改事件作为[Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html)格式规范中的记录追加到更改源：一种紧凑、快速、二进制的格式，提供具有内联架构的丰富数据结构。 这种格式广泛用于 Hadoop 生态系统、流分析和 Azure 数据工厂。

您可以异步、增量或完整地处理这些日志。 任意数量的客户端应用程序都可以并行和按照自己的节奏独立读取更改源。 分析应用程序（如[Apache Drill](https://drill.apache.org/docs/querying-avro-files/)或 Apache [Spark）](https://spark.apache.org/docs/latest/sql-data-sources-avro.html)可以直接使用作为 Avro 文件使用的日志，这样您就可以以低成本、高带宽处理日志，而无需编写自定义应用程序。

更改源支持非常适合基于已更改的对象处理数据的方案。 例如，应用程序可以：

  - 更新辅助索引，与缓存、搜索引擎或任何其他内容管理方案同步。
  
  - 根据对象在流式处理或批处理模式下发生的更改提取业务分析见解和指标。
  
  - 存储、审核和分析对象在任何时间段内的更改，以确保企业数据管理的安全性、合规性或智能性。

  - 构建用于备份、镜像或复制帐户中的对象状态的解决方案，以实现灾难管理或合规性。

  - 构建基于创建或更改的对象对更改事件或计划执行做出反应的连接应用程序管道。

> [!NOTE]
> 更改源提供了对 Blob 发生的更改的持久有序日志模型。 更改将在更改源日志中写入并在更改后的几分钟内提供。 如果应用程序必须对此更快地对事件做出反应，请考虑改用 Blob[存储事件](storage-blob-event-overview.md)。 [Blob 存储事件](storage-blob-event-overview.md)提供实时一次性事件，使 Azure 函数或应用程序能够对 Blob 发生的更改快速响应。 

## <a name="enable-and-disable-the-change-feed"></a>启用并禁用更改源

您必须启用存储帐户上的更改源才能开始捕获和记录更改。 禁用更改源以停止捕获更改。 您可以使用门户或 Powershell 上的 Azure 资源管理器模板启用和禁用更改。

在启用更改源时，需要记住以下几点。

- 每个存储帐户中只有一个 blob 服务的更改源，并且存储在 **$blobchangefeed**容器中。

- 创建、更新和删除更改仅在 blob 服务级别捕获。

- 更改源捕获帐户上发生的所有可用事件*的所有*更改。 客户端应用程序可以根据需要筛选出事件类型。 （请参阅当前版本[的条件](#conditions)）。

- 只有 GPv2 和 Blob 存储帐户才能启用更改源。 当前不支持高级块 Blob 存储帐户和启用分层命名空间的帐户。 不支持 GPv1 存储帐户，但可以升级到 GPv2，无需停机，有关详细信息，请参阅[升级到 GPv2 存储帐户](../common/storage-account-upgrade.md)。

> [!IMPORTANT]
> 更改源处于公共预览版中，可在**西中部**和**西部 2**区域提供。 请参阅本文[的条件](#conditions)部分。 要注册预览版，请参阅本文的["注册订阅](#register)"部分。 必须先注册订阅，然后才能在存储帐户上启用更改源。

### <a name="portal"></a>[门户](#tab/azure-portal)

使用 Azure 门户在存储帐户上启用更改源：

1. 在 [Azure 门户](https://portal.azure.com/)中，选择存储帐户。 

2. 导航到“Blob 服务”**** 下的“数据保护”**** 选项。

3. 单击 **"Blob 更改源**下**启用"**

4. 选择“保存”**** 按钮以确认“数据保护”设置

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

### <a name="powershell"></a>[电源外壳](#tab/azure-powershell)

使用 PowerShell 启用更改源：

1. 安装最新的电源壳获取。

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. 关闭，然后重新打开电源壳控制台。

3. 安装**Az.存储**预览模块。

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.8.1-preview –AllowPrerelease –AllowClobber –Force
   ```

4. 使用 `Connect-AzAccount` 命令登录到 Azure 订阅，然后按照屏幕上的说明进行身份验证。

   ```powershell
   Connect-AzAccount
   ```

5. 为存储帐户启用更改源。

   ```powershell
   Update-AzStorageBlobServiceProperty -EnableChangeFeed $true
   ```

### <a name="template"></a>[模板](#tab/template)
使用 Azure 资源管理器模板通过 Azure 门户在现有存储帐户上启用更改源：

1. 在 Azure 门户中，选择 **"创建资源**"。

2. 在“搜索市场”中键入“模板部署”，然后按 **ENTER**。********

3. 选择**["部署自定义模板](https://portal.azure.com/#create/Microsoft.Template)**"，然后在**编辑器中选择"构建您自己的模板**"。

4. 在模板编辑器中，粘贴到以下 json 中。 将 `<accountName>` 占位符替换为存储帐户的名称。

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
    
5. 选择 **"保存**"按钮，指定帐户的资源组，然后选择 **"购买**"按钮以部署模板并启用更改源。

---

## <a name="consume-the-change-feed"></a>使用更改源

更改源生成多个元数据和日志文件。 这些文件位于存储帐户**的$blobchangefeed**容器中。 

> [!NOTE]
> 在当前版本中 **，$blobchangefeed**容器在 Azure 存储资源管理器或 Azure 门户中不可见。 当前，当您调用 ListContainer API 时，您当前看不到$blobchangefeed容器，但可以直接在容器上调用 ListBlobs API 以查看 blob。

客户端应用程序可以使用与更改馈送处理器 SDK 一起提供的 Blob 更改源处理器库来使用更改源。 

请参阅[Azure Blob 存储中的进程更改源日志](storage-blob-change-feed-how-to.md)。

## <a name="understand-change-feed-organization"></a>了解更改源组织

<a id="segment-index"></a>

### <a name="segments"></a>段数

更改源是按**小时***细分*组织的更改日志，但每隔几分钟追加并更新一次。 仅当该小时内发生 Blob 更改事件时，才会创建这些段。 这使客户端应用程序能够使用特定时间范围内发生的更改，而无需在整个日志中搜索。 要了解更多信息，请参阅[规范](#specifications)。

更改源的可用每小时段在清单文件中描述，该文件指定该段的更改源文件的路径。 `$blobchangefeed/idx/segments/`虚拟目录的列表显示按时间排序的这些段。 段的路径描述段表示的每小时时间范围的开始。 您可以使用该列表筛选出您感兴趣的日志段。

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> 启用`$blobchangefeed/idx/segments/1601/01/01/0000/meta.json`更改源时自动创建 。 您可以安全地忽略此文件。 它是一个始终为空的初始化文件。 

段清单文件 （`meta.json`） 在`chunkFilePaths`属性中显示该段的更改源文件的路径。 下面是段清单文件的示例。

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
> 只有在`$blobchangefeed`您启用帐户上的更改源功能后，才会显示该容器。 启用更改源后，必须等待几分钟，然后才能在容器中列出 Blob。 

<a id="log-files"></a>

### <a name="change-event-records"></a>更改事件记录

更改源文件包含一系列更改事件记录。 每个更改事件记录对应于单个 Blob 的一个更改。 记录使用[Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html)格式规范进行序列化并写入文件。 可以使用 Avro 文件格式规范读取记录。 有几个库可用于处理该格式的文件。

更改源文件作为[追加](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs)blob`$blobchangefeed/log/`存储在虚拟目录中。 每个路径下的第一个更改源文件将在`00000`文件名中（例如`00000.avro`）。 添加到该路径的每个后续日志文件的名称将递增 1（例如： `00001.avro`。

下面是从转换源文件转换为 Json 的更改事件记录的示例。

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
> 创建段后，段的更改源文件不会立即显示。 延迟长度在更改源的发布延迟的正常间隔内，该间隔在更改后的几分钟内。

<a id="specifications"></a>

## <a name="specifications"></a>规范

- 更改事件记录仅追加到更改源中。 一旦这些记录被追加，它们是不可改变的，并且记录位置是稳定的。 客户端应用程序可以在更改源的读取位置上维护自己的检查点。

- 更改事件记录在更改后的几分钟内附加。 客户端应用程序可以选择使用记录，因为它们被追加用于流式访问，或者在任何其他时间批量使用。

- 更改事件记录按**每个 blob**的修改顺序排序。 在 Azure Blob 存储中未定义跨 Blob 的更改顺序。 前一段中的所有更改都发生在后续段的任何更改之前。

- 使用[Apache Avro 1.8.2](https://avro.apache.org/docs/1.8.2/spec.html)格式规范将更改事件记录序列化到日志文件中。

- 更改值为 的内部`eventType``Control`系统记录且不反映帐户中对象的更改的事件记录。 您可以安全地忽略这些记录。

- 属性包中`storageDiagnonstics`的值仅供内部使用，不设计供应用程序使用。 您的应用程序不应对该数据具有合同依赖性。 您可以安全地忽略这些属性。

- 段表示的时间**近似**，边界为 15 分钟。 因此，为了确保在指定时间内消耗所有记录，消耗连续的上一小时和下一小时段。

- 由于日志流的内部分区来管理发布吞吐量`chunkFilePaths`，每个段可以具有不同数量。 每个`chunkFilePath`日志文件都保证包含互斥的 Blob，并且可以并行使用和处理，而不会违反迭代期间每个 Blob 的修改顺序。

- 段以`Publishing`状态开始。 一旦将记录追加到段后，它将是`Finalized`。 应用程序不应使用`LastConsumable``$blobchangefeed/meta/Segments.json`文件中属性日期之后的任何段中的日志文件。 下面是文件中`LastConsumable`属性的示例`$blobchangefeed/meta/Segments.json`：

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

## <a name="register-your-subscription-preview"></a>注册订阅（预览版）

由于更改源仅在公共预览版中，因此您需要注册订阅才能使用该功能。

### <a name="register-by-using-powershell"></a>使用 PowerShell 进行注册

在 PowerShell 控制台中，运行以下命令：

```powershell
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```
   
### <a name="register-by-using-azure-cli"></a>使用 Azure CLI 注册

在 Azure 云外壳中，运行以下命令：

```azurecli
az feature register --namespace Microsoft.Storage --name Changefeed
az provider register --namespace 'Microsoft.Storage'
```

<a id="conditions"></a>

## <a name="conditions-and-known-issues-preview"></a>条件和已知问题（预览版）

本节介绍更改源的当前公共预览中的已知问题和条件。 
- 对于预览版，必须先[注册订阅](#register)，然后才能为西中部或西越地区的存储帐户启用更改源。 
- 更改源仅捕获创建、更新、删除和复制操作。 元数据更新当前未在预览中捕获。
- 任何单个更改的更改事件记录可能会多次显示在更改源中。
- 您还不能通过设置基于时间的保留策略来管理更改源日志文件的生存期，并且无法删除 Blob 
- 日志文件`url`的属性当前始终为空。
- 段`LastConsumable`.json 文件的属性不列出更改源完成的第一个段。 此问题仅在第一个段完成后才会出现。 第一小时后的所有后续段都准确捕获在属性中`LastConsumable`。
- 当前，当您调用 List容器 API 时，您当前看不到 **$blobchangefeed**容器，并且该容器不会显示在 Azure 门户或存储资源管理器上
- 以前启动[帐户故障转移](../common/storage-disaster-recovery-guidance.md)的存储帐户可能存在日志文件未出现的问题。 任何将来的帐户故障转移也可能在预览期间影响日志文件。

## <a name="faq"></a>FAQ

### <a name="what-is-the-difference-between-change-feed-and-storage-analytics-logging"></a>更改源和存储分析日志记录之间的区别是什么？
分析日志具有所有已读、写入、列表和删除操作的记录，这些操作具有跨所有操作的成功和失败的请求。 分析日志是尽力而为的，并且不保证订购。

更改源是一种解决方案，它提供成功突变或帐户更改（如 Blob 创建、修改和删除）的事务日志。 更改源保证按每个 Blob 成功更改的顺序记录和显示所有事件，因此您不必从大量读取操作或失败的请求中过滤掉杂色。 更改源从根本上针对需要某些保证的应用程序开发进行了设计和优化。

### <a name="should-i-use-change-feed-or-storage-events"></a>我应该使用更改源或存储事件？
您可以利用更改源和[Blob 存储事件](storage-blob-event-overview.md)这两个功能，提供相同的信息，同时保证相同的传递可靠性，主要区别在于事件记录的延迟、排序和存储。 更改源在更改后的几分钟内将记录发布到日志，并保证每个 Blob 的更改操作顺序。 存储事件实时推送，可能无法排序。 更改源事件可持久地存储在存储帐户中，作为只读稳定日志，具有自己的定义保留，而存储事件是暂时性的，由事件处理程序使用，除非您显式存储它们。 使用更改源，任意数量的应用程序都可以使用 blob API 或 SDK 在自身方便时使用日志。 

## <a name="next-steps"></a>后续步骤

- 请参阅如何使用 .NET 客户端应用程序读取更改源的示例。 请参阅[Azure Blob 存储中的进程更改源日志](storage-blob-change-feed-how-to.md)。
- 了解如何实时响应事件。 请参阅[对 Blob 存储事件的反应](storage-blob-event-overview.md)
- 详细了解所有请求的成功操作和失败操作的详细日志记录信息。 请参阅[Azure 存储分析日志记录](../common/storage-analytics-logging.md)
