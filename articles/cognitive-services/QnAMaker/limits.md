---
title: 限制和边界 - QnA Maker
description: QnA Maker 对部分知识库和服务具有元限制。 为了测试和发布，请务必将知识库保留在这些限制内。
ms.topic: reference
ms.date: 02/14/2020
ms.openlocfilehash: 6375a6c6efc0c7016d9947e04e9479385aa80af5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273339"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>QnA Maker 知识库限制和边界

下面提供的 QnA Maker 限制是[Azure 认知搜索定价层限制](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)和[QnA 制造商定价层限制](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)的组合。 您需要了解两组限制，以了解每个资源可以创建多少知识库，以及每个知识库可以增长多大。

## <a name="knowledge-bases"></a>知识库

知识库的最大数量基于[Azure 认知搜索层限制](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)。

|**Azure 认知搜索层** | **自由** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|已发布知识库的最大允许数量|2|14|49|199|199|2,999|

 例如，如果你的层有 15 个允许的索引，则可以发布 14 个知识库（每个已发布的知识库有 1 个索引）。 第 15 个索引 `testkb` 用于所有知识库以进行创作和测试。

## <a name="extraction-limits"></a>提取限制

### <a name="file-naming-constraints"></a>文件命名约束

文件名不能包括以下字符：

|不要使用字符|
|--|
|单报价`'`|
|双报价`"`|

### <a name="maximum-file-size"></a>文件大小上限

|格式|最大文件大小 （MB）|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>文件的最大数量

可以提取的文件的最大数量和最大文件大小基于**[QnA Maker 定价层限制](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)**。

### <a name="maximum-number-of-deep-links-from-url"></a>来自 URL 的深层链接的最大数量

可以从 URL 页提取 QNA 可爬网的最大深度链接数为**20**。

## <a name="metadata-limits"></a>元数据限制

元数据以基于文本的键：值对的形式显示，如`product:windows 10`。 它被存储和比较在小写。

### <a name="by-azure-cognitive-search-pricing-tier"></a>按 Azure 认知搜索定价层

每个知识库的最大元数据字段数基于**[Azure 认知搜索层限制](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)**。

|**Azure 认知搜索层** | **自由** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|每个 QnA Maker 服务的元数据字段的最大数量（包括所有知识库）|1,000|100*|1,000|1,000|1,000|1,000|

### <a name="by-name-and-value"></a>按名称和值

下表列出了元数据名称和值的长度和可接受字符。

|Item|允许的字符|雷格斯模式匹配|最大字符|
|--|--|--|--|
|名称（键）|允许<br>字母数字（字母和数字）<br>`_`（下划线）<br> 不能包含空格。|`^[a-zA-Z0-9_]+$`|100|
|“值”|允许除<br>`:`（结肠）<br>`|`（垂直管道）<br>只允许一个值。|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>知识库内容限制
知识库中内容的总体限制：
* 答案文本的长度：25,000
* 问题文本的长度：1,000
* 元数据键/值文本的长度：100
* 元数据名称的支持字符：字母、数字和`_`
* 元数据值的支持字符：除`:`和`|`
* 文件名长度：200
* 支持的文件格式:：“.tsv”、“.pdf”、“.txt”、“.docx”、“.xlsx”。
* 备用问题的最大数量： 300
* 答案对的最大数量：取决于所选的 Azure**[认知搜索层](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)**。 问答对映射到 Azure 认知搜索索引上的文档。
* URL/HTML 页面：100 万个字符

## <a name="create-knowledge-base-call-limits"></a>创建知识库调用限制：
表示每个创建知识库操作的限制；即，单击“创建知识库”或调用 CreateKnowledgeBase API**。
* 每个答案的备用问题的最大数量： 300
* 最大 URL 数：10
* 最大文件数：10

## <a name="update-knowledge-base-call-limits"></a>更新知识库调用限制
表示每个更新操作的限制；即，单击“保存并培训”或调用 UpdateKnowledgeBase API**。
* 每个源名称的长度：300
* 添加或删除的备用问题的最大数量：300
* 添加或删除的元数据字段的最大数量：10
* 可以刷新的 URL 的最大数量：5

## <a name="next-steps"></a>后续步骤

了解何时以及如何更改[服务定价层](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku)。
