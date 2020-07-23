---
title: 人工智能
description: LUIS 使用人工智能根据你定义的架构为你的数据提供语言理解。
ms.topic: conceptual
ms.date: 06/29/2020
ms.openlocfilehash: 4f145585e097a3cf6a2338dbab879f6fce07f71d
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2020
ms.locfileid: "85802646"
---
# <a name="artificial-intelligence-in-language-understanding-luis"></a>语言理解中的人工智能（LUIS）

LUIS 使用人工智能根据你定义的架构为你的数据提供自然语言理解（NLU）。

## <a name="natural-language-processing"></a>自然语言处理

自然语言理解（NLU）是自然语言处理（NLP）的特定子主题。

自然语言处理是一种更广泛的概念，用于处理文本数据的任何形式，这包括：

* 化
* 语音（pos）标记的一部分
* 分段
* 语形学分析
* 语义相似性
* Discourse
* 翻译

## <a name="natural-language-processing-in-luis"></a>LUIS 中的自然语言处理

可以通过以下方式在 LUIS 应用程序中使用自然语言处理：
* [自然语言理解](#natural-language-understanding)（LUIS）
* LUIS 中可配置的 NLP 方面：
    * [词汇切分](luis-language-support.md#tokenization)
    * 通过音调符号、标点和 word 窗体[API 设置](luis-reference-application-settings.md)Morphology
* 预处理或后处理其他[认知服务](../Welcome.md)提供的查询文本查询：
    * [翻译](../translator/translator-info-overview.md)

## <a name="natural-language-understanding"></a>自然语言理解

NLU 是一种将语言语句_转换_为表示形式的功能，它使你可以自然地了解你的用户。 自然语言理解仍是一个非常难的问题，并定义为_AI-硬_问题。

LUIS 旨在重点介绍意向和提取，这包括能够确定：
* 用户需要的内容
* 它们所谈论的内容。

LUIS 对更广泛的_NLP_方面（如语义相似性）几乎不知道，无需显式标识。 例如，以下标记（单词）是三个不同的内容，直到在提供的示例中将它们用于类似的上下文中：

* buy
* 用途
* 购买

## <a name="next-steps"></a>后续步骤

* 了解 LUIS 应用的[开发生命周期](luis-concept-app-iteration.md)