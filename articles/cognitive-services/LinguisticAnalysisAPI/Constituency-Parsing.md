---
title: 语言分析 API 中的成分分析 | Microsoft Docs
description: 了解成分分析（亦称为“短语结构分析”）如何识别本文中的短语。
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 03/21/2016
ms.author: lesun
ms.openlocfilehash: bff5e587621e1278c260d555aec280a0f4c7c8a1
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082166"
---
# <a name="constituency-parsing"></a>成分分析

成分分析（亦称为“短语结构分析”）旨在识别文本中的短语。
从文本中提取信息时，可能会发现此分析很有用。
客户可能要从大型文本正文中查找功能名称或关键短语，并查看每个此类短语两旁的修饰语和动作。

## <a name="phrases"></a>短语

若要成为语言学家，就不能只将短语看作是一系列字词。
一组字词必须组合在一起，并在句子中起到特定作用，才能称为短语。
这样的一组字词可以进行整体移动或替换，而句子仍应流畅且合乎语法。

假设句子如下

> I want to find a new hybrid automobile with Bluetooth.（我要找带蓝牙的新混合动力汽车。）

这个句子包含名词短语：“a new hybrid automobile with Bluetooth”（带蓝牙的新混合动力汽车）。
如何判断这是个短语？
可以将这整个短语前置，从而重写这个句子（更富有诗意）：

> A new hybrid automobile with Bluetooth I want to find.（带蓝牙的新混合动力汽车是我要找的。）

也可以将这个短语替换为功能和含义类似的短语，如“a fancy new car”（阔气的新车）：

> I want to find a fancy new car.（我要找阔气的新车。）

如果改为选取其他部分的字词，执行这些替换任务就会导致句子变得奇怪或难懂。
想想如果将“find a new”前置，会发生什么：

> Find a new I want to hybrid automobile with Bluetooth.

生成的句子难以阅读和理解。

分析程序旨在查找所有此类短语。
有趣的是，在自然语言中，短语往往嵌套在另一个短语中。
这些短语的自然表示形式就是树，如下所示：

![树](./Images/tree.png)

在此树中，标记为“NP”的分支是名词短语。
有多个此类短语：I（我）、a new hybrid automobile（新混合动力汽车）、Bluetooth（蓝牙）和 a new hybrid automobile with Bluetooth（带蓝牙的新混合动力汽车）。

## <a name="phrase-types"></a>短语类型

| 标签 | 说明 | 示例 |
|-------|-------------|---------|
|ADJP   | 形容词短语 | "so rude" |
|ADVP   | 副词短语 | "clear through" |
|CONJP  | 连接词短语 | "as well as" |
|FRAG   | 用于不完整或零碎输入的片段 | "Highly recommended..." |
|INTJ   | 感叹词 | "Hooray" |
|LST    | 列表标记（包括标点） | "#4)" |
|NAC    | 不是成分，用于界定非成分短语的范围 |  “you get things and for a good deal”中的“and for a good deal” |
|NP | 名词短语 | "a tasty potato pancake" |
|NX | 在某些复杂 NP 中用于标记头| |
|PP | 介词短语| "in the pool" |
|PRN    | 插入语| "(so called)" |
|PRT    | 小品词| “ripped out”中的“out” |
|QP | 名词短语内的数量短语（即复杂度量值/数量）| "around $75" |
|RRC    | 简化关系从句。| “many issues still unresolved”中的“still unresolved” |
|S  | 句子或从句。 | "This is a sentence."
|SBAR   | 从句（通常由从属连词引入） | “I looked around as I left.”中的“as I left”|
|SBARQ  | 由 wh-字词或 wh-短语引入的直接问句 | "What was the point?" |
|SINV   | 倒装陈述句 | "At no time were they aware." （请注意，常规主语“they”移到谓词“were”后） |
|SQ | 倒装一般疑问句或特殊疑问句主从句 | "Did they get the car?" |
|UCP    | 与协调短语不同| “small and with bugs”（请注意，形容词和介词短语通过“and”结合在一起）|
|VP | 谓词短语 | "ran into the woods" |
|WHADJP | Wh-形容词短语 | "how uncomfortable" |
|WHADVP | Wh-副词短语| "when" |
|WHNP   | Wh-名词短语| "which potato"、"how much soup"|
|WHPP   | Wh-介词短语| "in which country"|
|X  | 未知、不确定或无法归类。| “the... the soup”中的第一个“the” |


## <a name="specification"></a>规格

本文中的树使用[宾州树库](https://catalog.ldc.upenn.edu/ldc99t42)中的 S 表达式。
