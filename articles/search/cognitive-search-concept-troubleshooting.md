---
title: AI 扩充设计的提示
titleSuffix: Azure Cognitive Search
description: 在 Azure 认知搜索中设置 AI 扩充管道的提示和故障排除。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3fef5db90c3ae63a8fa48835646e09f9dfe6f023
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245481"
---
# <a name="tips-for-ai-enrichment-in-azure-cognitive-search"></a>Azure 认知搜索中的 AI 扩充的提示

本文包含一系列提示和技巧，可帮助你在 Azure 认知搜索中开始使用 AI 扩充功能。 

如果尚未这样做，请逐步学习[教程：了解如何调用 ai 扩充 api](cognitive-search-quickstart-blob.md) ，以便在将 ai 根据应用于 blob 数据源时进行练习。

## <a name="tip-1-start-with-a-small-dataset"></a>提示 1：从小数据集开始
若要快速查找问题，最好的方式是提高解决问题的速度。 若要缩短索引时间，最好的方式是减少要索引的文档数。 

一开始只使用少量文档/记录创建数据源。 文档示例应该能够很好地代表要进行索引的文档的多样性。 

通过端到端管道运行文档示例，检查结果是否符合自己的需求。 对结果满意以后，即可向数据源添加更多文件。

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>提示 2：确保数据源凭据正确
在定义一个使用数据源连接的索引器之前，数据源连接并未完成验证。 如果看到任何错误提及索引器无法访问数据，请确保：
- 连接字符串是正确的。 特别是在创建 SAS 令牌时，请确保使用 Azure 认知搜索所需的格式。 若要了解受支持的不同格式，请参阅[如何指定凭据部分](
https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials)。
- 索引器中的容器名称是正确的。

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>提示 3：了解各种适当的措施，即使是在存在某些故障的情况下
有时候，一个小的故障就会让索引器无法运行。 这没什么，只需按计划逐个解决问题即可。 但是，可能需要忽略特定类型的错误，让索引器继续运行，这样就可以看到哪些流实际上是可以运行的。

在这种情况下，可能需要告知索引器忽略错误。 为此，请在索引器定义中将 *maxFailedItems* 和 *maxFailedItemsPerBatch* 设置为 -1。

```
{
  "// rest of your indexer definition
   "parameters":
   {
      "maxFailedItems":-1,
      "maxFailedItemsPerBatch":-1
   }
}
```
## <a name="tip-4-looking-at-enriched-documents-under-the-hood"></a>提示 4：深入了解扩充的文档 
扩充的文档是在扩充期间创建但在完成处理后会删除的临时结构。

若要捕获索引编制期间创建的扩充文档的快照，请将名为 ```enriched``` 的字段添加到索引。 索引器将作为该文档的所有扩充项的字符串表示形式自动转储到字段中。

```enriched``` 字段将包含一个字符串，该字符串是内存中扩充文档的 JSON 逻辑表示形式，  但字段值是有效的 JSON 文档。 引号经过转义，因此，需将 `\"` 替换为 `"` 才能查看 JSON 格式的文档。 

扩充的字段用于调试目的，它只能帮助你了解求值表达式所针对的内容的逻辑形状。 不应依赖此字段来进行索引编制。

添加一个 ```enriched``` 字段作为索引定义的一部分，以便进行调试：

#### <a name="request-body-syntax"></a>请求正文语法
```json
{
  "fields": [
    // other fields go here.
    {
      "name": "enriched",
      "type": "Edm.String",
      "searchable": false,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```

## <a name="tip-5-expected-content-fails-to-appear"></a>提示 5：预期的内容没有出现

缺少内容可能是由于文档在索引编制过程中被丢弃。 免费层和基本层对文档大小的限制都很低。 如果文件超出此限制，则会在索引编制过程中将其丢弃。 可以在 Azure 门户中查找丢弃的文档。 在搜索服务仪表板中，双击“索引器”磁贴。 查看成功地进行索引的文档的比率。 如果不是 100%，可以单击该比率以获取更多详细信息。 

如果问题与文件大小相关，你可能会看到如下所示的错误： "blob \<文件名 >" 的大小为 \<文件大小 > 字节，超过了当前服务层的文档提取的最大大小。 " 有关索引器限制的详细信息，请参阅[服务限制](search-limits-quotas-capacity.md)。

内容没有出现的另一原因可能与输入/输出映射错误相关。 例如，输出目标名称为“People”，但索引字段名称为“people”（小写）。 系统可能会针对整个管道返回“201 成功”消息，因此你认为索引编制成功，但实际上有一个字段是空的。 

## <a name="tip-6-extend-processing-beyond-maximum-run-time-24-hour-window"></a>提示 6：延长处理时间至超出最长运行时间（24 小时）

即使情况很简单，图像分析也是计算密集型操作，因此当图像特别大或特别复杂时，处理时间可能会超过允许的最长时间。 

最长运行时间因层而异：免费层为数分钟，收费层为 24 小时（索引编制）。 进行按需处理时，如果处理无法在 24 小时期限内完成，则可改用计划形式，让索引器在计划时间接着上次的工作继续处理。 

对于计划的索引器来说，索引编制会按计划从已知正常的最后一个文档继续开始。 使用定时计划时，索引器可以在计划的一系列时间或日期进行积压图像的处理，直至所有未处理的图像得到处理。 有关计划语法的详细信息，请参阅[步骤3：创建索引器](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer)或了解[如何为 Azure 认知搜索计划索引器](search-howto-schedule-indexers.md)。

> [!NOTE]
> 如果索引器设置为某个计划，但每次运行时在同一文档上重复失败，则索引器将以不太频繁的间隔（最多每24小时至少一次）运行，直到它成功完成进度 aga中.  如果你认为已修复了导致索引器停留在某个时间点的任何问题，则可以执行索引器的按需运行，如果成功，则索引器将再次返回到其设置的计划时间间隔。

进行基于门户的索引编制（如快速入门中所述）时，选择“运行一次”索引器选项即可将处理时间限制为 1 小时 (`"maxRunTime": "PT1H"`)。 可能需要延长处理时间至更长。

## <a name="tip-7-increase-indexing-throughput"></a>提示 7：提高索引编制吞吐量

进行[并行索引编制](search-howto-large-index.md)时，请将数据置于多个容器中，或者置于同一容器的多个虚拟文件夹中， 然后创建多个数据源和索引器对。 所有索引器可以使用同一技术集并写入同一目标搜索索引，因此你的搜索应用不需了解这种分区。
有关详细信息，请参阅[为大型数据集编制索引](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)。

## <a name="see-also"></a>另请参阅
+ [快速入门：在门户中创建 AI 扩充管道](cognitive-search-quickstart-blob.md)
+ [教程：学习 AI 扩充 REST Api](cognitive-search-tutorial-blob.md)
+ [指定数据源凭据](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [为大型数据集编制索引](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [如何将扩充字段映射到索引](cognitive-search-output-field-mapping.md)
