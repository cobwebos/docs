---
title: "使用 Azure 搜索为 Azure Blob 存储编制索引"
description: "了解如何使用 Azure 搜索为 Azure Blob 存储编制索引，以及从文档中提取文本"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 2a5968f4-6768-4e16-84d0-8b995592f36a
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 11/24/2016
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: 2b62ddb83b35194b9fcd23c60773085a9551b172
ms.openlocfilehash: 041b47ed2aba11d45ed6ae02dadb73916046dd78

---

# <a name="indexing-documents-in-azure-blob-storage-with-azure-search"></a>使用 Azure 搜索为 Azure Blob 存储中的文档编制索引
本文说明如何使用 Azure 搜索服务为存储在 Azure Blob 存储中的文档（例如 PDF、Microsoft Office 文档和其他多种常用格式的文档）编制索引。 使用全新的 Azure 搜索服务 Blob 索引器可以快速顺利地完成此过程。

## <a name="supported-document-formats"></a>支持的文档格式
Blob 索引器可从以下文档格式提取文本：

* PDF
* Microsoft Office 格式：DOCX/DOC、XLSX/XLS、PPTX/PPT、MSG（Outlook 电子邮件）  
* HTML
* XML
* ZIP
* EML
* 纯文本文件  
* JSON（有关详细信息，请参阅[为 JSON Blob 编制索引](search-howto-index-json-blobs.md)）
* CSV（有关详细信息，请参阅[为 CSV Blob 编制索引](search-howto-index-csv-blobs.md)预览版功能）

> [!IMPORTANT]
> 对 CSV 和 JSON 文件的支持目前以预览版提供。 仅当使用 REST API **2015-02-28-Preview** 版本或 .NET SDK 2.x-preview 版本时，才支持这些格式。 请记住，预览版 API 仅供测试和评估，不应在生产环境中使用。
>
>

## <a name="setting-up-blob-indexing"></a>设置 Blob 索引
可以使用以下方式设置 Azure Blob 存储索引器：

