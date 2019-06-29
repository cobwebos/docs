---
title: 知识库 ID - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 知识库包含一组问题/答案 (QnA) 对和与每个 QnA 对关联的可选元数据。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b9562a1686c4de4f4e2ef57a7d91bbf18dce63ef
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447598"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>QnA Maker 知识库是什么？

QnA Maker 知识库包含一组问题/答案 (QnA) 对和与每个 QnA 对关联的可选元数据。

## <a name="key-knowledge-base-concepts"></a>重要知识库概念

* **问题** - 问题包含最能代表用户查询的文本。 
* **答案** - 答案是当用户查询与关联的问题匹配时返回的响应。  
* **元数据** - 元数据是与 QnA 对关联的标记，以键值对形式表示。 元数据标记用于筛选 QnA 对并限制对其执行查询匹配的集。

单个 QnA（由数字 QnA ID 表示）包含一个问题的多个变体（备用问题），这些变体全部映射到一个回答。 此外，每个此类对可以有多个与之关联的元数据字段： 一个键和一个值。

![QnA Maker 知识库](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>知识库内容格式

向某一知识库引入丰富的内容时，QnA Maker 会尝试将内容转换为 markdown。 阅读[此](https://aka.ms/qnamaker-docs-markdown-support)博客以了解大多数聊天客户端可理解的 markdown 格式。

元数据字段包含由冒号分隔的键值对 **(Product:Shredder)** 。 键和值都必须是纯文本。 元数据密钥不得包含任何空格。 元数据支持每个密钥只有一个值。

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>QnA Maker 如何处理用户查询选择最佳答案

训练并[发布](/quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)QnA Maker 知识库接收用户查询，从智能机器人应用程序或其他客户端应用程序中，在[GenerateAnswer API](/how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api)。 下图说明过程时收到用户查询。

![用户查询排名进程](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

下表说明了该过程：

|步骤|目的|
|--|--|
|第|客户端应用程序将发送到用户查询[GenerateAnswer API](/how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api)。|
|2|Qna Maker 预处理用户查询语言检测、 spellers，与断字符。|
|3|采取此预处理来更改用户为了获得最佳的搜索结果的查询。|
|4|此更改后的查询发送到 Azure 搜索索引，接收`top`结果数。 如果在这些结果不正确的答案，增加的价值`top`略有。 通常为 10 个值`top`90%的查询中的工作。|
|5|QnA Maker 应用高级特征化来确定用户查询提取 Azure 搜索结果的正确性。 |
|6|经过训练的排名器模型使用特征评分，从步骤 5： 对 Azure 搜索结果进行排名。|
|7|按照排名高低情况下，将新的结果返回到客户端应用程序。|
|||

使用功能，包括但不限于 word 级别语义的词级别中语料库和深度学习的语义模型来确定相似性和两个文本字符串之间的相关性的重要性。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [知识库的开发生命周期](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>另请参阅

[QnA Maker 概述](../Overview/overview.md)
