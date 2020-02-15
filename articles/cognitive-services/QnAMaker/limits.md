---
title: 限制和边界 - QnA Maker
description: QnA Maker 对部分知识库和服务具有元限制。 为了测试和发布，请务必将知识库保留在这些限制内。
ms.topic: article
ms.date: 02/14/2020
ms.openlocfilehash: 7fdf45a4a22f6d9ffe123f5998592739402be55f
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77252002"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>QnA Maker 知识库限制和边界

下面提供 QnA Maker 限制是[Azure 认知搜索定价层限制](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)和[QnA Maker 定价层限制](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)的组合。 您需要了解这两组限制，以了解您可以为每个资源创建多少知识库以及每个知识库的增长大小。

## <a name="knowledge-bases"></a>知识库

最大知识库数基于[Azure 认知搜索层限制](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)。

|**Azure 认知搜索层** | **免费** | **基本** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|已发布知识库的最大允许数量|2|14|49|199|199|2,999|

 例如，如果你的层有 15 个允许的索引，则可以发布 14 个知识库（每个已发布的知识库有 1 个索引）。 第 15 个索引 `testkb` 用于所有知识库以进行创作和测试。

## <a name="extraction-limits"></a>提取限制

### <a name="file-naming-constraints"></a>文件命名约束

文件名不能包含以下字符：

|不使用字符|
|--|
|单引号 `'`|
|双引号 `"`|

### <a name="maximum-file-size"></a>文件大小上限

|格式|最大文件大小（MB）|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>最大文件数

可以提取的最大文件数和最大文件大小取决于您 QnA Maker 的 **[定价层限制](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)** 。

### <a name="maximum-number-of-deep-links-from-url"></a>URL 中的最大深层链接数

可爬网的最大深度链接数为从 URL 页中提取 Qna 的最大数目为**20**。

## <a name="metadata-limits"></a>元数据限制

存储元数据，并以小写形式进行比较。

### <a name="by-azure-cognitive-search-pricing-tier"></a>由 Azure 认知搜索定价层

每个知识库的元数据字段的最大数目取决于 **[Azure 认知搜索层限制](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)** 。

|**Azure 认知搜索层** | **免费** | **基本** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|每个 QnA Maker 服务的元数据字段的最大数量（包括所有知识库）|1,000|100*|1,000|1,000|1,000|1,000|

### <a name="by-name-and-value"></a>按名称和值

下表列出了元数据名称和值可接受的长度和可接受的字符。

|Item|允许的字符|Regex 模式匹配|最大字符数|
|--|--|--|--|
|名称|可用<br>字母数字（字母和数字）<br>`_` （下划线）|`^[a-zA-Z0-9_]+$`|100|
|值|允许除以外的所有内容<br>`:` （冒号）<br>`|` （垂直管道）|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>知识库内容限制
知识库中内容的总体限制：
* 答案文本的长度：25,000
* 问题文本的长度：1,000
* 元数据键/值文本的长度：100
* 支持的元数据名称字符：字母、数字和 `_`
* 元数据值支持的字符：除 `:` 和 `|` 之外的所有字符
* 文件名长度：200
* 支持的文件格式:：“.tsv”、“.pdf”、“.txt”、“.docx”、“.xlsx”。
* 最大替代问题数：300
* 问题答案对的最大数目：取决于所选的 **[Azure 认知搜索层](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)** 。 问题和答案对映射到 Azure 认知搜索索引上的文档。
* URL/HTML 页面：1000000个字符

## <a name="create-knowledge-base-call-limits"></a>创建知识库调用限制：
表示每个创建知识库操作的限制；即，单击“创建知识库”或调用 CreateKnowledgeBase API。
* 每个回答的最大替代问题数：300
* 最大 URL 数：10
* 最大文件数：10

## <a name="update-knowledge-base-call-limits"></a>更新知识库调用限制
表示每个更新操作的限制；即，单击“保存并培训”或调用 UpdateKnowledgeBase API。
* 每个源名称的长度：300
* 添加或删除的最大替代问题数：300
* 添加或删除的元数据字段的最大数量：10
* 可以刷新的 URL 的最大数量：5

## <a name="next-steps"></a>后续步骤

了解何时以及如何更改[服务定价层](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker)。
