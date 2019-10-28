---
title: 知识存储（预览版）简介
titleSuffix: Azure Cognitive Search
description: 将扩充文档发送到 Azure 存储，随后可以在 Azure 认知搜索和其他应用中查看、整形和使用扩充文档。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 82f8606f4b4201833667347d3ed16fdd73f70a36
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790355"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Azure 认知搜索中的知识存储简介

> [!Note]
> 知识存储目前为预览版，不适合在生产环境中使用。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供了此功能。 目前不支持 .NET SDK。
>

知识存储是 Azure 认知搜索中的一项功能，它可以保留 [AI 扩充管道](cognitive-search-concept-intro.md)的输出供后续分析或进行其他下游处理。 扩充文档是管道的输出，是基于使用 AI 流程提取、结构化和分析的内容创建的。  在标准的 AI 管道中，扩充文档是临时的，仅在编制索引期间使用，然后被丢弃。 扩充文档将通过知识存储保存起来。 

如果你以前曾经在 Azure 认知搜索中用过认知技能，则已经知道技能集会通过一系列扩充来迁移文档。  结果可以是搜索索引，也可以是知识存储中的投影（此预览版中新增的）。 搜索索引和知识存储这两种输出共享相同的内容，但以非常不同的方式存储和使用。

在物理上，知识存储是一个 [Azure 存储](https://docs.microsoft.com/azure/storage/common/storage-account-overview)，可以是 Azure 表存储和/或 Azure Blob 存储。 任何可以连接到 Azure 存储的工具或进程都可以使用知识存储的内容。

![管道中的知识存储示意图](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "管道中的知识存储示意图")

投影是用于在知识存储中构造数据的机制。 例如，通过投影，可以选择是将输出保存为单个 blob 还是相关表的集合。 

若要使用知识存储，请将 `knowledgeStore` 元素添加到在索引管道中定义步进式操作的技能集。 在执行期间，Azure 认知搜索会在 Azure 存储帐户中创建一个空间，并根据配置将扩充文档投影为 Blob 或表。

## <a name="benefits-of-knowledge-store"></a>知识存储的优势

知识存储提供了结构、上下文和实际内容，这些收集自非结构化和半结构化数据文件（如 Blob）、已经过分析的图像文件，或甚至收集自已整形到新表单中的结构化数据。 在为此预览版撰写的[分步演练](knowledge-store-howto.md)中，你可以获得第一手信息：了解密集型 JSON 文档如何分区成子结构、如何重新构造成新结构，以及如何可用于下游进程（如机器学习和数据科学工作负荷）。

尽管了解 AI 扩充管道可以生成什么十分有用，但知识存储的真正强大之处是能够整形数据。 你可以从基本技能集入手，然后循环访问它以添加越来越多的结构级别，这样就能将它们合并成新结构，可用于除 Azure 认知搜索以外的其他应用。

知识存储的优势已枚举如下：

+ 在除搜索以外的[分析和报表工具](#tools-and-apps)中使用扩充文档。 含 Power Query 的 Power BI 就是个极具吸引力的选择，但只要是能连接到 Azure 存储，任何工具或应用都可以从你创建的知识存储中拉取文档。

+ 优化 AI 索引管道，同时调试步骤和技能集定义。 知识存储展示 AI 索引管道中的技能集定义的结果。 这些结果可用于设计更好的技能集，因为你可以清楚地看到扩充是什么样的。 可以使用 Azure 存储中的[存储资源管理器](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)来查看知识存储的内容。

+ 将数据整形到新表单中。 整形在技能集中编码化，但重点是技能集现在可以提供此功能。 Azure 认知搜索中的[整形程序技能](cognitive-search-skill-shaper.md)已扩展为包含此任务。 通过整形，可以定义与数据预期用途保持一致的投影，同时保留关系。

> [!Note]
> 不熟悉使用认知服务的 AI 扩充？ Azure 认知搜索与认知服务视觉和语言功能集成，以对图像文件使用光学字符识别 (OCR)、对文本文件使用实体识别和关键短语提取等来提取和扩充源数据。 有关详细信息，请参阅 [Azure 认知搜索中的 AI 扩充](cognitive-search-concept-intro.md)。

## <a name="creating-a-knowledge-store"></a>创建知识存储

知识存储是[技能集](cognitive-search-working-with-skillsets.md)的一部分，而后者是[索引器](search-indexer-overview.md)的一部分。 

在此预览版中，可以使用 REST API 和 `api-version=2019-05-06-Preview`，或者通过门户中的“导入数据”向导来创建知识存储。 

### <a name="json-representation-of-a-knowledge-store"></a>知识存储的 JSON 表示形式

以下 JSON 指定 `knowledgeStore`，它是技能集的一部分，可由索引器（未显示）调用。 如果你已熟悉 AI 扩充，便知道技能集决定了每个扩充文档的创建、组织和物质。 技能集必须至少包含一个技能，如果调制的是数据结构，则该技能很可能是整形程序技能。

`knowledgeStore` 由连接和投影组成。 

+ 连接是与 Azure 搜索所在的同一区域中某个存储帐户建立的。 

+ 投影是表-对象对。 `Tables` 定义扩充文档在 Azure 表存储中的物理表达形式。 `Objects` 定义 Azure Blob 存储中的物理对象。

```json
{
  "name": "my-new-skillset",
  "description": "Example showing knowledgeStore placement in a skillset.",
  "skills":
  [
    {
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document/content/phrases/*",
    "inputs": [
        {
        "name": "text",
        "source": "/document/content/phrases/*"
        },
        {
        "name": "sentiment",
        "source": "/document/content/phrases/*/sentiment"
        }
    ],
    "outputs": [
        {
        "name": "output",
        "targetName": "analyzedText"
        }
    ]
    },
  ],
  "cognitiveServices": 
    {
    "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
    "description": "mycogsvcs resource in West US 2",
    "key": "<your key goes here>"
    },
  "knowledgeStore": { 
    "storageConnectionString": "<your connection string goes here>", 
    "projections": [ 
        { 
            "tables": [  
            { "tableName": "Reviews", "generatedKeyName": "ReviewId", "source": "/document/Review" , "sourceContext": null, "inputs": []}, 
            { "tableName": "Sentences", "generatedKeyName": "SentenceId", "source": "/document/Review/Sentences/*", "sourceContext": null, "inputs": []}, 
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/Review/Sentences/*/KeyPhrases", "sourceContext": null, "inputs": []}, 
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/Review/Sentences/*/Entities/*" ,"sourceContext": null, "inputs": []} 

            ], 
            "objects": [ 
               
            ]      
        },
        { 
            "tables": [ 
            ], 
            "objects": [ 
                { 
                "storageContainer": "Reviews", 
                "format": "json", 
                "source": "/document/Review", 
                "key": "/document/Review/Id" 
                } 
            ]      
        }        
    ]     
    } 
}
```

### <a name="sources-of-data-for-a-knowledge-store"></a>知识存储的数据源

如果知识存储是 AI 扩充管道的输出，那么输入是什么？ 要提取、扩充并最终保存到知识存储的原始数据可能源自于搜索索引器支持的任何 Azure 数据源： 

* [Azure Cosmos DB](search-howto-index-cosmosdb.md)

* [Azure Blob 存储](search-howto-indexing-azure-blob-storage.md)

* [Azure 表存储](search-howto-indexing-azure-tables.md)

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

创建的索引器和技能集将提取并扩充此内容，或将其转换为索引工作负荷的一部分，然后将结果保存到知识存储。

### <a name="rest-apis-used-in-creation-of-a-knowledge-store"></a>用于创建知识存储的 REST API

只有两个 API 具有创建知识存储所需的扩展（“创建技能集”和“创建索引器”）。 其他 API 按原样使用。

| Object | REST API | 说明 |
|--------|----------|-------------|
| 数据源 | [创建数据源](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | 标识外部 Azure 数据源的资源，数据源提供用于创建扩充文档的源数据。  |
| 技能集 | [创建技能组 (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | 在编制索引期间协调扩充管道中所用[内置技能](cognitive-search-predefined-skills.md)和[自定义认知技能](cognitive-search-custom-skill-interface.md)的资源。 技能集使用 `knowledgeStore` 定义作为子元素。 |
| index | [创建索引](https://docs.microsoft.com/rest/api/searchservice/create-index)  | 表示搜索索引的架构。 索引中的字段映射到源数据中的字段，或扩充阶段生成的字段（例如，实体识别创建的组织名称的字段）。 |
| 索引器 | [创建索引器 (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | 定义索引编制期间使用的组件（包括数据源、技能、从源和中间数据结构到目标索引的字段关联，以及索引本身）的资源。 运行索引器会触发数据引入和扩充。 输出是基于索引架构的搜索索引，其中填充有源数据，并通过技能集进行了扩充。  |

### <a name="physical-composition-of-a-knowledge-store"></a>知识存储的物理组合

 投影（`knowledgeStore` 定义的元素）阐述输出的架构和结构，使其与目标用途相符。  如果你的应用程序使用不同格式和形状的数据，则可以定义多个投影。 

投影可以表述为对象或表：

+ 作为对象，投影映射到 Blob 存储，在存储内投影保存到容器中，其中包含数据科学管道等方案的对象或分层表示形式（采用 JSON 格式）。

+ 作为表，投影映射到表存储。 表格表示形式为数据分析或导出为数据帧以用于机器学习等方案保留关系。 然后，可以将扩充投影轻松导入其他数据存储中。 

为了适应组织中的各种顾客，可以在知识存储中创建多个投影。 开发人员可能需要访问扩充文档的完整 JSON 表示形式，而数据科学家或分析师可能则需要技能集塑形的具体或模块化数据结构。

例如，如果扩充过程的目标之一是还要创建用于定型模型的数据集，将数据投影到对象存储是使用数据科学管道中数据的一种方法。 或者，若要根据扩充文档创建快速 Power BI 仪表板，使用表格投影的效果会很好。

<a name="tools-and-apps"></a>

## <a name="connecting-with-tools-and-apps"></a>与工具和应用进行连接

只要扩充存在于存储中，连接到 Azure Blob 存储或表存储的任何工具或技术，都可用于浏览、分析或使用内容。 请从以下列表入手：

+ [存储资源管理器](knowledge-store-view-storage-explorer.md)：用于查看扩充文档的结构和内容。 将这视为用于查看知识存储内容的基线工具。

+ [Power BI](knowledge-store-connect-power-bi.md)：处理数字数据时使用的报告和分析工具。

+ [Azure 数据工厂](https://docs.microsoft.com/azure/data-factory/)：用于进一步操作。


<!---
## Data lifecycle and billing

Each time you run the indexer, the cache in Azure storage is updated if the skillset definition or underlying source data has changed. As input documents are edited or deleted, changes are propagated through the annotation cache to the projections, ensuring that your projected data is a current representation of your inputs at the end of the indexer run. 

Generally speaking, pipeline processing can be an all-or-nothing operation, but Azure Search can process incremental changes, which saves you time and money.

If a document is new or updated, all skills are run. If only the skillset changes, reprocessing is scoped to just those skills and documents affected by your edit.

### Changes to a skillset
Suppose that you have a pipeline composed of multiple skills, operating over a large body of static data (for example, scanned documents), that takes 8 hours and costs $200 to create the knowledge store. Now suppose you need to tweak one of the skills in the skillset. Rather than starting over, Azure Search can determine which skill is affected, and reprocess only that skill. Cached data and projections that are unaffected by the change remain intact in the knowledge store.

### Changes in the data
Scenarios can vary considerably, but let's suppose instead of static data, you have volatile data that changes between indexer invocations. Given no changes to the skillset, you are charged for processing the delta of new and modified document. The timestamp information varies by data source, but for illustration, in a Blob container, Azure Search looks at the `lastmodified` date to determine which blobs need to be ingested.

> [!Note]
> While you can edit the data in the projections, any edits will be overwritten on the next pipeline invocation, assuming the document in source data is updated. 

### Deletions

Although Azure Search creates and updates structures and content in Azure storage, it does not delete them. Projections and cached documents continue to exist even when the skillset is deleted. As the owner of the storage account, you should delete a projection if it is no longer needed. 

### Tips for development

+ Start small with a representative sample of your data as you make significant changes to skillset composition. As your design finalizes, you can slowly add more data during later-stage development, and then roll in the entire data set when you are comfortable with the pipeline composition.

+ Retain control over indexer invocation. Indexers can run on a schedule, which is helpful for solutions that are rolled into production, but less helpful if you are actively developing your pipeline. During development, avoid schedules so that you don’t lose track of cache or projection state. Once your solution is in production and skillset composition is static, you can put the indexer on a schedule to pick up routine changes in the external source data. 

-->

<!-- ## Where do I start?

We recommend the Free service for learning purposes, but be aware that the number of free transactions is limited to 20 documents per day, per subscription.

When using multiple services, create all of your services in the same region for best performance and to minimize costs. You are not charged for bandwidth for inbound data or outbound data that goes to another service in the same region.

**Step 1: [Create an Azure Cognitive Search resource](search-create-service-portal.md)** 

**Step 2: [Create an Azure storage account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)** 

**Step 3: [Create a Cognitive Services resource](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)** 

**Step 4: [Get started with the portal](cognitive-search-quickstart-blob.md) - or - [Get started with sample data using REST and Postman](knowledge-store-howto.md)** 

You can use REST `api-version=2019-05-06-Preview` to construct an AI-based pipeline that includes knowledge store. In the newest preview API, the Skillset object provides the `knowledgeStore` definition. -->

## <a name="next-steps"></a>后续步骤

知识存储提供扩充文档的持久性，在设计技能集，或者在创建新的结构和内容供可访问 Azure 存储帐户的任何客户端应用程序使用时，知识存储非常有用。

创建扩充文档的最简单方法是使用“导入数据”向导，但也可以使用 Postman 和 REST API，想要深入了解对象的创建和引用方式时，这两个工具更有用。 

> [!div class="nextstepaction"]
> [使用门户创建知识存储](knowledge-store-create-portal.md)
> [使用 Postman 和 REST API 创建知识存储](knowledge-store-create-rest.md)
