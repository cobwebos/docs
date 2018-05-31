---
title: Azure 搜索中认知搜索的故障排除提示 | Microsoft Docs
description: 在 Azure 搜索中设置认知搜索管道的提示和故障排除。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 15fc879958bfd886210a90239e0247c60fe231f9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786826"
---
# <a name="troubleshooting-tips-for-cognitive-search"></a>认知搜索故障排除提示

本文包含一系列提示和窍门，帮助你完成 Azure 搜索中认知搜索功能的入门。 

仔细阅读[教程：如何调用认知搜索 API](cognitive-search-quickstart-blob.md)，练习如何将认知搜索扩充项应用到 Blob 数据源（如果尚未这样做）。

## <a name="tip-1-start-with-a-small-dataset"></a>提示 1：从小数据集开始
若要快速查找问题，最好的方式是提高解决问题的速度。 若要缩短索引时间，最好的方式是减少要索引的文档数。 

一开始只使用少量文档/记录创建数据源。 文档示例应该能够很好地代表要进行索引的文档的多样性。 

通过端到端管道运行文档示例，检查结果是否符合自己的需求。 对结果满意以后，即可向数据源添加更多文件。

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>提示 2：确保数据源凭据正确
在定义一个使用数据源连接的索引器之前，数据源连接并未完成验证。 如果看到任何错误提及索引器无法访问数据，请确保：
- 连接字符串是正确的。 确保使用 Azure 搜索预期的格式，尤其是在创建 SAS 令牌的时候。 若要了解受支持的不同格式，请参阅[如何指定凭据部分](
https://docs.microsoft.com/en-us/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials)。
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

如果问题与文件大小相关，则可能会看到这样的错误：“Blob <file-name> 的大小为 <file-size> 字节，这超出了当前服务层的文档提取的最大大小。” 有关索引器限制的详细信息，请参阅[服务限制](search-limits-quotas-capacity.md)。

内容没有出现的另一原因可能与输入/输出映射错误相关。 例如，输出目标名称为“People”，但索引字段名称为“people”（小写）。 系统可能会针对整个管道返回“201 成功”消息，因此你认为索引编制成功，但实际上有一个字段是空的。 

## <a name="tip-6-extend-processing-beyond-maximum-run-time-24-hour-window"></a>提示 6：延长处理时间至超出最长运行时间（24 小时）

即使情况很简单，图像分析也是计算密集型操作，因此当图像特别大或特别复杂时，处理时间可能会超过允许的最长时间。 

最长运行时间因层而异：免费层为数分钟，收费层为 24 小时（索引编制）。 进行按需处理时，如果处理无法在 24 小时期限内完成，则可改用计划形式，让索引器在计划时间接着上次的工作继续处理。 

对于计划的索引器来说，索引编制会按计划从已知正常的最后一个文档继续开始。 使用定时计划时，索引器可以在计划的一系列时间或日期进行积压图像的处理，直至所有未处理的图像得到处理。 有关计划语法的详细信息，请参阅[步骤 3：创建索引器](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer)。

进行基于门户的索引编制（如快速入门中所述）时，选择“运行一次”索引器选项即可将处理时间限制为 1 小时 (`"maxRunTime": "PT1H"`)。 可能需要延长处理时间至更长。

## <a name="tip-7-increase-indexing-throughput"></a>提示 7：提高索引编制吞吐量

进行[并行索引编制](search-howto-reindex.md#parallel-indexing)时，请将数据置于多个容器中，或者置于同一容器的多个虚拟文件夹中， 然后创建多个数据源和索引器对。 所有索引器可以使用同一技术集并写入同一目标搜索索引，因此你的搜索应用不需了解这种分区。
有关详细信息，请参阅[为大型数据集编制索引](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)。

## <a name="see-also"></a>另请参阅
+ [快速入门：在门户中创建认知搜索管道](cognitive-search-quickstart-blob.md)
+ [教程：了解认知搜索 REST API](cognitive-search-tutorial-blob.md)
+ [指定数据源凭据](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [为大型数据集编制索引](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)
+ [如何定义技术集](cognitive-search-defining-skillset.md)
+ [如何将扩充字段映射到索引](cognitive-search-output-field-mapping.md)