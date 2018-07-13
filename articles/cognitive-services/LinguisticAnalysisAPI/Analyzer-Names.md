---
title: 语言分析 API 中的分析器命名结构 | Microsoft Docs
description: 了解语言分析 API 如何使用分析器命名结构来实现灵活性和精确性。
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 03/23/2016
ms.author: lesun
ms.openlocfilehash: 2729b7126e82862660fc8e1a995cc87ae996ea03
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365457"
---
# <a name="analyzer-names"></a>分析器名称

我们使用有点复杂的分析器命名结构，以实现分析器灵活性和理解名称含义的精确性。
分析器名称由四部分组成：ID、种类、规范和实现。
每个组成部分的作用定义如下。

## <a name="id"></a>ID
首先，分析器有唯一 ID，即 GUID。
这些 GUID 应该极少变化，但是唯一描述特定分析器的独一无二方式。

## <a name="kind"></a>种类
接下来，每个分析器都是一个种类。
这非常宽泛地定义了返回的分析类型，应唯一定义用于表示此类分析的数据结构。
目前，有三种不同种类：
 - [令牌](Sentences-and-Tokens.md)
 - [POS 标记](Pos-Tagging.md)
 - [成分树](constituency-parsing.md)

## <a name="specification"></a>规格
然而，在特定种类中，各个专家可能会对应如何分析特定现象持有不同看法。
与编程语言不同，对于应如何完成此操作，没有准确清晰的定义。

例如，假设要在英语句子“He didn't go”（他没有走）中查找词汇。
特别是，想想看字符串“didn't”（没有）。
一种可能解释是，应将它切分成两个词汇：“did”和“not”。
然后，可选句子“He did not go”（他没有走）就有一组相同的词汇。
另一种可能解释是，应将它切分成词汇“did”和“n't”。
后一个词汇通常不被视为字词，但这种方法保留了表层字符串的更多信息，有时可能会很有用。
或许应将这种缩写形式视为一个字词。

无论选择哪种方法，都应确保一致性。
这正是规范的作用所在：决定正确的表示形式应该是什么。

分析器输出只能与符合相同规范的数据进行相当比较。

## <a name="implementation"></a>实现

通常有多个模型试图获得相同结果，但它们的性能特征却不同。
一种模型可能会更快，但不太准确；另一种模型可能会做出不同的取舍。

分析器名称的实现部分用于标识此类信息，方便用户能够选取最能满足自己需求的分析器。
