---
title: QnA Maker 限制 - Azure 认知服务 | Microsoft Docs
description: QnA Maker 限制
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 93471faab9aac94616c770cbee21fb0364f73639
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39501854"
---
# <a name="qna-maker-limits"></a>QnA Maker 限制
QnA Maker 限制的完整列表。

## <a name="knowledge-bases"></a>知识库

* 基于 [Azure 搜索层限制](https://docs.microsoft.com/en-us/azure/search/search-limits-quotas-capacity)的知识库最大数量

|**Azure 搜索层** | **免费** | **基本** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|已发布知识库的最大允许数量（最大索引 - 1（为测试保留））|2|14|49|199|199|2999|

## <a name="extraction-limits"></a>提取限制
* 可提取文件的最大数量和最大文件大小：请参阅 [QnAMaker 定价](https://azure.microsoft.com/en-in/pricing/details/cognitive-services/qna-maker/)
* 从 FAQ HTML 页面提取 QnA 时可抓取的深层链接的最大数量：20

## <a name="metadata-limits"></a>元数据限制
* 根据 [Azure 搜索层限制](https://docs.microsoft.com/en-us/azure/search/search-limits-quotas-capacity)，每个知识库的元数据字段的最大数量

|**Azure 搜索层** | **免费** | **基本** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|每个 QnA Maker 服务的元数据字段的最大数量（包括所有知识库）|1000|100*|1000|1000|1000|1000|

## <a name="knowledge-base-content-limits"></a>知识库内容限制
知识库中内容的总体限制：
* 答案文本的长度：250000
* 问题文本的长度：1000
* 元数据键/值文本的长度：100
* 支持的元数据名称字符：字母、数字和 _  
* 支持的元数据值字符：除 : 和 | 以外的所有字符 
* 文件名长度：200
* 支持的文件格式:：“.tsv”、“.pdf”、“.txt”、“.docx”、“.xlsx”。
* 备用问题的最大数量：100
* 问-答对的最大数量：取决于所选的 [Azure 搜索层](https://docs.microsoft.com/en-in/azure/search/search-limits-quotas-capacity#document-limits) 

## <a name="create-knowledge-base-call-limits"></a>创建知识库调用限制：
表示每个创建知识库操作的限制；即，单击“创建知识库”或调用 CreateKnowledgeBase API。
* 每个答案的备用问题的最大数量：100
* 最大 URL 数：10
* 最大文件数：10

## <a name="update-knowledge-base-call-limits"></a>更新知识库调用限制
表示每个更新操作的限制；即，单击“保存并培训”或调用 UpdateKnowledgeBase API。
* 每个源名称的长度：300
* 添加或删除的备用问题的最大数量：100
* 添加或删除的元数据字段的最大数量：10
* 可以刷新的 URL 的最大数量：5
