---
title: 用于在导入期间抓取数据的索引器
titleSuffix: Azure Cognitive Search
description: 对 Azure SQL 数据库、SQL 托管实例、Azure Cosmos DB 或 Azure 存储进行爬网，提取可搜索的数据并填充 Azure 认知搜索索引。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 982073c77a7e876611f753c716f55c50df8b0817
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935154"
---
# <a name="indexers-in-azure-cognitive-search"></a>Azure 认知搜索中的索引器

Azure 认知搜索中的*索引器*是一种爬网程序，它从外部 Azure 数据源提取可搜索的数据和元数据，并根据索引与数据源之间字段到字段映射填充索引。 由于不需要编写任何将数据添加到索引的代码，该服务就能拉取数据，因此这种方法有时也称为“拉取模式”。

索引器基于数据源类型或平台，单个索引器适用于 Azure 上的 SQL Server、Cosmos DB、Azure 表存储和 Blob 存储。 Blob 存储索引器有特定于 Blob 内容类型的其他属性。

可以单独使用索引器来引入数据，也可以结合索引器使用多种技术来加载索引中的部分字段。

可以按需运行索引器，也可以采用每 5 分钟运行一次的定期数据刷新计划来运行索引器。 要进行更频繁的更新，则需要采用“推送模式”，便于同时更新 Azure 认知搜索和外部数据源中的数据。

## <a name="approaches-for-creating-and-managing-indexers"></a>创建及管理索引器的方法

可以使用以下方法创建和管理索引器：

* [门户 > 导入数据向导](search-import-data-portal.md)
* [服务 REST API](/rest/api/searchservice/Indexer-operations)
* [.NET SDK](/dotnet/api/microsoft.azure.search.iindexersoperations)

一开始会将新的索引器宣布为预览版功能。 预览版功能首先在 API（REST 和 .NET）中引入，然在逐渐公开发行以后再集成到门户中。 如果评估的是新索引器，则应做好编写代码的计划。

## <a name="permissions"></a>权限

与索引器相关的所有操作（包括对状态或定义的 GET 请求）都需要[管理员 api-key](search-security-api-keys.md)。

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>支持的数据源

索引器在 Azure 上抓取数据存储。

