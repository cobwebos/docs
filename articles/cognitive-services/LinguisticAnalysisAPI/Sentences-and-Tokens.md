---
title: 语言分析 API 中的句子和词汇 | Microsoft Docs
description: 了解认知服务中的语言分析 API 支持的句子拆分和词汇切分。
services: cognitive-services
author: DavidLiCIG
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 03/21/2016
ms.author: davl
ms.openlocfilehash: 78e539f365728ad540308e9cfb07af44bf6d8fe7
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2018
ms.locfileid: "37084036"
---
# <a name="sentence-separation-and-tokenization"></a>句子拆分和词汇切分

## <a name="background-and-motivation"></a>背景和动机

对于给定文本正文，语言分析的第一步是，将文本分解为句子和词汇。

### <a name="sentence-separation"></a>句子拆分

初看上去，将文本拆分为句子似乎很简单：只需找到句尾标点，即可拆分出句子。
不过，这些标点通常既复杂又不明确。

假设示例文本如下：

> What did you say?!?（你说什么？！？） I didn't hear about the director's "new proposal."（我没听说过主管的“新提案”。） It's important to Mr. and Mrs. Smith.（它对史密斯夫妇很重要。）

这段文本包含下面三个句子：

- What did you say?!?（你说什么？！？）
- I didn't hear about the director's "new proposal."（我没听说过主管的“新提案”。）
- It's important to Mr. and Mrs. Smith.（它对史密斯夫妇很重要。）

请注意，句尾标点截然不同。
第一个句子以问号和感叹号（有时称为“问叹号”）结尾。
第二个句子以句点或句号结尾，而后跟的引号则应分入上一句。
在第三个句子中，可以看到相同的句点符号还用于标记缩写。
虽然仅参考标点可以提供理想的候选集，但还需要进一步工作，才能真正确定句子边界。

### <a name="tokenization"></a>词汇切分

下一项任务是将这些句子切分为词汇。
大多数情况下，英语词汇是用空格隔开。
（英语词汇或字词查找起来比中文词汇或字词更简单，因为中文大多不使用空格分隔字词。
第一个句子可能写为“Whatdidyousay?”）

有几种棘手情况。
首先，标点通常（但不一定）应从周围上下文中切分出来。
其次，英语有缩写形式（例如“didn't”或“it's”），其中字词已压缩并缩写为更短形式。 tokenizer 的目标是，将字符序列切分为字词。

回到上面的示例句子。
现已在每个不同词汇之间插入“中心点”(&middot;)。

- What &middot; did &middot; you &middot; say &middot; ?!?
- I &middot; did &middot; n't &middot; hear &middot; about &middot; the &middot; director &middot; 's &middot; " &middot; new &middot; proposal &middot; . &middot; "
- It &middot; 's &middot; important &middot; to &middot; Mr. &middot; and &middot; Mrs.&middot; Smith &middot; .

请注意，大部分词汇都能在字典中查到（例如，important、director）。
另一些词汇只包含标点。
最后，还有一些更不常见的词汇表示缩写形式。例如，n't 表示 not，'s 表示所有格等。借助这种词汇切分，可以更一致地处理字词（例如 didn't）和短语（例如 did not）。

## <a name="specification"></a>规格

对于句子和词汇的组成成分，请务必确保所作决定一致。
我们依赖于 [Penn Treebank](https://catalog.ldc.upenn.edu/ldc99t42) 的规范（一些其他详细信息可以在 ftp://ftp.cis.upenn.edu/pub/treebank/public_html/tokenization.html 上找到）。
