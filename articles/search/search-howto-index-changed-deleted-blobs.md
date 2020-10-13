---
title: 已更改和已删除的 blob
titleSuffix: Azure Cognitive Search
description: 在从 Azure Blob 存储导入的初始搜索索引生成后，后续索引只能选取那些已更改或删除的 blob。 本文介绍详细信息。
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 2e73039418233c97fc20242ed7af7df14c5b47ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91534771"
---
# <a name="how-to-set-up-change-and-deletion-detection-for-blobs-in-azure-cognitive-search-indexing"></a>如何为 Azure 中的 blob 设置更改和删除检测认知搜索索引

创建初始搜索索引后，你可能想要配置后续索引器作业，以便只提取自初始运行以来创建或删除的那些文档。 对于源自 Azure Blob 存储的搜索内容，当你使用计划来触发索引时，将自动进行更改检测。 默认情况下，服务仅重新编制索引由 blob 的时间戳确定的已更改 blob `LastModified` 。 与搜索索引器支持的其他数据源相比，blob 始终具有时间戳，这无需手动设置更改检测策略。

尽管更改检测是给定的，但是删除检测不是这样。 如果要检测已删除的文档，请确保使用 "软删除" 方法。 如果彻底删除 Blob，相应的文档不会从搜索索引中删除。

可通过两种方法实现软删除方法。 下面介绍了这两种方法。

## <a name="native-blob-soft-delete-preview"></a>本机 Blob 软删除（预览版）

> [!IMPORTANT]
> 对本机 Blob 软删除的支持目前为预览版。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2020-06-30-Preview](./search-api-preview.md) 提供此功能。 目前不支持门户或 .NET SDK。

> [!NOTE]
> 使用本机 Blob 软删除策略时，索引中文档的文档键必须是 Blob 属性或 Blob 元数据。

在此方法中，你将使用 Azure Blob 存储提供的[本机 Blob 软删除](../storage/blobs/soft-delete-blob-overview.md)功能。 如果在存储帐户中启用了本机 Blob 软删除，你的数据源已设置了本地软删除策略，并且索引器找到了一个已转变为软删除状态的 Blob，则索引器会从索引中删除该文档。 为 Azure Data Lake Storage Gen2 中的 Blob 编制索引时，不支持本机 Blob 软删除策略。

使用以下步骤：

1. [为 Azure Blob 存储启用本地软删除](../storage/blobs/soft-delete-blob-overview.md)。 我们建议将保留策略设置为比索引器间隔计划大得多的值。 这样，如果在运行索引器时出现问题，或者如果有大量的文档需要编制索引，可以为索引器留出大量的时间来最终处理已软删除的 Blob。 仅当 Azure 认知搜索索引器在处理处于“已软删除”状态的 Blob 时，才会从索引中删除文档。

1. 在数据源中配置本机 Blob 软删除检测策略。 下面显示了一个示例。 由于此功能目前为预览版，因此必须使用预览版 REST API。

1. 运行索引器，或者将索引器设置为按计划运行。 当索引器运行并处理 Blob 时，将从索引中删除文档。

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.NativeBlobSoftDeleteDeletionDetectionPolicy"
        }
    }
    ```

### <a name="reindexing-un-deleted-blobs-using-native-soft-delete-policies"></a>使用本机软删除策略重新索引不删除的 blob () 

如果你从存储帐户上启用了本机软删除的 Azure Blob 存储中删除 blob，则 blob 会转换为软删除状态，从而使你能够在保持期内取消删除该 blob。 如果在索引器处理删除后撤消删除操作，则索引器不会始终为还原的 blob 编制索引。 这是因为，索引器根据 Blob 的 `LastModified` 时间戳确定要为哪些 Blob 编制索引。 如果删除了软删除的 blob，则 `LastModified` 不会更新其时间戳，因此，如果索引器已经处理了具有最新 `LastModified` 时间戳的 blob，则不会对未删除的 blob 重新编制索引。 

若要确保未删除的 blob 是重新编制索引的，需要更新 blob 的 `LastModified` 时间戳。 为此，可以重新保存该 Blob 的元数据。 无需更改元数据，但是重新保存元数据将更新 blob 的 `LastModified` 时间戳，以便索引器知道它需要对此 blob 重新索引。

## <a name="soft-delete-using-custom-metadata"></a>使用自定义元数据的软删除

在此方法中，你将使用 Blob 的元数据来指示何时应从搜索索引中删除文档。 此方法需要两个单独的操作，从索引中删除搜索文档，然后在 Azure 存储中删除 blob。

使用以下步骤：

1. 将一个自定义元数据键值对属性添加到 Blob，以告知 Azure 认知搜索该 Blob 已采用逻辑方式删除。

1. 在数据源中配置软删除列检测策略。 下面显示了一个示例。

1. 在索引器处理 blob 并从索引中删除文档后，可以在 Azure Blob 存储中删除 blob。

例如，如果某个 Blob 具有值为 `true` 的元数据属性 `IsDeleted`，以下策略会将该 Blob 视为已删除：

```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }
```

### <a name="reindexing-un-deleted-blobs-using-custom-metadata"></a>使用自定义元数据 (重新索引不删除的 blob) 

在索引器处理已删除的 blob 并从索引中删除相应的搜索文档后，如果在该 blob 的 `LastModified` 时间戳早于最后一次索引器运行的情况下还原该 blob，则不会重新访问该 blob。

如果要将该文档重新编制索引，请更改 `"softDeleteMarkerValue" : "false"` 该 blob 的，然后重新运行该索引器。

## <a name="help-us-make-azure-cognitive-search-better"></a>帮助我们改善 Azure 认知搜索

如果有功能请求或改进建议，请在 [UserVoice](https://feedback.azure.com/forums/263029-azure-search/) 上提供相关意见。 如果需要使用现有功能的帮助，请在 [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/18870)上发布问题。

## <a name="next-steps"></a>后续步骤

* [Azure 认知搜索中的索引器](search-indexer-overview.md)
* [如何配置 blob 索引器](search-howto-indexing-azure-blob-storage.md)
* [Blob 索引概述](search-blob-storage-integration.md)