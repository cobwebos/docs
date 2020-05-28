---
title: 什么是 BLEU 分数？ - 自定义翻译
titleSuffix: Azure Cognitive Services
description: BLEU 是对计算机翻译和相同源句子的人工创建引用翻译之间的差异的度量。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: b35dce5a2b572e5a3f11601d5e67efb2da02cdec
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83997054"
---
# <a name="what-is-a-bleu-score"></a>什么是 BLEU 分数？

[BLEU（双语评估候补）](https://en.wikipedia.org/wiki/BLEU)用于度量同一源语句的自动翻译与一个或多个人工创建的参考翻译之间的差异。

## <a name="scoring-process"></a>评分过程

BLEU 算法将自动翻译的连续短语与它在参考翻译中找到的连续短语进行比较，并以加权方式对匹配项数进行计数。 这些匹配项与位置无关。 匹配度越高表示与参考翻译的相似度越高，分数也越高。 不会考虑可理解性和语法正确性。

## <a name="how-bleu-works"></a>BLEU 如何工作？

BLEU 的优点是，它通过对测试语料库的单个句子判断错误进行求和，而不是尝试为每个句子设计确切的人工判断，从而与人工判断非常关联。

[此处](https://youtu.be/-UqDljMymMg)更详细地讨论了 BLEU 分数。

BLEU 结果在很大程度上取决于你的域的范围、测试数据与训练和优化数据之间的一致性，以及可用于训练的数据量。 如果模型是基于范围很小的域训练的，并且训练数据与测试数据一致，则可以预期得到较高的 BLEU 分数。

>[!NOTE]
>只有使用相同的测试集、相同的语言对和相同的 MT 引擎比较 BLEU 结果时，BLEU 分数之间的比较才有意义。 使用的测试集不同，BLEU 分数也必定不同。
