---
title: 配置 Blob 索引器
titleSuffix: Azure Cognitive Search
description: 设置 Azure Blob 索引器，以便在 Azure 认知搜索中自动为 Blob 内容编制索引以进行全文搜索操作。
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: e3419711c9a7358914f85574f6dbd5af29def1cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91403598"
---
# <a name="how-to-configure-a-blob-indexer-in-azure-cognitive-search"></a>如何在 Azure 中配置 blob 索引器认知搜索

本文介绍如何使用 Azure 认知搜索来索引基于文本的文档 (例如 Pdf、Microsoft Office 文档以及其他一些常见格式，) 存储在 Azure Blob 存储中。 首先，本文说明了设置和配置 Blob 索引器的基础知识。 其次，本文更加深入地探讨了你可能会遇到的行为和场景。

<a name="SupportedFormats"></a>

## <a name="supported-formats"></a>支持的格式

Blob 索引器可从以下文档格式提取文本：

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="set-up-blob-indexing"></a>设置 blob 索引

可以使用以下方式设置 Azure Blob 存储索引器：

* [Azure 门户](https://ms.portal.azure.com)
* Azure 认知搜索 [REST API](/rest/api/searchservice/Indexer-operations)
* Azure 认知搜索 [.NET SDK](/dotnet/api/overview/azure/search)

> [!NOTE]
> 某些功能（例如字段映射）在门户中尚不可用，必须以编程方式使用。
>

在这里，我们使用 REST API 演示流。

### <a name="step-1-create-a-data-source"></a>步骤 1：创建数据源

数据源指定要编制索引的数据、访问数据所需的凭据和有效标识数据更改（新行、修改的行或删除的行）的策略。 一个数据源可供同一搜索服务中的多个索引器使用。

若要为 Blob 编制索引，数据源必须具有以下属性：

* **name** 是搜索服务中数据源的唯一名称。
* **type** 必须是 `azureblob`。
* * * 凭据提供存储帐户连接字符串作为 `credentials.connectionString` 参数。 有关详细信息请参阅下方的[如何指定凭据](#Credentials)。
* **container** 指定存储帐户中的容器。 默认情况下，容器中的所有 Blob 都可检索。 如果只想为特定虚拟目录中的 Blob 编制索引，可以使用可选的 **query** 参数指定该目录。

创建数据源：

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }
```

有关创建数据源 API 的详细信息，请参阅[创建数据源](/rest/api/searchservice/create-data-source)。

<a name="Credentials"></a>

#### <a name="how-to-specify-credentials"></a>如何指定凭据

可通过以下一种方式提供 blob 容器的凭据：

* **托管标识连接字符串**： `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;` 

  此连接字符串不需要帐户密钥，但必须按照说明 [使用托管标识设置到 Azure 存储帐户的连接](search-howto-managed-identities-storage.md)。

* **完全访问存储帐户连接字符串**： `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`

  可通过导航到“存储帐户”边栏选项卡 >“设置”>“密钥”（对于经典存储帐户）或“设置”>“访问密钥”（对于 Azure 资源管理器存储帐户），从 Azure 门户获取连接字符串。

* **存储帐户共享访问签名** (SAS) 连接字符串： `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl`

  SAS 应具有容器和对象（本例中为 blob）的列表和读取权限。

* **容器共享访问签名**： `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl`

  SAS 应具有容器的列表和读取权限。

有关存储共享访问签名的详细信息，请参阅 [使用共享访问签名](../storage/common/storage-sas-overview.md)。

> [!NOTE]
> 如果使用 SAS 凭据，则需使用续订的签名定期更新数据源凭据，以防止其过期。 如果 SAS 凭据过期，索引器会失败，出现类似于 `Credentials provided in the connection string are invalid or have expired.` 的错误消息。  

### <a name="step-2-create-an-index"></a>步骤 2：创建索引

索引指定文档、属性和其他构造中可以塑造搜索体验的字段。

下面介绍了如何使用可搜索 `content` 字段创建索引，以存储从 Blob 中提取的文本：

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```

有关详细信息，请参阅 [创建索引 (REST API) ](/rest/api/searchservice/create-index)。

### <a name="step-3-create-an-indexer"></a>步骤 3：创建索引器

索引器将数据源与目标搜索索引关联，并提供自动执行数据刷新的计划。

创建索引和数据源后，就可以准备创建索引器了：

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

此索引器每隔两小时运行一次（已将计划间隔设置为“PT2H”）。 若要每隔 30 分钟运行一次索引器，可将间隔设置为“PT30M”。 支持的最短间隔为 5 分钟。 计划是可选的 - 如果省略，则索引器在创建后只运行一次。 但是，可以随时根据需要运行索引器。   

有关详细信息，请参阅 [Create 索引器 (REST API) ](/rest/api/searchservice/create-indexer)。 若要详细了解如何定义索引器计划，请参阅[如何为 Azure 认知搜索计划索引器](search-howto-schedule-indexers.md)。

<a name="how-azure-search-indexes-blobs"></a>

## <a name="how-blobs-are-indexed"></a>如何为 blob 编制索引

根据具体的[索引器配置](#PartsOfBlobToIndex)，Blob 索引器可以仅为存储元数据编制索引（如果只关注元数据，而无需为 Blob 的内容编制索引，则此功能非常有用）、为存储元数据和内容元数据编制索引，或者同时为元数据和文本内容编制索引。 默认情况下，索引器提取元数据和内容。

> [!NOTE]
> 默认情况下，包含结构化内容（例如 JSON 或 CSV）的 lob 以单一文本区块的形式编制索引。 如果想要以结构化方法为 JSON 和 CSV Blob 编制索引，请参阅[为 JSON Blob 编制索引](search-howto-index-json-blobs.md)和[为 CSV Blob 编制索引](search-howto-index-csv-blobs.md)来了解详细信息。
>
> 复合或嵌入式文档（例如 ZIP 存档、嵌入了带附件 Outlook 电子邮件的 Word 文档或带附件的 .MSG 文件）也以单一文档的形式编制索引。 例如，从 .MSG 文件的附件中提取的所有图像将在 normalized_images 字段中返回。

* 文档的文本内容将提取到名为 `content` 的字符串字段中。

  > [!NOTE]
  > Azure 认知搜索会根据定价层限制提取的文本数量：免费层为 32,000 个字符，基本层为 64,000 个字符，标准层为 400 万个字符、标准 S2 层为 800 万个字符，标准 S3 层为 1600 万个字符。 已截断的文本会在索引器状态响应中出现一条警告。  

* Blob 中用户指定的元数据属性（如果有）将逐字提取。 请注意，这要求在索引中定义与 blob 的元数据密钥名称相同的字段。 例如，如果 blob 有值为 `High` 的元数据密钥 `Sensitivity`，则应在搜索索引中定义一个名为“`Sensitivity`”的字段，该字段将用值“`High`”填充。

* 标准 Blob 元数据属性将提取到以下字段中：

  * **metadata\_storage\_name** (Edm.String) - Blob 的文件名。 例如，对于 Blob /my-container/my-folder/subfolder/resume.pdf 而言，此字段的值是 `resume.pdf`。

  * **metadata\_storage\_path** (Edm.String) - Blob 的完整 URI（包括存储帐户）。 例如： `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`

  * **metadata\_storage\_content\_type** (Edm.String) - 用于上传 Blob 的代码指定的内容类型。 例如，`application/octet-stream`。

  * **metadata\_storage\_last\_modified** (Edm.DateTimeOffset) - 上次修改 Blob 的时间戳。 Azure 认知搜索使用此时间戳来识别已更改的 Blob，避免在初次编制索引之后再次为所有内容编制索引。

  * **metadata\_storage\_size** (Edm.Int64) - Blob 大小，以字节为单位。

  * **metadata\_storage\_content\_md5** (Edm.String) - Blob 内容的 MD5 哈希（如果有）。

  * **metadata\_storage\_sas\_token** (Edm.String) - 一个临时 SAS 令牌，可由[自定义技能](cognitive-search-custom-skill-interface.md)用来获取对 Blob 的访问权限。 不应存储此令牌供以后使用，因为它可能会过期。

* 特定于每种文档格式的元数据属性将提取到[此处](#ContentSpecificMetadata)所列的字段。

无需在搜索索引中针对上述所有属性定义字段 - 系统只捕获应用程序所需的属性。

> [!NOTE]
> 通常，现有索引中的字段名称与文档提取期间所生成的字段名称不同。 可以使用**字段映射**将 Azure 认知搜索提供的属性名称映射到搜索索引中的字段名称。 后面会提供字段映射的用法示例。
>

<a name="DocumentKeys"></a>

### <a name="defining-document-keys-and-field-mappings"></a>定义文档键和字段映射

在 Azure 认知搜索中，文档键唯一标识某个文档。 每个搜索索引只能有一个类型为 Edm.String 的键字段。 键字段对于要添加到索引的每个文档必不可少（事实上它是唯一的必填字段）。  

应该仔细考虑要将提取的哪个字段映射到索引的键字段。 候选字段包括：

* **metadata\_storage\_name** - 这可能是一个便利的候选项，但是请注意，1) 名称可能不唯一，因为不同的文件夹中可能有同名的 Blob；2) 名称中包含的字符可能在文档键中无效，例如短划线。 可以使用 `base64Encode` [字段映射函数](search-indexer-field-mappings.md#base64EncodeFunction)处理无效的字符 - 如果这样做，请记得在将这些字符传入 Lookup 等 API 调用时为文档键编码。 （例如，在 .NET 中，可以使用 [UrlTokenEncode 方法](/dotnet/api/system.web.httpserverutility.urltokenencode)实现此目的）。

* **metadata\_storage\_path** - 使用完整路径可确保唯一性，但是路径必定会包含 `/` 字符，而该字符[在文档键中无效](/rest/api/searchservice/naming-rules)。  如上所述，可以选择使用 `base64Encode` [函数](search-indexer-field-mappings.md#base64EncodeFunction)为键编码。

* 第三种方法是将自定义元数据属性添加到 blob。 但是，此选项需要 blob 上传过程将该元数据属性添加到所有 blob。 由于键是必需的属性，因此没有该属性的所有 Blob 都无法编制索引。

> [!IMPORTANT]
> 如果索引中没有键字段的显式映射，Azure 认知搜索会自动使用 `metadata_storage_path` 作为键并对键值进行 base-64 编码（上述第二个选项）。
>
>

本示例选择 `metadata_storage_name` 字段作为文档键。 同时，假设索引具有名为 `key` 的键字段，以及一个用于存储文档大小的 `fileSize` 字段。 若要连接所需的元素，请在创建或更新索引器时指定以下字段映射：

```http
    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]
```

要将所有元素合并在一起，可按如下所示添加字段映射，并为现有索引器的键启用 base-64 编码：

```http
    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "dataSourceName" : " blob-datasource ",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "fieldMappings" : [
        { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
        { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
      ]
    }
```

有关详细信息，请参阅 [字段映射和转换](search-indexer-field-mappings.md)。

#### <a name="what-if-you-need-to-encode-a-field-to-use-it-as-a-key-but-you-also-want-to-search-it"></a>如果需要对字段进行编码以将其用作键，但也希望搜索它，该怎么办？

有时，需要使用一个字段的编码版本（如 metadata_storage_path）作为键，但也需要该字段是可搜索的（无需编码）。 若要解决此问题，可以将其映射到两个字段中：一个用于键，另一个用于搜索目的。 在下面的示例中，“键”字段包含编码的路径，而“路径”字段未编码且将用作索引中的可搜索字段 。

```http
    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "dataSourceName" : " blob-datasource ",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "fieldMappings" : [
        { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
        { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "path" }
      ]
    }
```
<a name="WhichBlobsAreIndexed"></a>

## <a name="index-by-file-type"></a>按文件类型编制索引

可以控制要为哪些 Blob 编制索引，以及要跳过哪些 Blob。

### <a name="include-blobs-having-specific-file-extensions"></a>包含具有特定文件扩展名的 blob

使用 `indexedFileNameExtensions` 索引器配置参数可以做到只为具有指定扩展名的 Blob 编制索引。 值是包含文件扩展名（包括前置句点）逗号分隔列表的字符串。 例如，如果只要为 .PDF 和 .DOCX Blob 编制索引，请执行以下操作：

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }
```

### <a name="exclude-blobs-having-specific-file-extensions"></a>排除具有特定文件扩展名的 blob

使用 `excludedFileNameExtensions` 配置参数可在编制索引时排除具有特定文件扩展名的 Blob。 值是包含文件扩展名（包括前置句点）逗号分隔列表的字符串。 例如，若要为所有 Blob 编制索引，但要排除具有 .PNG 和 .JPEG 扩展名的 Blob，请执行以下操作：

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }
```

如果同时存在 `indexedFileNameExtensions` 和 `excludedFileNameExtensions` 参数，Azure 认知搜索会先查看 `indexedFileNameExtensions`，再查看 `excludedFileNameExtensions`。 这意味着，如果两个列表中存在同一个文件扩展名，将从索引编制中排除该扩展名。

<a name="PartsOfBlobToIndex"></a>

## <a name="index-parts-of-a-blob"></a>Blob 的索引部分

可以使用 `dataToExtract` 配置参数控制要为 Blob 中的哪些部分编制索引。 该参数采用以下值：

* `storageMetadata` - 指定仅为[标准 Blob 属性和用户指定的元数据](../storage/blobs/storage-blob-container-properties-metadata.md)编制索引。
* `allMetadata` - 指定要为从 Blob 内容中提取的存储元数据和[内容类型特定的元数据](#ContentSpecificMetadata)编制索引。
* `contentAndMetadata` - 指定要为从 Blob 中提取的所有元数据和文本内容编制索引。 这是默认值。

例如，如果只要为存储元数据编制索引，请使用：

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }
```

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>使用 Blob 元数据来控制如何为 Blob 编制索引

上述配置参数适用于所有 Blob。 有时，你可能想要控制为*个体 Blob* 编制索引的方式。 为此，可以添加以下 Blob 元数据属性和值：

| 属性名称 | 属性值 | 说明 |
| --- | --- | --- |
| AzureSearch_Skip |"true" |指示 Blob 索引器完全跳过该 Blob， 既不尝试提取元数据，也不提取内容。 如果特定的 Blob 反复失败并且中断编制索引过程，此属性非常有用。 |
| AzureSearch_SkipContent |"true" |此属性等效于[上面](#PartsOfBlobToIndex)所述的与特定 Blob 相关的 `"dataToExtract" : "allMetadata"` 设置。 |

## <a name="index-from-multiple-sources"></a>来自多个源的索引

你可能希望从索引中的多个源“组装”文档。 例如，你可能希望将 blob 中的文本与 Cosmos DB 中存储的其他元数据进行合并。 甚至可以将推送索引 API 与各种索引器一起使用来基于多个部件搭建搜索文档。

若要使此方式可行，所有索引器和其他组件需要针对文档键达成一致。 有关本主题的更多详细信息，请参阅 [索引多个 Azure 数据源](./tutorial-multiple-data-sources.md) 或此博客文章，将 [文档与 Azure 中的其他数据合并认知搜索](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html)。

## <a name="index-large-datasets"></a>为大型数据集编制索引

Blob 编制索引可能是一个耗时的过程。 如果有几百万个 Blob 需要编制索引，可以将数据分区，并使用多个索引器来并行处理数据，从而加快索引编制的速度。 设置方法如下：

* 将数据分区到多个 Blob 容器或虚拟文件夹

* 设置多个 Azure 认知搜索数据源，为每个容器或文件夹各设置一个。 若要指向某个 Blob 文件夹，请使用 `query` 参数：

    ```json
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

* 为每个数据源创建相应的索引器。 所有索引器可以指向同一目标搜索索引。  

* 服务中的每个搜索单位在任何给定的时间都只能运行一个索引器。 只有当索引器实际上并行运行时，如上所述创建多个索引器才很有用。 若要并行运行多个索引器，请通过创建合适数量的分区和副本来横向扩展搜索服务。 例如，如果搜索服务有 6 个搜索单位（例如，2 个分区 x 3 个副本），则 6 个索引器可以同时运行，导致索引吞吐量增加六倍。 若要了解有关缩放和容量规划的详细信息，请参阅 [调整 Azure 认知搜索服务的容量](search-capacity-planning.md)。

<a name="DealingWithErrors"></a>

## <a name="handle-errors"></a>处理错误

默认情况下，Blob 索引器一旦遇到包含不受支持内容类型（例如图像）的 Blob 时，就会立即停止。 当然，可以使用 `excludedFileNameExtensions` 参数跳过某些内容类型。 但是，可能需要在未事先了解所有可能的内容类型的情况下，为 Blob 编制索引。 要在遇到了不受支持的内容类型时继续编制索引，可将 `failOnUnsupportedContentType` 配置参数设置为 `false`：

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }
```

对于某些 blob，Azure 认知搜索无法确定内容类型，或无法处理其内容类型受其他方式支持的文档。 若要忽略此故障模式，将 `failOnUnprocessableDocument` 配置参数设置为 false：

```http
      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }
