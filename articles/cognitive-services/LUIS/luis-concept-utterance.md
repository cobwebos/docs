---
title: 不错的示例话语
titleSuffix: Language Understanding - Azure Cognitive Services
description: 话语是应用需要解释的用户输入。 收集你认为用户会输入的短语。 包括意思相同但在单词长度和单词位置上以不同方式构造的陈述。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: 2fd3416824189007bfdbe55d30907d9cb56f87ca
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895116"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>了解哪些良好的话语适用于你的 LUIS 应用

**陈述**是应用需要解释的用户输入。 若要训练 LUIS 从其中提取意向和实体，请务必为每个意向捕获各种不同的示例话语。 主动学习或继续针对新陈述训练的过程对于 LUIS 提供的机器学习智能至关重要。

收集你认为用户会输入的话语。 请提供含义相同但以各种不同的方式构造的话语：

* 话语长度 - 根据客户端应用程序选择短、中和长
* 单词和短语的长度 
* 单词放置 - 实体位于话语的开头、中间和末尾
* 语法 
* 复数形式
* 词干
* 名词和动词选择
* 标点 - 使用正确、不正确语法以及无语法时的多种多样的标点

## <a name="how-to-choose-varied-utterances"></a>如何选择不同的陈述

第一次开始[将示例话语 添加](luis-how-to-add-example-utterances.md)到 LUIS 模型时，请记住以下原则。

### <a name="utterances-arent-always-well-formed"></a>陈述并非始终格式正确

它可能是一个句子，比如“为我预订到巴黎的机票”，也可能是句子的片段，比如“预订”或“巴黎航班”。  用户常犯拼写错误。 在规划应用时，请考虑在将用户输入传递给 LUIS 之前是否使用[必应拼写检查](luis-tutorial-bing-spellcheck.md)进行更正。 

如果你没有对用户陈述进行拼写检查，则应该针对包含拼写错误的陈述训练 LUIS。

### <a name="use-the-representative-language-of-the-user"></a>使用用户的代表性语言

选择话语时，请注意，你认为是常用术语或短语的内容对于客户端应用程序的典型用户来说可能不正确。 他们可能没有域经验。 请谨慎使用仅当用户是专家时才会说的术语或短语。

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>选择不同的术语和措辞

你会发现，即使你努力创造不同的句型，你仍然会重复一些词汇。

以这些示例陈述为例：

|示例陈述|
|--|
|如何买计算机？|
|在哪里买计算机？|
|我想要一台计算机，我该怎么做？|
|我什么时候能有一台计算机？| 

这里的核心术语“计算机”没有变化。 可以使用替代话语“台式电脑”、“笔记本电脑”、“工作站”，甚至是“机器”。 LUIS 会智能地从上下文中推断同义词，但当你为训练创建陈述时，最好还是改变它们。

## <a name="example-utterances-in-each-intent"></a>每个意向的示例陈述

每个意向都需要有示例话语，至少 15 个。 如果你的意向没有任何示例陈述，则将无法训练 LUIS。 如果你的意向只有一个或非常少的示例陈述，则 LUIS 将无法准确预测意向。 

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>为每个创作迭代添加由 15 个话语构成的小组

在模型的每个迭代中，不要添加大量陈述。 添加数量为 15 的话语。 再次[训练](luis-how-to-train.md)、[发布](luis-how-to-publish-app.md)和[测试](luis-interactive-test.md)。  

LUIS 使用由 LUIS 模型作者精心挑选的话语构建有效的模型。 添加太多话语是没有价值的，因为它会引起混乱。  

最好先从几个陈述开始，然后[审查终结点陈述](luis-how-to-review-endpoint-utterances.md)以进行正确的意向预测和实体提取。

## <a name="punctuation-marks"></a>标点符号

默认情况下，LUIS 不会忽略标点符号，因为某些客户端应用程序可能会对这些标记赋予含义。 确保示例话语使用“标点”和“无标点”，以便两种样式都返回相同的相对分数。 如果标点在你的客户端应用程序中没有特定含义，请考虑使用模式[忽略标点](#ignoring-words-and-punctuation)。 

## <a name="ignoring-words-and-punctuation"></a>忽略单词和标点

如果想要忽略示例陈述中的特定单词或标点符号，请在 _ignore_ 语法中使用[模式](luis-concept-patterns.md#pattern-syntax)。 

## <a name="training-utterances"></a>训练陈述

训练通常是非确定性的：在不同版本或应用中，陈述预测可能略有不同。 可以通过使用 `UseAllTrainingData` 名称/值对更新[版本设置](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) API 来使用所有训练数据。

## <a name="testing-utterances"></a>测试陈述 

开发人员应通过向[预测终结点](luis-how-to-azure-subscription.md) URL 发送话语来开始使用实际流量测试其 LUIS 应用程序。 这些陈述用于通过[审查陈述](luis-how-to-review-endpoint-utterances.md)来改善意向和实体的表现。 使用 LUIS 网站测试窗格提交的测试不会通过终结点发送，因此不会对主动学习有所帮助。 

## <a name="review-utterances"></a>审查陈述

在模型经过训练、发布并接收[终结点](luis-glossary.md#endpoint)查询后，请[审查 LUIS 建议的陈述](luis-how-to-review-endpoint-utterances.md)。 LUIS 会选择意向或实体得分较低的终结点陈述。 

## <a name="best-practices"></a>最佳做法

查看[最佳做法](luis-concept-best-practices.md)并将其应用为常规创作周期的一部分。

## <a name="next-steps"></a>后续步骤
有关定型 LUIS 应用以理解用户话语的信息，请参阅[添加示例话语](luis-how-to-add-example-utterances.md)。

