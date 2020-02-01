---
title: 搜索 Azure Blob 存储内容
titleSuffix: Azure Cognitive Search
description: 了解如何为 Azure Blob 存储编制索引，以及如何利用 Azure 认知搜索从文档中提取文本。
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: cd2e34be7ef55c4ee6d18c6db6010134a7d935d1
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76895957"
---
# <a name="how-to-index-documents-in-azure-blob-storage-with-azure-cognitive-search"></a>如何在 azure Blob 存储中用 Azure 认知搜索索引文档

本文介绍如何使用 Azure 认知搜索来索引存储在 Azure Blob 存储中的文档（如 Pdf、Microsoft Office 文档和多种其他常见格式）。 首先，它介绍了设置和配置 blob 索引器的基本知识。 然后，它可以更深入地了解你可能会遇到的行为和方案。

<a name="SupportedFormats"></a>

## <a name="supported-document-formats"></a>支持的文档格式
Blob 索引器可以从以下文档格式提取文本：

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>设置 blob 索引
可以使用以下内容设置 Azure Blob 存储索引器：

* [Azure 门户](https://ms.portal.azure.com)
* Azure 认知搜索[REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Azure 认知搜索[.NET SDK](https://aka.ms/search-sdk)

> [!NOTE]
> 某些功能（例如，字段映射）在门户中尚不可用，必须以编程方式使用。
>

在这里，我们使用 REST API 来演示流。

### <a name="step-1-create-a-data-source"></a>步骤1：创建数据源
数据源指定要编制索引的数据、访问数据所需的凭据和有效标识数据更改（新行、修改的行或删除的行）的策略。 同一搜索服务中的多个索引器可以使用数据源。

对于 blob 索引，数据源必须具有以下必需属性：

* **name**是搜索服务中数据源的唯一名称。
* 必须 `azureblob`**类型**。
* **凭据**提供存储帐户连接字符串作为 `credentials.connectionString` 参数。 有关详细信息，请参阅下面的[如何指定凭据](#Credentials)。
* **容器**指定存储帐户中的容器。 默认情况下，容器中的所有 blob 都可检索。 如果只想要为特定虚拟目录中的 blob 编制索引，则可以使用可选的**查询**参数指定该目录。

若要创建数据源：

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   

有关创建数据源 API 的详细信息，请参阅[创建数据源](https://docs.microsoft.com/rest/api/searchservice/create-data-source)。

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>如何指定凭据 ####

可以通过以下方式之一提供 blob 容器的凭据：

- **完全访问存储帐户连接字符串**： `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` 可以通过导航到 "存储帐户" 边栏选项卡 > "> 密钥" （对于经典存储帐户）或 "设置 > 访问密钥" （对于 Azure 资源管理器存储帐户），从 Azure 门户中获取连接字符串。
- **存储帐户共享访问签名**（SAS）连接字符串： `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` SAS 应该具有对容器和对象（在本例中为 blob）的列表和读取权限。
-  **容器共享访问签名**： `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl` SAS 应该具有容器上的 "列出" 和 "读取" 权限。

有关存储共享访问签名的详细信息，请参阅[使用共享访问签名](../storage/common/storage-dotnet-shared-access-signature-part-1.md)。

> [!NOTE]
> 如果你使用 SAS 凭据，你将需要使用续订的签名定期更新数据源凭据，以防止其过期。 如果 SAS 凭据过期，索引器会失败并出现类似于 `Credentials provided in the connection string are invalid or have expired.`的错误消息。  

### <a name="step-2-create-an-index"></a>步骤2：创建索引
索引指定文档、属性和其他构成搜索体验的构造中的字段。

下面介绍如何使用可搜索的 `content` 字段创建索引以存储从 blob 中提取的文本：   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }

有关创建索引的详细信息，请参阅[创建索引](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="step-3-create-an-indexer"></a>步骤3：创建索引器
索引器将数据源连接到目标搜索索引，并提供自动执行数据刷新的计划。

创建索引和数据源后，就可以创建索引器了：

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

此索引器每两小时运行一次（计划间隔设置为 "PT2H"）。 若要每隔30分钟运行一次索引器，请将间隔设置为 "PT30M"。 支持的最短间隔为5分钟。 计划是可选的-如果省略，则索引器在创建后只运行一次。 不过，你可以随时按需运行索引器。   

有关创建索引器 API 的更多详细信息，请参阅[Create 索引器](https://docs.microsoft.com/rest/api/searchservice/create-indexer)。

有关定义索引器计划的详细信息，请参阅[如何为 Azure 认知搜索计划索引器](search-howto-schedule-indexers.md)。

<a name="how-azure-search-indexes-blobs"></a>

## <a name="how-azure-cognitive-search-indexes-blobs"></a>Azure 认知搜索如何为 blob 编制索引

根据[索引器的配置](#PartsOfBlobToIndex)，blob 索引器只能索引存储元数据（仅关心元数据，而不需要对 blob 的内容进行索引）、存储和内容元数据或元数据和文本内容。 默认情况下，索引器提取元数据和内容。

> [!NOTE]
> 默认情况下，具有结构化内容（如 JSON 或 CSV）的 blob 被索引为单个文本块。 如果要以结构化的方式为 JSON 和 CSV blob 编制索引，请参阅为[json Blob 编制](search-howto-index-json-blobs.md)索引和为[csv blob 编制](search-howto-index-csv-blobs.md)索引以获取详细信息。
>
> 复合文档或嵌入式文档（如 ZIP 存档或带有包含附件的嵌入 Outlook 电子邮件的 Word 文档）也作为单个文档进行索引。

* 文档的文本内容将提取到名为 `content`的字符串字段中。

> [!NOTE]
> Azure 认知搜索会根据定价层限制其提取的文本大小：用于免费层的32000个字符、适用于 Basic 的64000、适用于 standard 8000000 的、标准 S2 的4000000以及标准 S3 的16000000。 已截断的文档的索引器状态响应中包含警告。  

* Blob 上存在的用户指定的元数据属性（如果有）将逐字提取。
* 标准 blob 元数据属性将提取到以下字段中：

  * **\_存储的元数据\_名称**（Edm）-blob 的文件名。 例如，如果有 blob/my-container/my-folder/subfolder/resume.pdf，则此字段的值为 `resume.pdf`。
  * **\_存储\_路径**（Edm）的元数据-blob （包括存储帐户）的完整 URI。 例如，`https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * **\_存储\_内容的元数据\_类型**（Edm）-内容类型，由用于上传 blob 的代码指定。 例如 `application/octet-stream` 。
  * **\_上次\_修改的元数据\_存储区**（Edm）-blob 的上次修改时间戳。 Azure 认知搜索使用此时间戳来识别已更改的 blob，以避免在初始索引后对所有内容进行索引。
  * **\_存储\_大小**（Edm）的元数据-blob 大小（字节）。
  * **元数据\_存储\_内容\_md5** （Edm）的 md5 哈希（如果可用）。
  * **\_存储\_sas\_标记**（Edm）的元数据-可由[自定义技能](cognitive-search-custom-skill-interface.md)用于获取对 blob 的访问权限的临时 sas 令牌。 不应存储此令牌以供以后使用，因为它可能会过期。

* 特定于每个文档格式的元数据属性将提取到[此处](#ContentSpecificMetadata)列出的字段中。

不需要在搜索索引中定义上述所有属性的字段-只需捕获应用程序所需的属性即可。

> [!NOTE]
> 通常，现有索引中的字段名称将不同于文档提取过程中生成的字段名称。 可以使用**字段映射**将 Azure 认知搜索提供的属性名称映射到搜索索引中的字段名称。 下面将显示一个字段映射使用示例。
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>定义文档键和字段映射
在 Azure 认知搜索中，文档键唯一标识一个文档。 每个搜索索引必须刚好有一个类型为 Edm 的键字段。 键字段对于要添加到索引中的每个文档都是必需的（它实际上是唯一的必填字段）。  

应该仔细考虑哪些提取的字段应该映射到索引的键字段。 候选项包括：

* **\_存储\_名称的元数据**。这可能是一个很方便的候选项，但请注意，这可能是一个不唯一的名称，因为在不同的文件夹中可能有同名的 blob，并且2）该名称可能包含在文档键中无效的字符，如虚线。 您可以使用 `base64Encode`[字段映射函数](search-indexer-field-mappings.md#base64EncodeFunction)处理无效字符-如果这样做，请记得在将文档键传入 API 调用（如查找）时对其进行编码。 （例如，在 .NET 中，可以使用[httpserverutility.urltokenencode 方法](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx)来实现此目的）。
* **\_存储\_路径的元数据**-使用完整路径可确保唯一性，但该路径明确包含[在文档键中无效](https://docs.microsoft.com/rest/api/searchservice/naming-rules)的 `/` 字符。  如上所述，你可以选择使用 `base64Encode`[函数](search-indexer-field-mappings.md#base64EncodeFunction)对密钥进行编码。
* 如果上面的选项均不适用，则可以将自定义元数据属性添加到 blob。 但是，此选项需要 blob 上传过程将该元数据属性添加到所有 blob。 由于密钥是必需的属性，因此不具有该属性的所有 blob 都将无法进行索引。

> [!IMPORTANT]
> 如果索引中没有键字段的显式映射，Azure 认知搜索会自动使用 `metadata_storage_path` 作为键，并以64编码的密钥值（上面的第二个选项）进行。
>
>

在此示例中，让我们选取 "`metadata_storage_name`" 字段作为文档键。 假设您的索引具有一个名为 `key` 的键字段和一个用于存储文档大小的字段 `fileSize`。 若要根据需要连接，请在创建或更新索引器时指定以下字段映射：

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

为了将这一切结合起来，下面介绍了如何添加字段映射，并为现有索引器启用密钥的64编码：

    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2019-05-06
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

> [!NOTE]
> 若要详细了解字段映射，请参阅[此文](search-indexer-field-mappings.md)。
>
>

<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>控制为哪些 blob 编制索引
您可以控制要为哪些 blob 编制索引，并跳过哪些 blob。

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>仅对具有特定文件扩展名的 blob 编制索引
你可以通过使用 `indexedFileNameExtensions` 索引器配置参数来仅为具有指定的文件扩展名的 blob 编制索引。 该值是一个字符串，其中包含以逗号分隔的文件扩展名列表（带有前导句点）。 例如，若为，则只对进行索引。PDF 和.DOCX blob，执行以下操作：

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions"></a>排除具有特定文件扩展名的 blob
您可以使用 `excludedFileNameExtensions` 配置参数从索引中排除具有特定文件扩展名的 blob。 该值是一个字符串，其中包含以逗号分隔的文件扩展名列表（带有前导句点）。 例如，若要对除包含的 blob 之外的所有 blob 进行索引。PNG 和。JPEG 扩展，请执行以下操作：

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

如果同时存在 `indexedFileNameExtensions` 和 `excludedFileNameExtensions` 参数，Azure 认知搜索首先查看 `indexedFileNameExtensions`，然后在 `excludedFileNameExtensions`。 这意味着，如果两个列表中存在相同的文件扩展名，则将从索引中排除它。

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>控制对 blob 的哪些部分进行索引

您可以使用 `dataToExtract` 配置参数控制 blob 的哪些部分进行索引。 它可以采用以下值：

* `storageMetadata`-指定仅为[标准 blob 属性和用户指定的元数据](../storage/blobs/storage-properties-metadata.md)编制索引。
* `allMetadata`-指定为从 blob 内容中提取的存储元数据和[内容类型特定的元数据](#ContentSpecificMetadata)编制索引。
* `contentAndMetadata`-指定为从 blob 中提取的所有元数据和文本内容编制索引。 这是默认值。

例如，若要仅对存储元数据编制索引，请使用：

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>使用 blob 元数据控制如何为 blob 编制索引

上述配置参数适用于所有 blob。 有时，你可能想要控制*各个 blob*的索引方式。 可以通过添加以下 blob 元数据属性和值来实现此目的：

| 属性名 | 属性值 | 说明 |
| --- | --- | --- |
| AzureSearch_Skip |true |指示 blob 索引器完全跳过 blob。 不尝试进行元数据和内容提取。 当特定的 blob 重复失败并中断索引过程时，这非常有用。 |
| AzureSearch_SkipContent |true |这等效于[上面](#PartsOfBlobToIndex)所述的将作用域限定为特定 blob 的 `"dataToExtract" : "allMetadata"` 设置。 |

<a name="DealingWithErrors"></a>
## <a name="dealing-with-errors"></a>处理错误

默认情况下，blob 索引器在遇到具有不受支持的内容类型（例如，图像）的 blob 时立即停止。 当然，您可以使用 `excludedFileNameExtensions` 参数跳过某些内容类型。 但是，你可能需要提前为 blob 编制索引，而无需事先知道所有可能的内容类型。 若要在遇到不受支持的内容类型时继续编制索引，请将 `failOnUnsupportedContentType` 配置参数设置为 `false`：

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }

对于某些 blob，Azure 认知搜索无法确定内容类型，或者无法处理其他受支持的内容类型的文档。 若要忽略此失败模式，请将 `failOnUnprocessableDocument` 配置参数设置为 false：

      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }

Azure 认知搜索限制已编制索引的 blob 的大小。 [Azure 认知搜索中的服务限制](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)中记录了这些限制。 默认情况下，超大的 blob 被视为错误。 但是，如果将 `indexStorageMetadataOnlyForOversizedDocuments` 配置参数设置为 true，则仍可为超大 blob 的存储元数据编制索引： 

    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }

如果在任意处理时间点发生错误，还可以继续编制索引，在分析 blob 或将文档添加到索引时也是如此。 若要忽略特定数量的错误，请将 `maxFailedItems` 和 `maxFailedItemsPerBatch` 配置参数设置为所需的值。 例如:

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }

## <a name="incremental-indexing-and-deletion-detection"></a>增量索引和删除检测
将 blob 索引器设置为按计划运行时，它仅重新编制索引由 blob 的 `LastModified` 时间戳确定的已更改 blob。

> [!NOTE]
> 无需指定更改检测策略-会自动启用增量索引。

若要支持删除文档，请使用 "软删除" 方法。 如果你完全删除这些 blob，则不会从搜索索引中删除相应的文档。 请改用以下步骤：  

1. 将自定义元数据属性添加到 blob，以指示 Azure 认知搜索逻辑删除它
2. 在数据源上配置软删除检测策略
3. 一旦索引器处理 blob （如索引器状态 API 所示），就可以实际删除该 blob。

例如，如果一个 blob 的元数据属性与值 `true``IsDeleted`，则以下策略会将其删除：

    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",     
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }   

## <a name="indexing-large-datasets"></a>为大型数据集编制索引

索引 blob 可能是一个耗时的过程。 如果有数百万个要编制索引的 blob，可以通过对数据进行分区并使用多个索引器来并行处理数据，从而加快索引。 下面介绍了如何进行此设置：

- 将你的数据划分到多个 blob 容器或虚拟文件夹中
- 设置多个 Azure 认知搜索数据源，每个容器或文件夹一个。 若要指向 blob 文件夹，请使用 `query` 参数：

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- 为每个数据源创建相应的索引器。 所有索引器都可以指向同一目标搜索索引。  

- 你的服务中的一个搜索单元可以在任何给定时间运行一个索引器。 如以上所述，创建多个索引器仅在实际并行运行时才有用。 若要并行运行多个索引器，请通过创建适当数量的分区和副本来横向扩展搜索服务。 例如，如果搜索服务包含6个搜索单位（例如，2个分区 x 3 个副本），则6个索引器可同时运行，从而导致索引吞吐量增加六倍。 若要了解有关缩放和容量规划的详细信息，请参阅[在 Azure 中缩放用于查询和索引工作负荷的资源级别认知搜索](search-capacity-planning.md)。

## <a name="indexing-documents-along-with-related-data"></a>索引文档以及相关数据

你可能想要 "组装" 索引中的多个源的文档。 例如，你可能希望将 blob 中的文本与 Cosmos DB 中存储的其他元数据进行合并。 你甚至可以将推送索引 API 与各种索引器结合使用，以便从多个部分构建搜索文档。 

为了使其正常工作，所有索引器和其他组件都需要同意文档键。 有关本主题的更多详细信息，请参阅为[多个 Azure 数据源编制索引](https://docs.microsoft.com/azure/search/tutorial-multiple-data-sources)。 有关详细演练，请参阅外部文章：将[文档与 Azure 中的其他数据合并认知搜索中](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html)。

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>纯文本索引 

如果所有 blob 都包含采用相同编码的纯文本，则可以使用**文本分析模式**大幅提高索引性能。 若要使用文本分析模式，请将 `parsingMode` 配置属性设置为 `text`：

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }

默认情况下，将采用 `UTF-8` 编码。 若要指定不同的编码，请使用 `encoding` 配置属性： 

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }


<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>内容类型特定的元数据属性
下表汇总了每种文档格式的处理过程，并介绍了由 Azure 认知搜索提取的元数据属性。

| 文档格式/内容类型 | 内容类型特定的元数据属性 | 处理详细信息 |
| --- | --- | --- |
| HTML （文本/html） |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |去除 HTML 标记并提取文本 |
| PDF （应用程序/pdf） |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |提取文本，包括嵌入文档（图像除外） |
| .DOCX （application/vnd.apple.mpegurl. vnd.openxmlformats-officedocument.spreadsheetml.sheet. wordprocessingml） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |提取文本，包括嵌入的文档 |
| DOC （application/msword） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |提取文本，包括嵌入的文档 |
| DOCM （application/vnd.apple.mpegurl. macroenabled） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |提取文本，包括嵌入的文档 |
| WORD XML （application/vnd.apple.mpegurl-word2006ml） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |去除 XML 标记并提取文本 |
| WORD 2003 XML （application/vnd.apple.mpegurl. ms-wordml） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |去除 XML 标记并提取文本 |
| .XLSX （application/vnd.apple.mpegurl. vnd.openxmlformats-officedocument.spreadsheetml.sheet. vnd.openxmlformats-officedocument.spreadsheetml.sheet） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |提取文本，包括嵌入的文档 |
| XLS （application/vnd.apple.mpegurl. vnd.ms-excel） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |提取文本，包括嵌入的文档 |
| XLSM （application/vnd.apple.mpegurl. macroenabled） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |提取文本，包括嵌入的文档 |
| .PPTX （application/vnd.apple.mpegurl. vnd.openxmlformats-officedocument.spreadsheetml.sheet. presentationml） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |提取文本，包括嵌入的文档 |
| PPT （application/vnd.apple.mpegurl. ms-powerpoint） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |提取文本，包括嵌入的文档 |
| PPTM （application/vnd.apple.mpegurl. macroenabled） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |提取文本，包括嵌入的文档 |
| MSG （application/vnd.apple.mpegurl. ms-outlook） |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |提取文本，包括附件。 `metadata_message_to_email`、`metadata_message_cc_email` 和 `metadata_message_bcc_email` 都是字符串集合，其余字段是字符串。|
| ODT （application/vnd.apple.mpegurl） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |提取文本，包括嵌入的文档 |
| ODS （application/vnd.apple.mpegurl. oasis） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |提取文本，包括嵌入的文档 |
| ODP （application/vnd.apple.mpegurl. oasis） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |提取文本，包括嵌入的文档 |
| ZIP （应用程序/zip） |`metadata_content_type` |从存档中的所有文档中提取文本 |
| GZ （应用程序/gzip） |`metadata_content_type` |从存档中的所有文档中提取文本 |
| EPUB （application/EPUB + zip） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |从存档中的所有文档中提取文本 |
| XML （application/xml） |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |去除 XML 标记并提取文本 |
| JSON （application/json） |`metadata_content_type`<br/>`metadata_content_encoding` |提取文本<br/>注意：如果需要从 JSON blob 提取多个文档字段，请参阅为[json Blob 编制索引](search-howto-index-json-blobs.md)以了解详细信息 |
| .EML （message/rfc822） |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |提取文本，包括附件 |
| RTF （应用程序/rtf） |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | 提取文本|
| 纯文本（文本/普通） |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | 提取文本|


## <a name="help-us-make-azure-cognitive-search-better"></a>帮助我们提高 Azure 认知搜索
如果有功能请求或改进建议，请在我们的[UserVoice 网站](https://feedback.azure.com/forums/263029-azure-search/)上告知我们。
