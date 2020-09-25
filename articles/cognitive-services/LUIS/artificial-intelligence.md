---
title: 人工智能 (AI)
description: LUIS 根据你定义的架构，使用人工智能 (AI) 为数据提供语言理解。
ms.topic: conceptual
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 08/07/2020
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 2586bf2d31ef28c662ad88bde3718541bb21a31c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327198"
---
# <a name="artificial-intelligence-in-language-understanding-luis"></a>语言理解 (LUIS) 中的人工智能

LUIS 使用人工智能 (AI) 根据你定义的架构，为你的数据提供自然语言理解 (NLU) 。

## <a name="natural-language-processing-nlp"></a>自然语言处理 (NLP) 

自然语言理解 (NLU) 是自然语言处理 (NLP) 的特定子主题。

自然语言处理是一个更广泛的概念，可以用于任何形式的文本数据处理，其中包括：

* 词汇切分
* 词性 (pos) 标记
* 分段
* 语形学分析
* 语义相似性
* 公开 (Discourse)
* 翻译

## <a name="natural-language-processing-in-luis"></a>LUIS 中的自然语言处理

可以通过以下方式在 LUIS 应用中使用自然语言处理：
* [自然语言理解](#natural-language-processing-nlp) (LUIS)
* LUIS 中可配置的 NLP 特性：
    * [词汇切分](luis-language-support.md#tokenization)
    * 通过音调符号、标点和单词形式 [API 设置](luis-reference-application-settings.md)配置词态
* 其他[认知服务](../Welcome.md)提供的查询言语的预处理或后期处理，例如：
    * [翻译](../translator/translator-info-overview.md)

## <a name="natural-language-understanding-nlu"></a>自然语言理解 (NLU)

NLU 功能可用于将口语语句转换为可让你自然地理解用户意思的表示形式。 自然语言理解仍是一个极具挑战性的问题，被定义为一个 AI 难题。

LUIS 旨在关注意图和提取，这包括能够识别：
* 用户需求
* 用户谈论的内容。

在示例中没有明确标识的情况下，LUIS 几乎或完全不了解更广泛的 NLP 特性（例如语义相似性）。 例如，在所提供的示例中将以下标记（单词）用于相似的上下文中之前，它们是三个不同的内容：

* buy
* buying
* bought

## <a name="next-steps"></a>后续步骤

* 了解 LUIS 应用的[开发生命周期](luis-concept-app-iteration.md)