* [Azure 门户](https://ms.portal.azure.com)
* Azure 搜索 [REST API](https://msdn.microsoft.com/library/azure/dn946891.aspx)
* Azure 搜索 .NET SDK [版本 2.0-preview](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx)

> [!NOTE]
> 某些功能（例如字段映射）在门户中尚不可用，必须以编程方式使用。
>
>

本文将使用 REST API 设置一个索引器。 首先，我们将创建数据源，然后创建索引，最后配置索引器。

之后，将详细介绍 Blob 索引器如何分析 Blob，如何选择要编制索引的 Blob，如何处理不受支持内容类型的 Blob，以及可用的配置设置。 

### <a name="step-1-create-a-data-source"></a>步骤 1：创建数据源
数据源指定要编制索引的数据、访问数据所需的凭据和有效标识数据更改（新行、修改的行或删除的行）的策略。 一个数据源可供同一搜索服务中的多个索引器使用。

若要为 Blob 编制索引，数据源必须具有以下属性：

* **name** 是搜索服务中数据源的唯一名称。
* **type** 必须是 `azureblob`。
* **credentials** 以 `credentials.connectionString` 参数的形式提供存储帐户连接字符串。 可从 Azure 门户获取连接字符串：导航到所需的存储帐户边栏选项卡，选择“设置” > “密钥”，然后使用“主连接字符串”或“辅助连接字符串”值。
* **container** 指定存储帐户中的容器。 默认情况下，容器中的所有 Blob 都可检索。 如果只想为特定虚拟目录中的 Blob 编制索引，可以使用可选的 **query** 参数指定该目录。

以下示例演示了一个数据源定义：

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   

有关创建数据源 API 的详细信息，请参阅[创建数据源](https://msdn.microsoft.com/library/azure/dn946876.aspx)。

### <a name="step-2-create-an-index"></a>步骤 2：创建索引
索引指定文档、属性和其他构造中可以塑造搜索体验的字段。  

若要为 Blob 编制索引，请确保索引包含用于存储 Blob 的可搜索 `content` 字段。

    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }

有关创建索引 API，请参阅[创建索引](https://msdn.microsoft.com/library/dn798941.aspx)

### <a name="step-3-create-an-indexer"></a>步骤 3：创建索引器
索引器可以连接数据源与目标搜索索引，并提供计划信息，以便你可以自动刷新数据。 创建索引和数据源后，创建引用数据源和目标索引的索引器的过程相对就很简单。 例如：

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

此索引器每隔两小时运行一次（已将计划间隔设置为“PT2H”）。 若要每隔 30 分钟运行索引器一次，可将间隔设置为“PT30M”。 支持的最短间隔为 5 分钟。 计划是可选的 - 如果省略，索引器只在创建后运行一次。 但是，可以随时根据需要运行索引器。   

有关创建索引器 API 的更多详细信息，请参阅[创建索引器](https://msdn.microsoft.com/library/azure/dn946899.aspx)。

## <a name="how-azure-search-indexes-blobs"></a>Azure 搜索如何为 Blob 编制索引

根据具体的[索引器配置](#PartsOfBlobToIndex)，Blob 索引器可以仅为存储元数据编制索引（如果你只关注元数据，而无需为 Blob 的内容编制索引，则此功能非常有用）、为存储元数据和内容元数据编制索引，或者同时为元数据和文本内容编制索引。 默认情况下，索引器提取元数据和内容。 

> [!NOTE]
> 默认情况下，包含结构化内容（例如 JSON、CSV 或 XML）的 Blob 将以单一文本区块的形式编制索引。 如果想要以结构化方法为 JSON 和 CSV Blob 编制索引，请参阅[为 JSON Blob 编制索引](search-howto-index-json-blobs.md)和[为 CSV Blob 编制索引](search-howto-index-csv-blobs.md)预览版功能。 目前不支持分析 XML 内容；如果有这方面的需要，请通过 [UserVoice](https://feedback.azure.com/forums/263029-azure-search) 提供建议。
> 
> 复合或嵌入式文档（例如 ZIP 存档，或者嵌入了带附件 Outlook 电子邮件的 Word 文档）也以单一文档的形式编制索引。

* 文档的整个文本内容将提取到名为 `content` 的字符串字段中。
* Blob 中用户指定的元数据属性（如果有）将逐字提取。
* 标准 Blob 元数据属性将提取到以下字段中：

  * **metadata\_storage\_name** (Edm.String) - Blob 的文件名。 例如，对于 Blob /my-container/my-folder/subfolder/resume.pdf 而言，此字段的值是 `resume.pdf`。
  * **metadata\_storage\_path** (Edm.String) - Blob 的完整 URI（包括存储帐户）。 例如： `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * **metadata\_storage\_content\_type** (Edm.String) - 用于上载 Blob 的代码指定的内容类型。 例如，`application/octet-stream`。
  * **metadata\_storage\_last\_modified** (Edm.DateTimeOffset) - 上次修改 Blob 的时间戳。 Azure 搜索服务使用此时间戳来识别已更改的 Blob，避免在初次编制索引之后再次为所有内容编制索引。
  * **metadata\_storage\_size** (Edm.Int64) - Blob 大小，以字节为单位。
  * **metadata\_storage\_content\_md5** (Edm.String) - Blob 内容的 MD5 哈希（如果有）。
* 特定于每种文档格式的元数据属性将提取到[此处](#ContentSpecificMetadata)所列的字段。

无需在搜索索引中针对上述所有属性定义字段 - 系统只捕获应用程序所需的属性。

> [!NOTE]
> 通常，现有索引中的字段名称与文档提取期间所生成的字段名称不同。 可以使用**字段映射**将 Azure 搜索服务提供的属性名称映射到搜索索引中的字段名称。 后面将会提供字段映射的用法示例。
>
>

### <a name="picking-the-document-key-field-and-dealing-with-different-field-names"></a>选择文档键字段，然后处理不同的字段名称
在 Azure 搜索中，文档键唯一标识某个文档。 每个搜索索引只能有一个类型为 Edm.String 的键字段。 键字段对于要添加到索引的每个文档必不可少（事实上它是唯一的必填字段）。  

应该仔细考虑要将提取的哪个字段映射到索引的键字段。 候选字段包括：

* **metadata\_storage\_name** - 这可能是一个便利的候选项，但是请注意，1) 名称可能不唯一，因为不同的文件夹中可能有同名的 Blob；2) 名称中包含的字符可能在文档键中无效，例如短划线。 可以使用 `base64Encode` [字段映射函数](search-indexer-field-mappings.md#base64EncodeFunction)处理无效的字符 - 如果这样做，请记得在将这些字符传入 Lookup 等 API 调用时为文档键编码。 （例如，在 .NET 中，可以使用 [UrlTokenEncode 方法](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx)实现此目的）。
* **metadata\_storage\_path** - 使用完整路径可确保唯一性，但是路径必定会包含 `/` 字符，而该字符[在文档键中无效](https://msdn.microsoft.com/library/azure/dn857353.aspx)。  如上所述，可以选择使用 `base64Encode` [函数](search-indexer-field-mappings.md#base64EncodeFunction)为键编码。
* 如果上述所有做法都不起作用，可将一个自定义元数据属性添加到 Blob。 但是，这种做法需要通过 Blob 上载过程将该元数据属性添加到所有 Blob。 由于键是必需的属性，因此没有该属性的所有 Blob 都无法编制索引。

> [!IMPORTANT]
> 如果索引中没有键字段的显式映射，Azure 搜索服务将自动使用 `metadata_storage_path`（上述第二个选项）作为键并对该键进行 base-64 编码。
>
>

本示例选择 `metadata_storage_name` 字段作为文档键。 同时，假设索引具有名为 `key` 的键字段，以及一个用于存储文档大小的 `fileSize` 字段。 若要连接所需的元素，请在创建或更新索引器时指定以下字段映射：

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

若要将所有元素合并在一起，可按如下所示添加字段映射，并为现有索引器的键启用 base-64 编码：

    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2016-09-01
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
> 有关字段映射的详细信息，请参阅[此文](search-indexer-field-mappings.md)。
>
>

<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>控制要为哪些 Blob 编制索引
可以控制要为哪些 Blob 编制索引，以及要跳过哪些 Blob。

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>只为具有特定文件扩展名的 Blob 编制索引
使用 `indexedFileNameExtensions` 索引器配置参数可以做到只为具有指定扩展名的 Blob 编制索引。 值是包含文件扩展名（包括前置句点）逗号分隔列表的字符串。 例如，如果只要为 .PDF 和 .DOCX Blob 编制索引，请执行以下操作：

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions"></a>排除具有特定文件扩展名的 Blob
使用 `excludedFileNameExtensions` 配置参数可在编制索引时排除具有特定文件扩展名的 Blob。 值是包含文件扩展名（包括前置句点）逗号分隔列表的字符串。 例如，若要为所有 Blob 编制索引，但要排除具有 .PNG 和 .JPEG 扩展名的 Blob，请执行以下操作：

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

如果同时存在 `indexedFileNameExtensions` 和 `excludedFileNameExtensions` 参数，Azure 搜索服务会先查找 `indexedFileNameExtensions`，再查找 `excludedFileNameExtensions`。 这意味着，如果两个列表中存在同一个文件扩展名，将从索引编制中排除该扩展名。

### <a name="dealing-with-unsupported-content-types"></a>处理不受支持的内容类型

默认情况下，Blob 索引器一旦遇到包含不受支持内容类型（例如图像）的 Blob 时，就会立即停止。 当然，你可以使用 `excludedFileNameExtensions` 参数跳过某些内容类型。 但是，你可能需要在未事先了解所有可能的内容类型的情况下，为 Blob 编制索引。 若要在遇到了不受支持的内容类型时继续编制索引，可将 `failOnUnsupportedContentType` 配置参数设置为 `false`： 

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    } 

### <a name="ignoring-parsing-errors"></a>忽略分析错误

Azure 搜索的文档提取逻辑并不完美，有时无法分析包含受支持内容类型的文档，例如 .DOCX 或 .PDF。 如果在遇到这种情况时不希望中断索引编制，可将 `maxFailedItems` 和 `maxFailedItemsPerBatch` 配置参数设置为某些合理值。 例如： 

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    } 

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>控制要为 Blob 中的哪些部分编制索引

可以使用 `dataToExtract` 配置参数控制要为 Blob 中的哪些部分编制索引。 该参数采用以下值： 

* `storageMetadata` - 指定仅为[标准 Blob 属性和用户指定的元数据](../storage/storage-properties-metadata.md)编制索引。
* `allMetadata` - 指定要为从 Blob 内容中提取的存储元数据和[内容类型特定的元数据](#ContentSpecificMetadata)编制索引。
* `contentAndMetadata` - 指定要为从 Blob 中提取的所有元数据和文本内容编制索引。 这是默认值。

例如，如果只要为存储元数据编制索引，请使用： 

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>使用 Blob 元数据来控制如何为 Blob 编制索引

上述配置参数适用于所有 Blob。 有时，你可能想要控制如何为*单个 Blob* 编制索引。 为此，可以添加以下 Blob 元数据属性和值：

| 属性名称 | 属性值 | 说明 |
| --- | --- | --- |
| AzureSearch_Skip |"true" |指示 Blob 索引器完全跳过该 Blob， 既不尝试提取元数据，也不提取内容。 如果特定的 Blob 反复失败并且中断编制索引过程，此属性非常有用。 |
| AzureSearch_SkipContent |"true" |此属性等效于[上面](#PartsOfBlobToIndex)所述的与特定 Blob 相关的 `"dataToExtract" : "allMetadata"` 设置。 |

## <a name="incremental-indexing-and-deletion-detection"></a>增量索引和删除检测
将 Blob 索引器设置为按计划运行时，它只根据 Blob 的 `LastModified` 时间戳，为更改的 Blob 重新编制索引。

> [!NOTE]
> 无需指定更改检测策略 - 系统将自动启用增量索引。

若要支持删除文档，请使用“软删除”方法。 如果彻底删除 Blob，相应的文档不会从搜索索引中删除。 应该改用以下步骤：  

1. 将一个自定义元数据属性添加到 Blob，告知 Azure 搜索该 Blob 已按逻辑方式删除
2. 在数据源上配置软删除检测策略
3. 索引器处理 Blob 后（如索引器状态 API 所示），可以使用物理方式删除该 Blob

例如，如果某个 Blob 具有值为 `true` 的元数据属性 `IsDeleted`，以下策略会将该 Blob 视为已删除：

    PUT https://[service name].search.windows.net/datasources?api-version=2016-09-01
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

Blob 编制索引可能是一个耗时的过程。 如果有几百万个 Blob 需要编制索引，可以将数据分区，并使用多个索引器来并行处理数据，从而加快索引编制的速度。 设置方法如下： 

- 将数据分区到多个 Blob 容器或虚拟文件夹 
- 设置多个 Azure 搜索数据源，为每个容器或文件夹各设置一个。 若要指向某个 Blob 文件夹，请使用 `query` 参数： 

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- 为每个数据源创建相应的索引器。 所有索引器可以指向同一目标搜索索引。  

<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>特定于内容类型的元数据属性
下表汇总了针对每种文档格式执行的处理，并说明了 Azure 搜索提取的元数据属性。

| 文档格式/内容类型 | 特定于内容类型的元数据属性 | 处理详细信息 |
| --- | --- | --- |
| HTML (`text/html`) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |剥离 HTML 标记并提取文本 |
| PDF (`application/pdf`) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |提取文本，包括嵌入的文档（不包括图像） |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |提取文本，包括嵌入的文档 |
| DOC (application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |提取文本，包括嵌入的文档 |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |提取文本，包括嵌入的文档 |
| XLS (application/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |提取文本，包括嵌入的文档 |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |提取文本，包括嵌入的文档 |
| PPT (application/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |提取文本，包括嵌入的文档 |
| MSG (application/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_message_bcc`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |提取文本，包括附件 |
| ZIP (application/zip) |`metadata_content_type` |从存档中的所有文档提取文本 |
| XML (application/xml) |`metadata_content_type`</br>`metadata_content_encoding`</br> |剥离 XML 标记并提取文本 |
| JSON (application/json) |`metadata_content_type`</br>`metadata_content_encoding` |提取文本<br/>注意：如果需要从 JSON Blob 提取多个文档字段，请参阅[为 JSON Blob 编制索引](search-howto-index-json-blobs.md)了解详细信息 |
| EML (message/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |提取文本，包括附件 |
| 纯文本 (text/plain) |`metadata_content_type`</br>`metadata_content_encoding`</br> | |

## <a name="help-us-make-azure-search-better"></a>帮助我们改进 Azure 搜索
如果想要请求新功能或者在改进方面有什么看法，敬请通过 [UserVoice 站点](https://feedback.azure.com/forums/263029-azure-search/)告诉我们。



<!--HONumber=Nov16_HO4-->