```

Azure 认知搜索会限制进行了索引编制的 blob 的大小。 这些限制记录在 [Azure 认知搜索中的服务限制](./search-limits-quotas-capacity.md)中。 过大的 blob 会被默认视为错误。 但是，如果将 `indexStorageMetadataOnlyForOversizedDocuments` 配置参数设为 true，你仍可以对过大 blob 的存储元数据编制索引：

```http
    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }
```

如果在任意处理点（无论是在解析 blob 时，还是在将文档添加到索引时）发生错误，仍然可以继续索引。 若要忽略特定的错误数，将 `maxFailedItems` 和 `maxFailedItemsPerBatch` 配置参数设置为所需值。 例如：

```http
    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }
```

<a name="ContentSpecificMetadata"></a>

## <a name="content-type-specific-metadata-properties"></a>特定于内容类型的元数据属性

下表汇总了针对每种文档格式执行的处理，并说明了 Azure 认知搜索提取的元数据属性。

| 文档格式/内容类型 | 提取的元数据 | 处理详细信息 |
| --- | --- | --- |
| HTML（文本/html） |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |剥离 HTML 标记并提取文本 |
| PDF（应用程序/pdf） |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |提取文本，包括嵌入的文档（不包括图像） |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |提取文本，包括嵌入的文档 |
| DOC (application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |提取文本，包括嵌入的文档 |
| DOCM（应用程序/vnd.ms-word.document.macroenabled.12） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |提取文本，包括嵌入的文档 |
| WORD XML（应用程序/vnd.ms-word2006ml） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |剥离 XML 标记并提取文本 |
| WORD 2003 XML（应用程序/vnd.ms-wordml） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |剥离 XML 标记并提取文本 |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |提取文本，包括嵌入的文档 |
| XLS (application/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |提取文本，包括嵌入的文档 |
| XLSM（应用程序/vnd.ms-excel.sheet.macroenabled.12） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |提取文本，包括嵌入的文档 |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |提取文本，包括嵌入的文档 |
| PPT (application/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |提取文本，包括嵌入的文档 |
| PPTM（应用程序/vnd.ms-powerpoint.presentation.macroenabled.12） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |提取文本，包括嵌入的文档 |
| MSG (application/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |提取文本，包括从附件中提取的文本。 `metadata_message_to_email`、`metadata_message_cc_email` 和 `metadata_message_bcc_email` 是字符串集合，其余字段是字符串。|
| ODT（应用程序/vnd.oasis.opendocument.text） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |提取文本，包括嵌入的文档 |
| ODS（应用程序/vnd.oasis.opendocument.spreadsheet） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |提取文本，包括嵌入的文档 |
| ODP（应用程序/vnd.oasis.opendocument.presentation） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |提取文本，包括嵌入的文档 |
| ZIP (application/zip) |`metadata_content_type` |从存档中的所有文档提取文本 |
| GZ（应用程序/gzip） |`metadata_content_type` |从存档中的所有文档提取文本 |
| EPUB（应用程序/epub+zip） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |从存档中的所有文档提取文本 |
| XML (application/xml) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |剥离 XML 标记并提取文本 |
| JSON (application/json) |`metadata_content_type`<br/>`metadata_content_encoding` |提取文本<br/>注意：如果需要从 JSON Blob 提取多个文档字段，请参阅[为 JSON Blob 编制索引](search-howto-index-json-blobs.md)了解详细信息 |
| EML (message/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |提取文本，包括附件 |
| RTF（应用程序/rtf） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | 提取文本|
| 纯文本 (text/plain) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | 提取文本|

## <a name="see-also"></a>请参阅

* [Azure 认知搜索中的索引器](search-indexer-overview.md)
* [使用 AI 了解 blob](search-blob-ai-integration.md)
* [Blob 索引概述](search-blob-storage-integration.md)