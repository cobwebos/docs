---
title: 知识库 ID - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 知识库包含一组问题/答案 (QnA) 对和与每个 QnA 对关联的可选元数据。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 022b16669791b9b9cce066b3dd17c70b33569cc0
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955237"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>QnA Maker 知识库是什么？

QnA Maker 知识库包含一组问题/答案 (QnA) 对和与每个 QnA 对关联的可选元数据。

## <a name="key-knowledge-base-concepts"></a>重要知识库概念

* **问题** - 问题包含最能代表用户查询的文本。 
* **答案** - 答案是当用户查询与关联的问题匹配时返回的响应。  
* **元数据** - 元数据是与 QnA 对关联的标记，以键值对形式表示。 元数据标记用于筛选 QnA 对并限制对其执行查询匹配的集。

单个 QnA（由数字 QnA ID 表示）包含一个问题的多个变体（备用问题），这些变体全部映射到一个回答。 此外, 每个此类对都可以有多个与之关联的元数据字段: 一个键和一个值。

![QnA Maker 知识库](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>知识库内容格式

向某一知识库引入丰富的内容时，QnA Maker 会尝试将内容转换为 markdown。 阅读[此](https://aka.ms/qnamaker-docs-markdown-support)博客以了解大多数聊天客户端可理解的 markdown 格式。

元数据字段包含由冒号分隔的键值对 **(Product:Shredder)** 。 键和值都必须是纯文本。 元数据密钥不得包含任何空格。 元数据仅支持每个键一个值。

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>QnA Maker 如何处理用户查询以选择最佳答案

训练和[发布](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base)的 QnA Maker 知识库通过[GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)从机器人或其他客户端应用程序接收用户查询。 下图说明了收到用户查询时的过程。

![用户查询的排名过程](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

下表对此过程进行了说明:

|步骤|用途|
|--|--|
|1|客户端应用程序将用户查询发送到[GENERATEANSWER API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)。|
|2|Qna Maker 通过语言检测、spellers 和断字符来预处理用户查询。|
|3|此预处理用于更改用户查询以获得最佳搜索结果。|
|4|此更改的查询将发送到 Azure 搜索索引, 并`top`收到结果数。 如果这些结果中没有正确答案, 请将值`top`略微增大。 通常, 90% 的查询`top`中的值为10。|
|5|QnA Maker 会应用高级特征化, 以确定获取用户查询的 Azure 搜索结果的正确性。 |
|6|训练的 ranker 模型使用步骤5中的功能分数对 Azure 搜索结果进行排序。|
|7|新结果将按顺序返回到客户端应用程序。|
|||

使用的功能包括但不限于 word 级语义、语料库中的术语级别重要性, 以及深入了解的语义模型, 以确定两个文本字符串之间的相似性和相关性。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [知识库的开发生命周期](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>请参阅

[QnA Maker 概述](../Overview/overview.md)