* [Azure Blob 存储](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)（预览版）
* [Azure 表存储](search-howto-indexing-azure-tables.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure SQL 数据库和 SQL 托管实例](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure 虚拟机中的 SQL Server](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)
* [SQL 托管实例](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

## <a name="indexer-stages"></a>索引器阶段

在首次运行时，当索引为空时，索引器将读取表或容器中提供的所有数据。 在后续运行中，索引器通常可以只检测并检索已更改的数据。 对于 blob 数据，更改检测是自动进行的。 对于其他数据源（如 Azure SQL 或 Cosmos DB），必须启用更改检测。

对于它引入的每个文档，索引器实现或协调多个步骤，从文档检索到最终搜索引擎 "移交" 进行索引。 （可选）索引器还有助于驱动技能组合执行和输出，假设定义了技能组合。

![索引器阶段](./media/search-indexer-overview/indexer-stages.png "索引器阶段")

### <a name="stage-1-document-cracking"></a>阶段1：文档破解

文档破解是打开文件和提取内容的过程。 根据数据源的类型，索引器将尝试执行不同的操作来提取可能可索引的内容。  

示例：  

* 当该文档是 [AZURE SQL 数据源](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)中的记录时，该索引器将提取该记录的每个字段。
* 如果文档是 [Azure Blob 存储数据源](search-howto-indexing-azure-blob-storage.md)中的 PDF 文件，则索引器将提取该文件的文本、图像和元数据。
* 如果文档是 [Cosmos DB 数据源](search-howto-index-cosmosdb.md)中的记录，则索引器将从 Cosmos DB 文档中提取字段和子字段。

### <a name="stage-2-field-mappings"></a>阶段2：字段映射 

索引器从源字段中提取文本，并将其发送到索引或知识库中的目标字段。 当字段名称和类型一致时，路径会被清除。 不过，您可能希望输出中有不同的名称或类型，在这种情况下，您需要告诉索引器如何映射字段。 此步骤在文档破解后、转换之前、在索引器读取源文档时出现。 定义 [字段映射](search-indexer-field-mappings.md)时，源字段的值将按原样发送到目标字段，而不做任何修改。 字段映射是可选的。

### <a name="stage-3-skillset-execution"></a>阶段3：技能组合执行

技能组合执行是一个可选步骤，用于调用内置或自定义 AI 处理。 对于光学字符识别，您可能需要用到图像分析形式 (OCR) ，否则可能需要语言翻译。 无论是哪种转换，技能组合执行都是扩充发生的位置。 如果索引器是管道，则可以将 [技能组合](cognitive-search-defining-skillset.md) 视为 "管道内的管道"。 技能组合有自己的一系列称为技能的步骤。

### <a name="stage-4-output-field-mappings"></a>阶段4：输出字段映射

技能组合的输出实际上是一棵称为 "扩充的文档" 的信息树。 通过输出字段映射，可以选择此树中要映射到索引中的字段的部分。 了解如何 [定义输出字段映射](cognitive-search-output-field-mapping.md)。

与将原义值从源字段关联的字段映射一样，输出字段映射会告知索引器如何将已放大文档中的已转换值关联到索引中的目标字段。 与被视为可选的字段映射不同，你始终需要为需要驻留在索引中的任何已转换内容定义输出字段映射。

下图显示了索引器阶段的示例索引器 [调试会话](cognitive-search-debug-session.md) 表示形式： "文档破解"、"字段映射"、"技能组合执行" 和 "输出字段映射"。

:::image type="content" source="media/search-indexer-overview/sample-debug-session.png" alt-text="示例调试会话" lightbox="media/search-indexer-overview/sample-debug-session.png":::

## <a name="basic-configuration-steps"></a>基本配置步骤

索引器可提供数据源独有的功能。 因此，索引器或数据源配置的某些方面会因索引器类型而不同。 但是，所有索引器的基本构成元素和要求都相同。 下面介绍所有索引器都适用的共同步骤。

### <a name="step-1-create-a-data-source"></a>步骤 1：创建数据源
索引器从数据源对象获取数据源连接。 数据源定义提供连接字符串和可能的凭据。 调用[创建数据源](/rest/api/searchservice/create-data-source) REST API 或 [DataSource 类](/dotnet/api/microsoft.azure.search.models.datasource)以创建资源。

数据源的配置和管理独立于使用数据源的索引器，这意味着多个索引器可使用一个数据源，同时加载多个索引。

### <a name="step-2-create-an-index"></a>步骤 2：创建索引
索引器会自动执行某些与数据引入相关的任务，但通常不会自动创建索引。 先决条件是必须具有预定义的索引，且索引的字段必须与外部数据源中的字段匹配。 字段需按名称和数据类型进行匹配。 有关构建索引的详细信息，请参阅 [创建索引（Azure 认知搜索 REST API）](/rest/api/searchservice/Create-Index)或[索引类](/dotnet/api/microsoft.azure.search.models.index)。 如需字段关联方面的帮助，请参阅 [Azure 认知搜索索引器中的字段映射](search-indexer-field-mappings.md)。

> [!Tip]
> 虽然不能使用索引器来生成索引，但可以使用门户中的**导入数据**向导。 大多数情况下，该向导可以根据源中现有的元数据推断索引架构，提供一个初级索引架构，该架构在向导处于活动状态时可以进行内联编辑。 在服务上创建索引以后，若要在门户中进一步进行编辑，多数情况下只能添加新字段。 可以将向导视为索引的创建工具而非修订工具。 如需手动方式的学习，请一步步完成[门户演练](search-get-started-portal.md)。

### <a name="step-3-create-and-schedule-the-indexer"></a>步骤 3：创建和计划索引器
索引器定义是一种构造，它将与数据引入相关的所有元素组合在一起。 必需元素包括数据源和索引。 可选元素包括计划和字段映射。 只有在源字段和索引字段明确对应的情况下，字段映射才是可选的。 有关构建索引器的详细信息，请参阅 [创建索引器（Azure 认知搜索 REST API）](/rest/api/searchservice/Create-Indexer)。

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>按需运行索引器

虽然通常会对索引操作进行计划，但也可使用 [Run 命令](/rest/api/searchservice/run-indexer)按需调用索引器：

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2020-06-30
api-key: [Search service admin key]
```

> [!NOTE]
> “运行 API”成功返回时，已计划索引器调用，但实际处理过程以异步方式发生。 

可以通过门户或“获取索引器状态 API”监视索引器状态。 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>获取索引器状态

可以通过[“获取索引器状态”命令](/rest/api/searchservice/get-indexer-status)检索索引器的状态和执行历史记录：

```http
GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2020-06-30
api-key: [Search service admin key]
```

响应包含总体索引器状态、最后一次（或正在进行的）索引器调用以及最近索引器调用的历史记录。

```output
{
    "status":"running",
    "lastResult": {
        "status":"success",
        "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
     },
    "executionHistory":[ {
        "status":"success",
         "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
    }]
}
```

执行历史记录包含最多 50 个最近完成的执行，它们被按反向时间顺序排序（因此，最新执行出现在响应中的第一个）。

## <a name="next-steps"></a>后续步骤
了解基本概念后，下一步是查看每种数据源特定的要求和任务。

* [Azure 虚拟机上的 Azure SQL 数据库、SQL 托管实例或 SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob 存储](search-howto-indexing-azure-blob-storage.md)
* [Azure 表存储](search-howto-indexing-azure-tables.md)
* [使用 Azure 认知搜索 Blob 索引器为 CSV blob 编制索引](search-howto-index-csv-blobs.md)
* [使用 Azure 认知搜索 Blob 索引器为 JSON blob 编制索引](search-howto-index-json-blobs.md)