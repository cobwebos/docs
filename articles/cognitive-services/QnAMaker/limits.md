---
title: 限制和边界 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 对部分知识库和服务具有元限制。 为了测试和发布，请务必将知识库保留在这些限制内。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 07/18/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b366352d19b3f1e03e32e5fbddf0cb2816fa1ba3
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320286"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>QnA Maker 知识库限制和边界

下面提供 QnA Maker 限制是[Azure 搜索定价层限制](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)和[QnA Maker 定价层限制](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)的组合。 您需要了解这两组限制, 以了解您可以为每个资源创建多少知识库以及每个知识库的增长大小。

## <a name="knowledge-bases"></a>知识库

最大知识库数基于[Azure 搜索层限制](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)。

|**Azure 搜索层** | **免费** | **基本** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|已发布知识库的最大允许数量|2|14|49|199|199|2,999|

 例如，如果你的层有 15 个允许的索引，则可以发布 14 个知识库（每个已发布的知识库有 1 个索引）。 第 15 个索引 `testkb` 用于所有知识库以进行创作和测试。 

## <a name="extraction-limits"></a>提取限制

### <a name="maximum-number-of-files"></a>最大文件数

可以提取的最大文件数和最大文件大小取决于您 QnA Maker 的 **[定价层限制](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)** 。

### <a name="maximum-number-of-deep-links-from-url"></a>URL 中的最大深层链接数

可爬网的最大深度链接数为从 URL 页中提取 Qna 的最大数目为**20**。

## <a name="metadata-limits"></a>元数据限制

每个知识库的元数据字段的最大数目取决于 **[Azure 搜索层限制](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)** 。

|**Azure 搜索层** | **免费** | **基本** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|每个 QnA Maker 服务的元数据字段的最大数量（包括所有知识库）|1,000|100*|1,000|1,000|1,000|1,000|

## <a name="knowledge-base-content-limits"></a>知识库内容限制
知识库中内容的总体限制：
* 答案文本的长度：25,000
* 问题文本的长度：1,000
* 元数据键/值文本的长度：100
* 支持的元数据名称字符：字母、数字和 _  
* 支持的元数据值字符：除 : 和 | 以外的所有字符 
* 文件名长度：200
* 支持的文件格式:：“.tsv”、“.pdf”、“.txt”、“.docx”、“.xlsx”。
* 备用问题的最大数量：300
* 问题答案对的最大数量：取决于所选的 **[Azure 搜索层限制](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)** 。 问答对是 Azure 搜索索引中的文档。 

## <a name="create-knowledge-base-call-limits"></a>创建知识库调用限制：
表示每个创建知识库操作的限制；即，单击“创建知识库”或调用 CreateKnowledgeBase API  。
* 每个答案的备用问题的最大数量：300
* 最大 URL 数：10
* 最大文件数：10

## <a name="update-knowledge-base-call-limits"></a>更新知识库调用限制
表示每个更新操作的限制；即，单击“保存并培训”或调用 UpdateKnowledgeBase API  。
* 每个源名称的长度：300
* 添加或删除的备用问题的最大数量：300
* 添加或删除的元数据字段的最大数量：10
* 可以刷新的 URL 的最大数量：5

## <a name="next-steps"></a>后续步骤

了解何时以及如何更改服务层级：

* [QnA Maker](how-to/upgrade-qnamaker-service.md#upgrade-qna-maker-management-sku):如果你需要在知识库中具有更多的源文件或更大的文档, 请在当前层外升级 QnA Maker 服务定价层。
* [应用服务](how-to/upgrade-qnamaker-service.md#upgrade-app-service):当知识库需要处理来自客户端应用的更多请求时，请升级应用服务定价层。
* [Azure 搜索](how-to/upgrade-qnamaker-service.md#upgrade-azure-search-service):当计划拥有许多知识库时，请升级 Azure 搜索服务定价层。
