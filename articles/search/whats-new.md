---
title: 服务中的新增功能
titleSuffix: Azure Cognitive Search
description: 新功能和增强功能的公告，其中包括将 Azure 搜索的服务重命名为 Azure 认知搜索。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3a2967ab40da8594b2d6372d5da816a6c76c9109
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721714"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Azure 认知搜索中的新增功能

了解该服务中的新增功能。 将此页添加为书签，以便及时了解该服务。

<a name="new-service-name"></a>

## <a name="new-service-name-for-azure-search"></a>Azure 搜索的新服务名称

Azure 搜索现已重命名为**azure 认知搜索**，以反映在核心操作中扩展的认知技能和 AI 处理的使用。 尽管认知技能增加了新功能，但使用 AI 是完全可选的。 你可以继续使用 Azure 认知搜索而无需 AI，就可以在云中创建和管理的、基于文本的基于文本的内容来构建丰富的全文搜索解决方案。 

API 版本、Nuget 包、命名空间和终结点保持不变。 现有的搜索解决方案不受服务名称更改的影响。

## <a name="feature-announcements"></a>功能公告

2019年11月4日-Ignite 会议

+ [增量索引（预览版）](cognitive-search-incremental-indexing-conceptual.md)可让你仅处理或重新处理在对扩充管道进行修改时绝对必要的步骤。 如果你有以前分析过的图像内容，这会特别有用。 将存储成本高昂分析的输出，然后将其用作附加索引或扩充的基础。

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [文档提取（预览版）](cognitive-search-skill-document-extraction.md)是在编制索引期间使用的一种认知技巧，可用于从技能组合中提取文件的内容。 以前，文档破解仅在技能组合执行之前发生。 添加了这一技能后，还可以在执行技能组合的过程中执行此操作。

+ [文本翻译（预览）](cognitive-search-skill-text-translation.md)是在编制索引时使用的一项认知技巧，用于计算文本，对于每个记录，将返回转换为指定目标语言的文本。

+ [Power BI 模板](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md)可以在 Power BI 桌面的知识存储中快速学习对已丰富内容的可视化和分析。 此模板专用于通过 "[导入数据" 向导](knowledge-store-create-portal.md)创建的 Azure 表投影。

+ 索引器中现在支持[Azure Data Lake Storage Gen2 （预览版）](search-howto-index-azure-data-lake-storage.md)、 [Cosmos DB Gremlin API （预览）](search-howto-index-cosmosdb.md)和[Cosmos DB Cassandra API （预览版）](search-howto-index-cosmosdb.md) 。 你可以使用[此表单](https://aka.ms/azure-cognitive-search/indexer-preview)进行注册。 一旦您接受了预览计划，您将收到一封确认电子邮件。

## <a name="service-updates"></a>服务更新

可在 Azure 网站上找到 Azure 认知搜索的[服务更新公告](https://azure.microsoft.com/updates/?product=search&status=all)。