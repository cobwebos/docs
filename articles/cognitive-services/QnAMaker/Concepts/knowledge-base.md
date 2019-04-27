---
title: 知识库 ID - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 知识库包含一组问题/答案 (QnA) 对和与每个 QnA 对关联的可选元数据。
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/04/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 02111ac90fe97ddaddbd41ad42410e7e76f1c405
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61379221"
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

元数据字段包含由冒号分隔的键值对 **(Product:Shredder)**。 键和值都必须是纯文本。 元数据密钥不得包含任何空格。 元数据支持每个密钥只有一个值。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [知识库的开发生命周期](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>另请参阅

[QnA Maker 概述](../Overview/overview.md)
